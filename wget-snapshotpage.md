## Use wget to mirror a single page and its visible dependencies (images, styles)

### Explanation

via [GNU wget docs](http://www.gnu.org/software/wget/manual/html_node/HTTP-Options.html)

#### `--adjust-extension`, `-E` 

> If a file of type ‘application/xhtml+xml’ or ‘text/html’ is downloaded and the URL does not end with the regexp ‘\.[Hh][Tt][Mm][Ll]?’, this option will cause the suffix ‘.html’ to be appended to the local filename. This is useful, for instance, when you’re mirroring a remote site that uses ‘.asp’ pages, but you want the mirrored pages to be viewable on your stock Apache server. Another good use for this is when you’re downloading CGI-generated materials. A URL like ‘http://site.com/article.cgi?25’ will be saved as article.cgi?25.html.

#### `--span-hosts`, `H`



## Short form:

```sh
wget -E -H -k -K -p http://flair.myfloridacfo.com/approot/dispub2/cvphsrch.htm


wget -E --span-hosts --convert-links --backup-converted \
     --page-requisites --no-directories 
     --directory-prefix=thepageslug
     http://www.thepage.to/download/for/posterity.html
```


## The long form:

```sh
wget --adjust-extension --span-hosts --convert-links --backup-converted \
     --page-requisites --no-directories 
     --directory-prefix=thepageslug
     http://www.thepage.to/download/for/posterity.html
```
