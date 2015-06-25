# Introduction #

Describes the steps needed to uninstall ODK Aggregate v1.0.

## Google App Engine ##

To uninstall:

  1. Go to:  https://appengine.google.com/
  1. Click on your application id
  1. Click on "Application Settings" under the "Administration" heading in the left sidebar.
  1. Scroll down and click on "Disable Application..."
  1. Choose "Disable Application"
  1. Choose "Request Permanent Deletion"

This will stop and delete your appspot instance.

## MySQL or PostgreSQL ##

To uninstall from Tomcat:

  1. Stop Tomcat
  1. Open a file system browser and explore to the Tomcat webapps location.  On Windows systems, this is typically at, e.g., `C:\Program Files\Tomcat\apache-tomcat-6.0.26\webapps`
  1. Delete the ODKAggregate.war file and the ODKAggregate directory. If you instead renamed these to ROOT.war, delete the ROOT.war file and the ROOT directory.

If you aren't using Tomcat, you can also remove that application and close the firewall port you opened.  On Windows, to close the firewall port, open a cmd window and execute, e.g.,:
```
  netsh firewall delete portopening TCP 80 "ODK Aggregate"
```

If you opened port 443 (https), you would repeat the command with 443 replacing 80 (http).

To remove Tomcat from Windows, if you haven't installed it as a service, you should be able to simply delete its directory (the parent of the webapps directory).

## MySQL ##

After deleting the web application, you will still have data in MySQL. You can uninstall MySQL but that will still leave the data on the system. Browse to the data directory and delete it to reclaim that
space. On Windows, this is typically `C:\MySQL Datafiles`.  You will need administrator permissions to delete these files.

## PostgreSQL ##

After deleting the web application, you will still have data in PostgreSQL.  You can uninstall PostgreSQL but that will still leave the data on the system. Browse to the data directory and delete it to reclaim that space.  On Windows, this is typically `C:\Program Files\PostgreSQL\9.0\data` You should be able to delete everything at 9.0 and below.  You will need administrator permissions to delete these files.