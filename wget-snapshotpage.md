## Use wget to mirror a single page and its visible dependencies (images, styles)

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


--------











### Example usage and output

```sh
user:~/data/wtfviz $ wget -E -H -k -K -nd -N -p -P florida-cfo-state-payments-dollar \
                     http://flair.myfloridacfo.com/approot/dispub2/cvphsrch.htm
--2015-05-22 12:53:38--  http://flair.myfloridacfo.com/approot/dispub2/cvphsrch.htm
Resolving flair.myfloridacfo.com... 158.229.251.10
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 200 Document follows
Length: 13655 (13K) [text/html]
Saving to: 'florida-cfo-state-payments-dollar/cvphsrch.htm'

cvphsrch.htm        100%[=====================>]  13.33K  63.5KB/s   in 0.2s   

2015-05-22 12:53:39 (63.5 KB/s) - 'florida-cfo-state-payments-dollar/cvphsrch.htm' saved [13655/13655]

Loading robots.txt; please ignore errors.
--2015-05-22 12:53:39--  http://flair.myfloridacfo.com/robots.txt
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 302 Found
Location: https://flair.dbf.state.fl.us/robots.txt [following]
--2015-05-22 12:53:39--  https://flair.dbf.state.fl.us/robots.txt
Resolving flair.dbf.state.fl.us... 158.229.251.10
Connecting to flair.dbf.state.fl.us|158.229.251.10|:443... connected.
HTTP request sent, awaiting response... 404 Not Found
2015-05-22 12:53:39 ERROR 404: Not Found.

--2015-05-22 12:53:39--  http://flair.myfloridacfo.com/dispub2/navs.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 3023 (3.0K) [text/css]
Saving to: 'florida-cfo-state-payments-dollar/navs.css'

navs.css            100%[=====================>]   2.95K  --.-KB/s   in 0s     

2015-05-22 12:53:40 (67.0 MB/s) - 'florida-cfo-state-payments-dollar/navs.css' saved [3023/3023]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/textual.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 2124 (2.1K) [text/css]
Saving to: 'florida-cfo-state-payments-dollar/textual.css'

textual.css         100%[=====================>]   2.07K  --.-KB/s   in 0s     

2015-05-22 12:53:40 (169 MB/s) - 'florida-cfo-state-payments-dollar/textual.css' saved [2124/2124]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/dfs_headfoot_screen.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 3270 (3.2K) [text/css]
Saving to: 'florida-cfo-state-payments-dollar/dfs_headfoot_screen.css'

dfs_headfoot_screen 100%[=====================>]   3.19K  --.-KB/s   in 0s     

2015-05-22 12:53:40 (104 MB/s) - 'florida-cfo-state-payments-dollar/dfs_headfoot_screen.css' saved [3270/3270]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/eo.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 22 [text/css]
Saving to: 'florida-cfo-state-payments-dollar/eo.css'

eo.css              100%[=====================>]      22  --.-KB/s   in 0s     

2015-05-22 12:53:40 (2.62 MB/s) - 'florida-cfo-state-payments-dollar/eo.css' saved [22/22]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/links.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 3796 (3.7K) [text/css]
Saving to: 'florida-cfo-state-payments-dollar/links.css'

links.css           100%[=====================>]   3.71K  --.-KB/s   in 0s     

2015-05-22 12:53:40 (90.5 MB/s) - 'florida-cfo-state-payments-dollar/links.css' saved [3796/3796]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/print.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 588 [text/css]
Saving to: 'florida-cfo-state-payments-dollar/print.css'

print.css           100%[=====================>]     588  --.-KB/s   in 0s     

2015-05-22 12:53:40 (24.4 MB/s) - 'florida-cfo-state-payments-dollar/print.css' saved [588/588]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/approot/dispub2/css/vendor.css
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 4092 (4.0K) [text/css]
Saving to: 'florida-cfo-state-payments-dollar/vendor.css'

vendor.css          100%[=====================>]   4.00K  --.-KB/s   in 0s     

2015-05-22 12:53:40 (139 MB/s) - 'florida-cfo-state-payments-dollar/vendor.css' saved [4092/4092]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/crumb2.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 755 [application/x-javascript]
Saving to: 'florida-cfo-state-payments-dollar/crumb2.js'

crumb2.js           100%[=====================>]     755  --.-KB/s   in 0s     

2015-05-22 12:53:40 (72.0 MB/s) - 'florida-cfo-state-payments-dollar/crumb2.js' saved [755/755]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/dispub2/print2.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 955 [application/x-javascript]
Saving to: 'florida-cfo-state-payments-dollar/print2.js'

print2.js           100%[=====================>]     955  --.-KB/s   in 0s     

2015-05-22 12:53:40 (91.1 MB/s) - 'florida-cfo-state-payments-dollar/print2.js' saved [955/955]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/approot/dispub2/js/showhide.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 448 [application/x-javascript]
Saving to: 'florida-cfo-state-payments-dollar/showhide.js'

showhide.js         100%[=====================>]     448  --.-KB/s   in 0s     

2015-05-22 12:53:40 (42.7 MB/s) - 'florida-cfo-state-payments-dollar/showhide.js' saved [448/448]

--2015-05-22 12:53:40--  http://flair.myfloridacfo.com/approot/dispub2/js/fiscal_year.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 673 [application/x-javascript]
Saving to: 'florida-cfo-state-payments-dollar/fiscal_year.js'

fiscal_year.js      100%[=====================>]     673  --.-KB/s   in 0s     

2015-05-22 12:53:41 (58.3 MB/s) - 'florida-cfo-state-payments-dollar/fiscal_year.js' saved [673/673]

--2015-05-22 12:53:41--  http://flair.myfloridacfo.com/ibi_html/javaassist/nls.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 OK
Length: 2932 (2.9K) [text/javascript]
Saving to: 'florida-cfo-state-payments-dollar/nls.js'

nls.js              100%[=====================>]   2.86K  --.-KB/s   in 0.001s 

2015-05-22 12:53:41 (1.90 MB/s) - 'florida-cfo-state-payments-dollar/nls.js' saved [2932/2932]

--2015-05-22 12:53:41--  http://flair.myfloridacfo.com/ibi_html/javaassist/ibi/html/js/ibigbl.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 OK
Length: 65402 (64K) [text/javascript]
Saving to: 'florida-cfo-state-payments-dollar/ibigbl.js'

ibigbl.js           100%[=====================>]  63.87K  --.-KB/s   in 0.1s   

2015-05-22 12:53:41 (590 KB/s) - 'florida-cfo-state-payments-dollar/ibigbl.js' saved [65402/65402]

--2015-05-22 12:53:41--  http://flair.myfloridacfo.com/approot/dispub2/spacer.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 404 Not Found
2015-05-22 12:53:41 ERROR 404: Not Found.

--2015-05-22 12:53:41--  http://flair.myfloridacfo.com/approot/dispub2/img/statepayments.png
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 200 Document follows
Length: 17033 (17K) [www/unknown]
Saving to: 'florida-cfo-state-payments-dollar/statepayments.png'

statepayments.png   100%[=====================>]  16.63K  86.9KB/s   in 0.2s   

2015-05-22 12:53:41 (86.9 KB/s) - 'florida-cfo-state-payments-dollar/statepayments.png' saved [17033/17033]

--2015-05-22 12:53:41--  http://flair.myfloridacfo.com/approot/dispub2/img/FACTSLink.png
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 21171 (21K) [www/unknown]
Saving to: 'florida-cfo-state-payments-dollar/FACTSLink.png'

FACTSLink.png       100%[=====================>]  20.67K  --.-KB/s   in 0.003s 

2015-05-22 12:53:41 (7.86 MB/s) - 'florida-cfo-state-payments-dollar/FACTSLink.png' saved [21171/21171]

--2015-05-22 12:53:41--  http://flair.myfloridacfo.com/approot/dispub2/img/dollarbillnohead2_2014.jpg
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 109641 (107K) [image/jpeg]
Saving to: 'florida-cfo-state-payments-dollar/dollarbillnohead2_2014.jpg'

dollarbillnohead2_2 100%[=====================>] 107.07K   539KB/s   in 0.2s   

2015-05-22 12:53:42 (539 KB/s) - 'florida-cfo-state-payments-dollar/dollarbillnohead2_2014.jpg' saved [109641/109641]

--2015-05-22 12:53:42--  http://flair.myfloridacfo.com/approot/dispub2/legal.js
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 523 [application/x-javascript]
Saving to: 'florida-cfo-state-payments-dollar/legal.js'

legal.js            100%[=====================>]     523  --.-KB/s   in 0s     

2015-05-22 12:53:42 (49.9 MB/s) - 'florida-cfo-state-payments-dollar/legal.js' saved [523/523]

--2015-05-22 12:53:42--  http://flair.myfloridacfo.com/dispub2/internal_blue.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 117 [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/internal_blue.gif'

internal_blue.gif   100%[=====================>]     117  --.-KB/s   in 0s     

2015-05-22 12:53:42 (11.2 MB/s) - 'florida-cfo-state-payments-dollar/internal_blue.gif' saved [117/117]

--2015-05-22 12:53:42--  http://flair.myfloridacfo.com/color_tabs_left2.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 302 Found
Location: https://flair.dbf.state.fl.us/color_tabs_left2.gif [following]
--2015-05-22 12:53:42--  https://flair.dbf.state.fl.us/color_tabs_left2.gif
Connecting to flair.dbf.state.fl.us|158.229.251.10|:443... connected.
HTTP request sent, awaiting response... 401 Unauthorized

Username/Password Authentication Failed.
--2015-05-22 12:53:43--  http://flair.myfloridacfo.com/color_tabs_right2.gif
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://flair.dbf.state.fl.us/color_tabs_right2.gif [following]
--2015-05-22 12:53:43--  https://flair.dbf.state.fl.us/color_tabs_right2.gif
Connecting to flair.dbf.state.fl.us|158.229.251.10|:443... connected.
HTTP request sent, awaiting response... 401 Unauthorized

Username/Password Authentication Failed.
--2015-05-22 12:53:43--  http://flair.myfloridacfo.com/dispub2/appHeader934.gif
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 200 Document follows
Length: 9662 (9.4K) [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/appHeader934.gif'

appHeader934.gif    100%[=====================>]   9.44K  --.-KB/s   in 0.1s   

2015-05-22 12:53:44 (95.7 KB/s) - 'florida-cfo-state-payments-dollar/appHeader934.gif' saved [9662/9662]

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/link_media.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 174 [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/link_media.gif'

link_media.gif      100%[=====================>]     174  --.-KB/s   in 0s     

2015-05-22 12:53:44 (16.6 MB/s) - 'florida-cfo-state-payments-dollar/link_media.gif' saved [174/174]

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/link_external.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 94 [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/link_external.gif'

link_external.gif   100%[=====================>]      94  --.-KB/s   in 0s     

2015-05-22 12:53:44 (8.96 MB/s) - 'florida-cfo-state-payments-dollar/link_external.gif' saved [94/94]

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/link_h1.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 404 Not Found
2015-05-22 12:53:44 ERROR 404: Not Found.

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/internal_black.gif
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 404 Not Found
2015-05-22 12:53:44 ERROR 404: Not Found.

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/link_h3.gif
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 404 Not Found
2015-05-22 12:53:44 ERROR 404: Not Found.

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/link_docu.gif
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 200 Document follows
Length: 173 [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/link_docu.gif'

link_docu.gif       100%[=====================>]     173  --.-KB/s   in 0s     

2015-05-22 12:53:44 (16.5 MB/s) - 'florida-cfo-state-payments-dollar/link_docu.gif' saved [173/173]

--2015-05-22 12:53:44--  http://flair.myfloridacfo.com/dispub2/link_eMail.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 200 Document follows
Length: 75 [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/link_eMail.gif'

link_eMail.gif      100%[=====================>]      75  --.-KB/s   in 0s     

2015-05-22 12:53:45 (5.50 MB/s) - 'florida-cfo-state-payments-dollar/link_eMail.gif' saved [75/75]

--2015-05-22 12:53:45--  http://flair.myfloridacfo.com/dispub2/link_eServe.gif
Reusing existing connection to flair.myfloridacfo.com:80.
HTTP request sent, awaiting response... 404 Not Found
2015-05-22 12:53:45 ERROR 404: Not Found.

--2015-05-22 12:53:45--  http://flair.myfloridacfo.com/siteImages/global/docicons/internal_blue_trans.png
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://flair.dbf.state.fl.us/siteImages/global/docicons/internal_blue_trans.png [following]
--2015-05-22 12:53:45--  https://flair.dbf.state.fl.us/siteImages/global/docicons/internal_blue_trans.png
Connecting to flair.dbf.state.fl.us|158.229.251.10|:443... connected.
HTTP request sent, awaiting response... 401 Unauthorized

Username/Password Authentication Failed.
--2015-05-22 12:53:45--  http://flair.myfloridacfo.com/dispub2/appheaderTransparency.gif
Connecting to flair.myfloridacfo.com|158.229.251.10|:80... connected.
HTTP request sent, awaiting response... 200 Document follows
Length: 5264 (5.1K) [image/gif]
Saving to: 'florida-cfo-state-payments-dollar/appheaderTransparency.gif'

appheaderTransparen 100%[=====================>]   5.14K  --.-KB/s   in 0.002s 

2015-05-22 12:53:46 (3.24 MB/s) - 'florida-cfo-state-payments-dollar/appheaderTransparency.gif' saved [5264/5264]

FINISHED --2015-05-22 12:53:46--
Total wall clock time: 7.2s
Downloaded: 25 files, 259K in 0.8s (319 KB/s)
Converting florida-cfo-state-payments-dollar/cvphsrch.htm... 20-4
Converting florida-cfo-state-payments-dollar/dfs_headfoot_screen.css... 1-0
Converting florida-cfo-state-payments-dollar/navs.css... 1-2
Converting florida-cfo-state-payments-dollar/eo.css... nothing to do.
Converting florida-cfo-state-payments-dollar/textual.css... nothing to do.
Converting florida-cfo-state-payments-dollar/links.css... 11-6
Converting florida-cfo-state-payments-dollar/vendor.css... 1-0
Converting florida-cfo-state-payments-dollar/print.css... 0-1
Converted 8 files in 0.003 seconds.
```


