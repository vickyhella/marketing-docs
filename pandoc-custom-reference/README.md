# Pandoc Options

## --reference-doc

Use the specified file as a style reference in producing a docx or ODT file. 

For details, see [reference-doc](https://pandoc.org/MANUAL.html#option--reference-doc).

```
--reference-doc=FILE|URL
# Example:
--reference-doc my.docx
```

Open the reference file and **modify the styles** as needed, and then use the `--reference-doc` option in the pandoc command.

The references are already generated: 
- [docx](custom-reference.docx)
- [pptx](custom-reference.pptx)
- [odt](custom-reference.odt)

You can regenerate the references using the commands below:

```
pandoc -o custom-reference.docx --print-default-data-file reference.docx
pandoc -o custom-reference.pptx --print-default-data-file reference.pptx
pandoc -o custom-reference.odt --print-default-data-file reference.odt
```