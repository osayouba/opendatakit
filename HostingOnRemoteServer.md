# Connecting to a Server #

### UW student account ###

Homepage URL is found at
```
http://students.washington.edu/<uw_netid>/
```
(Substitute `<uw_netid>` with your UW student id)

#### Activating your account ####
  * Go to http://myuw.washington.edu
  * Click on "My Account" in the upper right corner
  * Click on "Computing Services" on the left sidebar
  * Check that "Dante Account" or "Student Web Publishing" are among the "Active Services"
  * If not active, check them and click subscribe

#### Connect to the vergil server ####
  * If using an FTP client (e.g. Filezilla)
    * For a tutorial on how to use Filezilla, go to https://wiki.filezilla-project.org/FileZilla_Client_Tutorial_(en)
    * Enter the following information:
```
    Hostname/Server: vergil.u.washington.edu
    Port:22
    Protocol: SFTP/SSH
    Username: <uw_netid>
    Password: <uw_netid password>
    Directory: <Leave blank, or if required try "public_html">
```
    * Click connect
    * In the `<uw_netid>`/ directory in the lower right panel of Filezilla, click on the public\_html folder
    * Inside public\_html/ you should find a single file, index.html; the rendered index.html file should be the webpage you first see when you go to your homepage in your browser
  * If using SSH in a terminal
    * Use the command:<br><code>$ssh &lt;uw_netid&gt;@vergil.u.washington.edu</code>
<ul><li>Enter your password when prompted<br>
</li><li>Go to the public_html directory:<br><code>$cd public_html/</code>
</li></ul><ul><li>For more information, visit <a href='http://www.washington.edu/itconnect/connect/web-publishing/shared-hosting/'>http://www.washington.edu/itconnect/connect/web-publishing/shared-hosting/</a></li></ul>


<h3>CSE account</h3>

<ul><li>The url of your homepage is at<br>
<pre><code>http://homes.cs.washington.edu/~&lt;cse_id&gt;/<br>
</code></pre>
</li><li>In the file system, the homepage is located at<br>
<pre><code>/cse/web/homes/&lt;cse_id&gt;<br>
</code></pre>
</li><li>If you wish to have a simple index page, create a .htaccess file in the top level directory<br>
</li><li>In .htaccess, add the line:<br><code>Options +Indexes</code>
</li><li>For more information, visit <a href='http://homes.cs.washington.edu/FAQ.html'>http://homes.cs.washington.edu/FAQ.html</a>
<br></li></ul>

<h1>Making Changes to Files</h1>

The instructions in this section apply only to the server copy of <code>opendatakit.survey-js/</code> <br>
You should not make these changes to your local copy<br>
<br>
<h3>opendatakit.survey-js\app\framework\survey\js\shim.js</h3>
<ul><li>In shim.js, line 40, search for the function  <code>getPlatformInfo</code>
</li><li>Look for the key-value pair in the return statement:<br>
<pre><code>"baseUri":"http://localhost:8000/app/"<br>
</code></pre>
</li><li>For UW accounts, change it to<br>
<pre><code>"baseUri":"http://students.washington.edu/&lt;uw_netid&gt;/ODK/opendatakit.survey-js/app/"<br>
</code></pre>
</li><li>For CSE accounts, change it to<br>
<pre><code>"baseUri":"http://homes.cs.washington.edu/~&lt;cse_netid&gt;/ODK/opendatakit.survey-js/app/"<br>
</code></pre></li></ul>

<h3>opendatakit.survey-js\app\framework\tables\js\control.js</h3>
<ul><li>In control.js, line 92, search for the function  <code>getPlatformInfo</code>
</li><li>Look for the key-value pair in <code>var platformInfo</code>:<br>
<pre><code>baseUri:'http://localhost:8000/app/'<br>
</code></pre>
</li><li>For UW accounts, change it to<br>
<pre><code>baseUri:'http://students.washington.edu/&lt;uw_netid&gt;/ODK/opendatakit.survey-js/app/'<br>
</code></pre>
</li><li>For CSE accounts, change it to<br>
<pre><code>baseUri:'http://homes.cs.washington.edu/~&lt;cse_netid&gt;/ODK/opendatakit.survey-js/app/'<br>
</code></pre>
<br>
<h1>After connecting to the server</h1>
</li><li>Create a new directory in your server called ODK/<br>
<ul><li>For UW accounts, it should be created inside the public_html directory<br>
</li><li>For CSE accounts, in <code>/cse/web/homes/&lt;cse_id&gt;/</code>
</li></ul></li><li>The entire opendatakit.survey-js/ folder will be placed in this newly made ODK/ directory</li></ul>

<h3>If pulling from the repository</h3>
<ul><li>After <code>ssh</code> into the server, go to the ODK/ directory:<br><code>$cd ODK</code>
</li><li>Pull from hg:<br> <code>$hg clone https://code.google.com/p/opendatakit.survey-js/</code>
</li><li>Go to the opendatakit.survey-js/ directory:<br><code>$cd opendatakit.survey-js/</code>
</li><li>Update to the development tip:<br><code>$hg update development</code></li></ul>

<h3>If uploading files using Filezilla</h3>
<ul><li>Drag and drop the entire opendatakit.survey-js/ folder to the ODK/ directory in the server <br>Make sure that you copy over the edited files</li></ul>

<h3>If uploading files using the terminal</h3>
<ul><li>Log out of your server by typing Control+D (Control and d at the same time)<br>
</li><li>Go to the local directory where opendatakit.survey-js/ is located, using the <code>cd</code> command<br>
</li><li>Copy over the opendatakit.survey-js/ directory:<br>
<ul><li>For UW accounts:<br><code>$scp -r opendatakit.survey-js &lt;uw net_id&gt;@vergil.u.washington.edu:public_html/ODK</code>
</li><li>For CSE accounts:<br><code>$scp -r opendatakit.survey-js &lt;cse_id&gt;@attu.cs.washington.edu:/cse/web/homes/&lt;cse_id&gt;/ODK</code>
</li></ul></li><li>Make sure that you copy over the edited files</li></ul>

<h2>See ODK in Chrome</h2>
<ul><li>For UW accounts:<br>
<pre><code>http://students.washington.edu/&lt;uw_netid&gt;/ODK/opendatakit.survey-js/<br>
</code></pre>
</li><li>For CSE accounts:<br>
<pre><code>http://homes.cs.washington.edu/~&lt;cse_id&gt;/ODK/opendatakit.survey-js/<br>
</code></pre>