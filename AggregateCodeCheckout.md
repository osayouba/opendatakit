# Introduction #

Instructions for how to checkout the Aggregate code base in eclipse. This page is meant for computer users who are familiar with programming in Java and Eclipse. These instructions are not recommended for novice users.

### Setting up ODK Aggregate with Eclipse ###
  1. Download Eclipse for Java and make sure it runs (reference Eclipse setup for any issues)
  1. Install the <a href='http://www.javaforge.com/project/HGE'> Hg-Mercurial Eclipse Plugin</a> and <a href='http://code.google.com/appengine/docs/java/tools/eclipse.html'>Google Eclipse Plugin</a>. You may also need to install Mercurial separately depending on the OS.
  1. Follow the AggregateEclipseProjectSetup instructions to checkout ODK Aggregate into an Eclipse Project
  1. If you have not done so already, <a href='http://appengine.google.com'>sign up</a> for an App Engine Account and create an application to host ODK
  1. To upload Aggregate to Google App Engine follow the AggregateEclipseUpload instructions.
  1. You should now have an empty ODK Aggregate server. Next, you'll want to upload your XForm to Aggregate and start submitting data.
  1. EclipseDebugging contains some helpful **DEBUGGING SUGGESTIONS**