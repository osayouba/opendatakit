# Introduction #

Read this if:
  * You've set up ODK Aggregate and it runs great and you can access it from a browser on your machine.  <br>NOTE: the path after the hostname is case sensitive, so <pre><code>http://myserver.org/odkaggregate</code></pre> is different from <pre><code>http://myserver.org/ODKAggregate</code></pre>.<br>
<ul><li>You've installed ODK Collect on a phone.<br>
</li><li>You can't get ODK COllect to download forms from ODK Aggregate.</li></ul>

Here are some troubleshooting tips.<br>
<br>
<h1>Isolating the problem</h1>

Verify that your phone isn't in Airport mode... I've forgotten that more times than I can count.<br>
<br>
Can you can access the ODK Aggregate home page from your phone's browser?<br>
<br>
Can you reach the ODK Aggregate home page from another laptop or computer?<br>
<br>
If one or both fail, refer to the <a href='#Getting_Browsers_to_Work.md'>Getting Browsers to Work</a> section.<br>
<br>
If the browser, works:<br>
<br>
<ul><li>Refer to <a href='#Form_List_shows_but_Downloads_Fail.md'>Form List shows but Downloads Fail</a> if you can see the list of forms but cannot download them to the phone.</li></ul>

<ul><li>Refer to <a href='#Cannot_Retrieve_List_of_Forms.md'>Cannot Retrieve List of Forms</a> if you can't even see the list of forms.</li></ul>

<h1>Getting Browsers to Work</h1>

Here are the two cases for troubleshooting getting the browsers to talk.<br>
<br>
<h2>Both Phone Browser and Other Browser Fail</h2>

Possible causes are:<br>
<ol><li>the name of your webserver is not well-known (browsers typically return a "Server not found error," or a search page, or a set of search results when this happens).  Refer to <a href='#Your_Well-Known_Server_Name.md'>Your Well-Known Server Name</a>.  Note that you need to re-run the ODK Aggregate installer and redeploy to Appspot or copy the new war to your Tomcat server directory to update your server name.<br>
</li><li>you're trying to use HTTPS with a self-signed SSL certificate. ODK Collect will validate the SSL certificate, so if you have a self-signed certificate, the phone will reject the connection until you add the signing certificate to the phone's certificate store (others have posted links on how to do this).  If you're trying to do this, first configure and deploy without SSL, get everything working, then configure with SSL.<br>
</li><li>a firewall running on your webserver is preventing access.  On Linux and MacOSX, this is generally the iptables app.  You will typically want to open port 80 and, if you have an SSL certificate, port 443.  On Windows, this is usually the windows firewall app; refer to <a href='#Windows_Firewall.md'>Windows Firewall</a>.<br>
</li><li>there may be a firewall between the devices and the computer (but not on the computer itself) that is preventing access to the computer.  This is typically true with larger organizations when the devices are connecting from outside the organization's firewall (e.g., browsers on smartphones).  Talk to your network administrator. Firewalls can also be present on cable modems, DSL modems, or WiMax modems.  Contact your Internet service provider for assistance.</li></ol>

<h2>Only Phone Browser Fails</h2>

The phone, especially if it is not using wi-fi hotspots, will be accessing your server from a path through your phone service provider, across the internet, into your organization, and then to your computer.  While it may physically be within your office, the path it uses to reach your server will be from across the internet.<br>
<br>
If only your phone can't reach the webpage, but another computer can,<br>
<br>
<ol><li>the name of your webserver is not well-known on the internet (browsers typically return a "Server not found error," or a search page, or a set of search results when this happens).  Refer to <a href='#Your_Well-Known_Server_Name.md'>Your Well-Known Server Name</a>.  Note that you need to re-run the ODK Aggregate installer and redeploy to Appspot or copy the new war to your Tomcat server directory to update your server name.<br>
</li><li>there may be a firewall between the phone and the computer that is preventing access to the computer.  This is typically true with larger organizations.  Talk to your network administrator. Firewalls can also be present on cable modems, DSL modems, or WiMax modems.  Contact your Internet service provider for assistance.</li></ol>

<h1>Your Well-Known Server Name</h1>

To troubleshoot the naming of your server:<br>
<ul><li>A common configuration problem is specifying "localhost" or "127.0.0.1" for the fully qualified hostname during the ODK Aggregate installation process.  This is like saying "myself"  -- if you enter that into a browser on the phone or another computer, you're directing that device's browser to connect to the device itself rather than the machine with ODK Aggregate running on it.<br>
</li><li>the hostname you specified may not be fully-qualified, so it might not be a proper name for identifying your computer from outside your organization (from the internet).<br>
</li></ul><blockquote>For example, <code>humbug</code> might be a name for your server within your organization; from outside, if your organization is <code>grinch.org</code>, then you likely need to specify <code>humbug.grinch.org</code> to identify your computer before devices outside of your organization (e.g., your phone or connections from a wi-fi hotspot in a coffee shop) can find your server. Talk to your network administrator.<br>
</blockquote><ul><li>If you don't have a network administrator, try using Dynamic DNS to give your server a fully-qualified name visible from the internet.</li></ul>

