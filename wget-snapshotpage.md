# Use wget to mirror a single page and its visible dependencies (images, styles)

This is a quick command I use to snapshot webpages that have a fun image I want to keep for my own collection of [WTFViz](http://viz.wtf/). Why not just right-click and save the image? Oftentimes the webpage that the image lives on contains necessary context.

The result of this [wget command](http://www.gnu.org/software/wget/) is something a little more portable than a screenshot of said webpage and embedded graphic.

## Short form:

```sh
wget -E -H -k -K -nd -N -p -P thepageslug \
     http://www.thepage.to/save/four/posterity.html
```


## The long form:

```sh
wget --adjust-extension --span-hosts --convert-links --backup-converted \
     --no-directories --timestamping --page-requisites \
     --directory-prefix=thepageslug \
     http://www.thepage.to/save/four/posterity.html
```


## Explanation

Relevant Stack Overflow questions/answers that were consulted/copied from:

[command line - Save a single web page (with background images) with Wget - Super User (superuser.com)](http://superuser.com/questions/55040/save-a-single-web-page-with-background-images-with-wget) 

[How do I use Wget to download all Images into a single Folder - Stack Overflow (stackoverflow.com)](http://stackoverflow.com/questions/4602153/how-do-i-use-wget-to-download-all-images-into-a-single-folder/7843316#7843316) 




#### `--adjust-extension`, `-E` 

Appends `.html` to the local copy of whatever file you're trying to mirror:

      http://www.example.com/hey/there

&ndash; results in:
  
      there.html

via [GNU Wget 1.16.2 Manual: HTTP Options (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/HTTP-Options.html) :

> If a file of type ‘application/xhtml+xml’ or ‘text/html’ is downloaded and the URL does not end with the regexp ‘\.[Hh][Tt][Mm][Ll]?’, this option will cause the suffix ‘.html’ to be appended to the local filename. This is useful, for instance, when you’re mirroring a remote site that uses ‘.asp’ pages, but you want the mirrored pages to be viewable on your stock Apache server. Another good use for this is when you’re downloading CGI-generated materials. A URL like ‘http://site.com/article.cgi?25’ will be saved as article.cgi?25.html.

#### `--span-hosts`, `-H`

Even though we're trying to mirror a single page, that page may link to offsite assets (such as images or stylesheets) located on a CDN (i.e. a different domain, such as `s3.thissite.amazon.aws.com`). However, since we're *only* mirroring a single page, this shouldn't cause wget to crawl past the foreign hosts that are hosting these asset files.

via [GNU Wget 1.16.2 Manual: Spanning Hosts (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Spanning-Hosts.html):

> __Span to any host__: The ‘-H’ option turns on host spanning, thus allowing Wget’s recursive run to visit any host referenced by a link. Unless sufficient recursion-limiting criteria are applied depth, these foreign hosts will typically link to yet more hosts, and so on until Wget ends up sucking up much more data than you have intended.


#### `--convert-links`, `-k`

When mirroring the target webpage, we create a self-contained folder of all of its external dependencies, so that you can disconnect your computer from the Internet, open that target webpage, and see it (mostly) function in its own non-Internet-aware environment.

via [GNU Wget 1.16.2 Manual: Recursive Retrieval Options (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Recursive-Retrieval-Options.html):

> After the download is complete, convert the links in the document to make them suitable for local viewing. This affects not only the visible hyperlinks, but any part of the document that links to external content, such as embedded images, links to style sheets, hyperlinks to non-HTML content, etc.

#### `--backup-converted`, `-K`

Because the original HTML are being converted via `--converted-links`, you might want to stash a copy of the original HTML and its original local paths/urls for posterity. This option saves those files &ndash; typically with `.css` and `.html` extensions &ndash; with an extra `.orig` extension.

More info at [GNU Wget 1.16.2 Manual: Recursive Retrieval Options (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Recursive-Retrieval-Options.html) 

#### `--page-requisites`, `p`

via [GNU Wget 1.16.2 Manual: Recursive Retrieval Options (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Recursive-Retrieval-Options.html):

> This option causes Wget to download all the files that are necessary to properly display a given HTML page. This includes such things as inlined images, sounds, and referenced stylesheets.

#### `--timestamping`, `N`

This checks to see if `target.html.orig` (assuming `--convert-links` was used) already exists locally, and if so, if it is older than the remote server's timestamp for the file. If not, then wget won't bother to re-download the page. This only works if the remote server returns a `Last-Modified` header.

More info at: [GNU Wget 1.16.2 Manual: Time-Stamping Usage (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Time_002dStamping-Usage.html#Time_002dStamping-Usage) 


#### `--directory-prefix=someprefix`, `-P someprefix`, 

Saves the mirrored page and its contents in a folder named `someprefix`

via [GNU Wget 1.16.2 Manual: Directory Options (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Directory-Options.html#index-directory-prefix):

> Set directory prefix to prefix. The directory prefix is the directory where all other files and subdirectories will be saved to, i.e. the top of the retrieval tree. The default is ‘.’ (the current directory).

#### `--no-directories`, `-nd`

Makes a nice, flat listing of files. By default, using `wget` on:

        http://www.example.com/hello/world/whatIwant.html

&ndash; will create a local folder in which you have to navigate the subfolders:

       ./hello/world/whatIwant.html

The `-nd` option will put the target page (and all of its dependencies) into the top-level folder:

      ./whatIwant.html

via [GNU Wget 1.16.2 Manual: Directory Options (gnu.org)](http://www.gnu.org/software/wget/manual/html_node/Directory-Options.html):

> Do not create a hierarchy of directories when retrieving recursively. With this option turned on, all files will get saved to the current directory, without clobbering (if a name shows up more than once, the filenames will get extensions ‘.n’).



