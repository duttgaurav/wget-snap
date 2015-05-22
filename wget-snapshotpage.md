## Use wget to mirror a single page and its visible dependencies (images, styles)

Short form:

```sh
wget --adjust-extension --span-hosts --convert-links --backup-converted \
     --page-requisites --no-directories 
     --directory-prefix=thepageslug
     http://www.thepage.to/download/for/posterity.html
```


```

The long form:

```sh
wget --adjust-extension --span-hosts --convert-links --backup-converted \
     --page-requisites --no-directories 
     --directory-prefix=thepageslug
     http://www.thepage.to/download/for/posterity.html
```
