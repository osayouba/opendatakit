# Introduction #

This document describes how to set up your own web server for the various ODK Scan web applications. I.e., TemplateMaker tool.

# Step 0: install a web server #

Install an Apache web server: http://httpd.apache.org/download.cgi
on your computer. Make note of the port you configured that server to use.

This server will have an 'htdocs' directory. You will place the various ODK Scan web applications in that directory.

# TemplateMaker #

Go to: https://github.com/UW-ICTD/TemplateMaker
Click the 'Download ZIP' button on the right side of the screen.

Go to: https://github.com/ajaxorg/ace
Click the 'Download ZIP' button on the right side of the screen

Go to: https://github.com/nathannathan/onde
Click the 'Download ZIP' button on the right side of the screen

Extract all these files to their respective directories.

These will extract to
  * TemplateMaker-master
  * ace-master
  * onde-master

Depending upon your extract tool, you may have a directory with the same name nested within those. e.g., TemplateMaker-master\TemplateMaker-master

Remove any extra level of directory nesting, if present.

Rename the directories, removing the '-master' portion of the name.

This leaves you with:

  * TemplateMaker/  (contains directories 'onde' and 'ace-builds' plus many other files and directories)
  * ace/ (contains directories 'api', 'build' and many other files)
  * onde/ (contains directories 'samples' and 'src')

Now move the onde/ directory into TemplateMaker.  This will overwrite the onde/ directory within TemplateMaker (which didn't have anything in it).

Rename ace/ to ace-builds/ and move it into TemplateMaker. This will overwrite the ace-builds/ directory within TemplateMaker (which had been empty).

Now, copy the entire TemplateMaker directory into the 'htdocs' directory in your Apache web server.

Open Chrome or Safari, and enter http://localhost:XXXX/TemplateMaker
where XXXX is whatever port you installed your webserver on.  If it is installed using port 80, you can omit the :XXXX

You should see the TemplateMaker page.

# XLSXGenerator (ODK Scan form generator) #

Go to: https://github.com/UW-ICTD/XLSXGenerator

Click and select the 'master' branch using the drop-down button just above the list of files and to the right of the green button.

Click the 'Download ZIP' button on the right side of the screen.

Extract the files from the downloaded zip.

Depending upon your extract tool, you may have a directory with the same name nested within those. e.g., XLSXGenerator-master\XLSXGenerator-master

NOTE: if it downloaded XLSXGenerator-gh-pages, then you did not select the 'master' branch before downloading.

Remove any extra level of directory nesting, if present.

Rename the directory, removing the '-master' portion of the name.

Copy this directory to the htdocs directory of your web server.

Open Chrome or Safari, and enter http://localhost:XXXX/XLSXGenerator
where XXXX is whatever port you installed your webserver on.  If it is installed using port 80, you can omit the :XXXX

You should see the XLSXGeneratorpage.