<b>NOTE:</b> you need to re-run the ODK Aggregate installer to update the configuration and resolve this problem.<br>
<br>
<h1>Windows Firewall</h1>

To allow phones and other computers to access ODK Aggregate, you need to open the ports to the server for that access. To do this, open a command window (Start, search for 'cmd'. Right-click, choose 'Run as administrator'.). Within this command window, type:<br>
<pre><code>netsh firewall add portopening TCP 8080 "ODK Aggregate"<br>
</code></pre>
This will allow network traffic to access the Tomcat server through port 8080.  If you wish to use the standard http port, specify port 80 instead (you must also specify port 80 during the install).<br>
<br>
If you wish to use SSL/TLS communication (https:), you must configure Tomcat with an SSL certificate and also open port 443 (repeat the above command, but with 443 replacing 80). The configuration of Tomcat with SSL is well beyond the scope of this document. Refer to the Tomcat website for the steps involved.<br>
<br>
After you are done with these steps, you can close the command window.<br>
<br>
<h1>Cannot Retrieve List of Forms</h1>

This assumes you can reach the ODK Aggregate webpage from the phone's browser.<br>
<br>
Steps to try:<br>
<ol><li>On ODK Collect, verify that you have specified the full URL to your ODK Aggregate instance. If you have a browser open, the Server URL to specify in ODK Collect is everything before the <code>/Aggregate.html</code>.<br>
</li><li>On ODK Collect, pay particular attention to whether the host is reached using http or https.  ODK Aggregate 1.x is configured to use https whenever possible; this is a change from ODK Aggregate 0.9.x and is easy to overlook when configuring the server URL on the phone.<br>
</li><li>On ODK Aggregate, have you configured it to allow anonymous submissions or configured an ODK username with <code>Data Collector</code> privileges? Refer to <a href='#Configuring_ODK_Aggregate_Privileges.md'>Configuring ODK Aggregate Privileges</a>.<br>
</li><li>On ODK Collect, have you either left the username/password blank (if allowing anonymous submissions) or specified the ODK username and password for a username with <code>Data Collector</code> privileges?</li></ol>

Note that ODK Collect 1.1.5 and earlier can only connect to an ODK Aggregate instance that allows anonymous submissions. Refer to <a href='#Configuring_ODK_Aggregate_Privileges.md'>Configuring ODK Aggregate Privileges</a>.<br>
<br>
<h2>Configuring ODK Aggregate Privileges</h2>

Log onto ODK Aggregate using the super-user Email account, or another account with <code>Site Administrator</code> privileges.<br>
<br>
Navigate to the <code>Permissions</code> sub-tab under the <code>Site Admin</code> tab.<br>
<br>
<h3>Allowing anonymous access</h3>

On the <code>Permissions</code> sub-tab:<br>
<br>
<ol><li>Give the <code>Data Collector</code> privilege to the <code>anonymousUser</code> by placing a check in the checkbox under the <code>Data Collector</code> heading.<br>
</li><li>Click <code>Save Changes</code> to apply this change.</li></ol>

<h3>Configuring an ODK Username</h3>

On the <code>Permissions</code> sub-tab:<br>
<br>
To add a user, give them the <code>Data Collector</code> privilege, and set a password, do the following:<br>
<ol><li>Type the username in the text area below the <code>Add Users</code> heading.  Spaces, commas and semi-colons can be used to delimit multiple usernames.<br>
</li><li>Click <code>Add</code> to create this username.  The page will refresh with the username(s) appearing in the <code>Edit Users</code> table.<br>
</li><li>Give the <code>Data Collector</code> privilege to a username by placing a check in the checkbox under the <code>Data Collector</code> heading.<br>
</li><li>Click <code>Save Changes</code>.  The table will refresh.<br>
</li><li>Click <code>Change Password</code> for the given username.  Enter the password in the pop-up dialog and click on the <code>Change Password</code> button on that pop-up.</li></ol>

<h1>Form List shows but Downloads Fail</h1>

On the phone, from within ODK Collect, you can see the list of forms, but when you choose a form to download, it fails.<br>
<br>
This is usually a misconfiguration of ODK Aggregate.  You've likely made a change to the hostname in order to access the server, but haven't re-run the ODK Aggregate installer and re-deployed ODK Aggregate in order to make the corresponding update in the server configuration.<br>
<br>
To confirm what the problem is, you can open a browser, log onto ODK Aggregate, and then edit the URL and replace <code>/Aggregate.html</code>, and everything after that, with <code>/xformsList</code>

The browser should then show an XML document that is exactly what the phone would have retrieved to display the form list.  Pay particular attention to the <code>&lt;downloadUrl&gt;</code> and <code>&lt;manifestUrl&gt;</code> entries in this document. There is likely something wrong with the hostname (or it is specifying http instead of https). You should be able to cut-and-paste the URL of the downloadUrl into a browser and have it download the form definition from the server.<br>
<br>
The URLs in this XML document are constructed using the fully-qualified hostname you gave the ODK Aggregate installer. You need to re-run the installer and redeploy your ODK Aggregate instance (either to Appspot or by copying the resulting war file to your Tomcat server) to correct any problems.<br>
<br>
<h1>NOTES</h1>
