# 部署具有外部数据库的高可用 K3s

## 介绍

本文了介绍如何以高可用模式部署 Kubernetes 并配置远程备份数据库。我将介绍怎样在具有外部数据库 Postgres 的 HA 配置中部署 K3s。[K3s](https://k3s.io/) 是用于物联网和边缘计算的认证 Kubernetes 发行版。我将它部署在 IBM Z 大型机中的虚拟机上。我没有选择 etcd，而是使用了 Postgres 作为 K3s 集群的存储方案。我以非 HA 模式部署了 Postgres。有关如何在 HA 模式下部署 Postgres 的信息，请参阅他们的[官方文档](https://www.postgresql.org/docs/13/high-availability.html)。Postgres 和 K3s 集群之间的通信是 SSL 安全的。我在 K3s server 前面的第 4 层使用了 Nginx 进行负载均衡。我们以后可能需要添加新的服务器节点或关闭服务器。因此，如果不使用 K3s Server IP 而是使用负载均衡器，我们就能避免这类麻烦，并且还可以实现 HA。请注意，Nginx 负载均衡器也可能成为单一故障点。有关在 HA 模式下部署 Nginx 的信息，请参阅[文档](https://www.nginx.com/products/nginx/high-availability/)。

## HA 配置

下图描述了我在 HA 模式下部署具有两个节点的 K3s 集群的方式。即使一台 Server 出现故障，另一台 Server 仍然可以访问，而且负载均衡器能将所有请求路由到可访问的 Server。由于 K3s 的所有组件都是无状态的，即使 Server 出现故障，我们也不会丢失任何信息。所有信息都存储在 Postgres 中，并且必须是 HA。

![](https://raw.githubusercontent.com/rohitsakala/blog/main/K3S-HA.png)
具有负载均衡器和数据库的 K3s HA 模式

## 环境

在开始之前，你需要以下环境：

操作系统 - SLE15SP2 虚拟机 - 5 VCPU - 2 RAM - 8 GB 磁盘 - 30 GB Arch - s390x

以下是我使用的各个虚拟机的 IP 地址。你可以随时在本文查阅这些 IP 地址代表的含义：

K3s Server 1 - `10.161.129.54`
K3s Server 2 - `10.161.129.154`
K3s Agent - `10.161.129.196`
Postgres - `10.161.129.212`
Nginx - `10.161.129.118`

我在大型机 (s390x) 虚拟机中部署，你也可以在其他任何架构上使用相同的命令。现在，我们来看看安装 Postgres 和 K3s 集群的命令。

## 安装 Postgres

我在其中一台虚拟机上安装了 postgres10。我们将该虚拟机命名为 Postgres。你可以复制粘贴以下命令来安装 Postgres。为了安全起见，请确保 Postgres 数据库和 K3s 集群之间存在相互 TLS 通信。我使用了自签名证书来识别使用 OpenSSL 创建的 Postgres。

### Postgres 虚拟机

- 使用 zypper 安装 Postgres 包：

```
zypper -n in postgresql10 postgresql10-server
systemctl start postgresql
```

- 创建 K3s 数据库、用户角色并授予用户角色的所有访问权限：

```
sudo -u postgres psql
create database K3s;
create user K3s with encrypted password 'K3s';
grant all privileges on database K3s to K3s;
exit;
```

我们将使用 `K3s` 数据库来存储集群信息。我们使用 `K3s` 用户来为 K3s 集群进行 Postgres 数据库身份验证。

- 创建标识 Postgres 服务器的自签名证书并将它们存储在 `/var/lib/pgsql/data/` 中：

```
openssl req -new -x509 -days 365 -nodes -text -out /var/lib/pgsql/data/postgres.crt   -keyout /var/lib/pgsql/data/postgres.key -subj "/CN=postgres.rancher.rke2" -addext "subjectAltName=DNS:postgres.rancher.rke2"
```

- 确保限制私钥的访问：

```
chmod 0600 /var/lib/pgsql/data/postgres.key
chown postgres:postgres /var/lib/pgsql/data/postgres.key
```

- 将标识 Postgres 的公钥证书复制到两台 K3s Server，使 K3s Server 可以验证 Postgres 的身份以进行 SSL 通信：

```
scp /var/lib/pgsql/data/postgres.crt sles@10.161.129.54:
scp /var/lib/pgsql/data/postgres.crt sles@10.161.129.154:
```

- 将 `/var/lib/pgsql/data/pg_hba.conf` 的内容替换为以下内容：

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections onlyf
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
hostssl all             all             0.0.0.0/0               md5 clientcert=verify-full
```

该文件的内容表明，localhost 连接无需密码或 SSL 通信即可连接到数据库。但是，Postgres 的所有其他连接则必须通过 SSL 客户端验证（K3s Server 是 Postgres 的客户端）和密码验证才能进行通信。

在进一步设置 Postgres 数据库之前，我们先创建标识 K3s 集群的证书并将证书复制到 Postgres 虚拟机，以便 Postgres 验证 K3s Server。

### K3s Server 1 虚拟机

- 创建一个标识 K3s 集群的自签名证书，并授予私钥权限：

```
openssl req -new -x509 -days 365 -nodes -text -out K3s.crt -keyout K3s.key -subj "/CN=K3s" -addext "subjectAltName=DNS:K3s"
chmod 0600 K3s.key
```

- 将公钥证书复制到 Postgres 主机，以便 Postgres 可以验证 K3s 客户端：

```
scp /home/sles/K3s.crt sles@10.161.129.212:
```

- 将公钥和私钥复制到其他 K3s Server。两台 Server 组成 K3s 集群：

```
scp /home/sles/K3s.crt /home/sles/K3s.key sles@10.161.129.154:
```

接下来，我们继续看 Postgres 虚拟机。

### Postgres 虚拟机

- 将 K3s.crt 移动到 `/var/lib/pgsql/data` 目录以供 Postgres 配置文件使用：

```
mv /home/sles/K3s.crt /var/lib/pgsql/data/
```

- 将 `/var/lib/pgsql/data/postgresql.conf` 的内容修改为以下值：

```
listen_addresses = '*'
ssl = on
#ssl_ciphers = 'HIGH:MEDIUM:+3DES:!aNULL' # allowed SSL ciphers
#ssl_prefer_server_ciphers = on
#ssl_ecdh_curve = 'prime256v1'
#ssl_dh_params_file = ''
ssl_cert_file = '/var/lib/pgsql/data/postgres.crt'
ssl_key_file = '/var/lib/pgsql/data/postgres.key'
ssl_ca_file = '/var/lib/pgsql/data/K3s.crt'
```

`listen_addresses`：设置为 `*` 或 Postgres 服务器的 IP。这样能确保 Postgres 服务器能侦听节点的 IP 地址。

`ssl`：打开 SSL 来仅使用安全的方式进行通信。

`ssl_cert_file` `ssl_key_file`：标识 Postgres 数据库的证书。我已经在文章开头创建了证书，现在只需要将它们指向证书位置即可。

`ssl_ca_file`：这是一个 CA（证书颁发机构）证书，用于识别 Postgres 的客户端。在我们的示例中，K3s 是客户端。因此，我为 K3s 创建了一个自签名证书，并将 `ssl_ca_file` 指向 K3s 集群的自签名公共证书。

- 重启 Postgres 服务器来应用新配置：

```
systemctl restart postgresql
```

我们已成功部署了 Postgres 数据库，现在我们转到 K3s 虚拟机并在那里安装 K3s。

### K3s Server 1 虚拟机

- 使用正确的标志和值安装 K3s Server：

```
curl -sfL https://get.k3s.io | sh -s - server --datastore-endpoint="postgres://K3s:K3s@postgres.rancher.rke2:5432/K3s" --datastore-cafile="/home/sles/postgres.crt" --token=K3s --datastore-certfile="/home/sles/K3s.crt" --datastore-keyfile="/home/sles/K3s.key" --tls-san=10.161.129.118
```

`--datastore-endpoint`：Postgres 的格式是 `postgres://username:password@hostname:port/database-name`。在这个示例中，我创建了一个 `K3s` 角色，密码是 `K3s`，数据库名称也是 `K3s`。我使用了 `postgres.rancher.rke` 作为 hostname，因为证书是使用 CN 值作为名称创建的。

`--datastore-cafile`：设为 Postgres 的公钥证书，以便 K3s 使用该证书验证 Postgres。在自签名证书中，公共证书充当 CA，可以进行自我验证。

`--datastore-certfile`：这是标识 K3s 集群的公共证书。

`--datastore-keyfile`：属于 K3s 集群的私钥。

`--token`：将创建一个 Secret 密码，以便其他服务器或 Agent 连接到此 K3s 集群。

`--tls-san`：负载均衡器的 IP 地址。

- 为了让 K3s 解析 `postgres.rancher.rke2`，我在 `/etc/hosts` 文件末附加了以下内容：

```
10.161.129.212  postgres.rancher.rke2
```

其中 `10.161.129.212` 是 Postgres 服务器的 IP 地址。

- 现在，验证 K3s Server 是否正常运行并已连接到 Postgres：

```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get pods -A
```

你应该会看到所有处于 `running` 状态的 pod。如果没有，你可以运行：

```
journalctl -xe
```

这能让你查看安装 K3s Server 时发生的错误。

### K3s Server 2 虚拟机

- 使用与安装第一台 Server 相同的命令来安装第二台 K3s Server：

```
curl -sfL https://get.k3s.io | sh -s - server --datastore-endpoint="postgres://K3s:K3s@postgres.rancher.rke2:5432/K3s" --datastore-cafile="/home/sles/postgres.crt" --token=K3s --datastore-certfile="/home/sles/K3s.crt" --datastore-keyfile="/home/sles/K3s.key" --tls-san=10.161.129.118
```

- 验证 K3s Server 是否正常运行并已连接到 Postgres：

```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get pods -A
```

结果应与以下内容类似：

```
NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE
kube-system   svclb-traefik-55frd                       2/2     Running   0          11m
kube-system   svclb-traefik-x59vc                       2/2     Running   0          2m43s
kube-system   local-path-provisioner-6c79684f77-55tkc   1/1     Running   0          107s
kube-system   coredns-d76bd69b-5n8s7                    1/1     Running   0          107s
kube-system   traefik-df4ff85d6-88phx                   1/1     Running   0          107s
kube-system   metrics-server-7cd5fcb6b7-x7t2r           1/1     Running   0          107s
```

- 你还可以通过运行此命令检查集群是否有两台 Server，并查看它们是否具有 `master` 角色：

```
kubectl get nodes
```
结果应与以下内容类似：

```
NAME           STATUS   ROLES                  AGE   VERSION
k3s-server-1   Ready    control-plane,master   14m   v1.23.6+K3s1
k3s-server-2   Ready    control-plane,master   29s   v1.23.6+K3s1
```

在连接到调度工作负载的 K3s Agent 之前，我们需要在 Server 前面添加一个负载均衡器，以便用户或 K3s Agent 与其通信。

### Nginx 负载均衡器虚拟机

我在网络堆栈第 4 层的 K3s 集群 Server 节点前面使用了 Nginx。我们会将端口 6443 的所有请求转发到负载均衡器，然后再发送都其中一台 K3s Server。Kubernetes API server 会侦听该端口。

- 安装 Nginx 包：
```
zypper in nginx
```
- 创建 `/etc/nginx/nginx.conf` 文件并输入如下内容：
```
load_module /usr/lib64/nginx/modules/ngx_stream_module.so;

worker_processes 4;
worker_rlimit_nofile 40000;


events {
	worker_connections 8192;
}

stream {
	log_format logs '$remote_addr - - [$time_local] $protocol $status $bytes_sent $bytes_received $session_time "$upstream_addr"';

	access_log /var/log/nginx/access.log logs;

	upstream K3s_api_server {
		least_conn;
		server 10.161.129.54:6443 max_fails=3 fail_timeout=5s;
		server 10.161.129.154:6443 max_fails=3 fail_timeout=5s;
	}
	server {
		listen 6443;
		proxy_pass K3s_api_server;
	}
}
```

我们使用 `least_conn` 算法来决定请求应该发送到哪个 K3s Server。Nginx 将根据该算法将请求路由到具有最少 active 连接的 Server。

- 重启 Nginx 以使更改生效：

```
nginx -s reload
systemctl reload nginx && systemctl restart nginx
```

至此，我们已设置了负载均衡器，因此现在任何人都可以与我们的 K3s Server 通信。现在我们添加一个 K3s Agent，Agent 会与这个负载均衡器通信并注册 Agent。

### K3s Agent 虚拟机

- 使用以下命令安装 K3s Agent。

`--server`：负载均衡器 IP 地址。

```
curl -sfL https://get.k3s.io | sh -s - agent --token=K3s --server https://10.161.129.118:6443
```

### K3s Server 1 虚拟机

- 现在，你可以通过运行此命令检查 K3s Agent 是否已成功注册：

```
kubectl get nodes
```

结果应与以下内容类似：

```
NAME           STATUS   ROLES                  AGE    VERSION
k3s-server-1   Ready    control-plane,master   41m    v1.23.6+K3s1
k3s-server-2   Ready    control-plane,master   28m    v1.23.6+K3s1
k3s-agent      Ready    <none>                 105s   v1.23.6+K3s1
```

因此，我们已经成功安装了 Postgres、K3s Server 和一个 K3s Agent。你现在拥有了一个具有外部数据库的 HA K3s 集群。请注意，只有部署了 Postgres 和 Nginx 时才能实现完全 HA。有关更多信息，你可以查看参考中的链接。

## [尝试使用 K3s](https://k3s.io)

## 参考

- [K3s 架构文档](https://rancher.com/docs/k3s/latest/en/architecture/#high-availability-with-an-external-db)
- [K3s 数据存储文档](https://rancher.com/docs/k3s/latest/en/installation/datastore/)
- [K3s 安装文档](https://rancher.com/docs/k3s/latest/en/installation/ha/)
- [Nginx 负载均衡器文档](https://rancher.com/docs/rancher/v2.5/en/installation/resources/k8s-tutorials/infrastructure-tutorials/nginx/)