# Introduction #

Google is deprecating the Master Slave datastore. This document describes the steps needed to migrate your ODK Aggregate instance to the new HRD (High Replication Datastore).

# ODK Aggregate 1.3.0 instructions #

ODK Aggregate 1.3.0 installer now supports the migration mechanism. You just need to follow the instructions on the Google site, and can ignore the steps detailed below.  You do need to run the installer to produce an install image for the new HRD deployment. When you do this, there is now a checkbox on the installer's "Google AppEngine application ID" prompt screen that asks whether this was or is being migrated from a Master-Slave datastore. If you check that checkbox, you will be asked what the original application id was for your app.  Given the new and old application ids, the installer can then generate an install image for the new application id that will properly handle requests sent to the older application id (all users and all ODK Collect clients should continue to use the older application id).

# (Legacy) ODK Aggregate 1.2.0 instructions #


<font color='red'>ODK Aggregate 1.2.0 installer does not support the migration mechanism. It is recommended that you do not upgrade at this time.  If you must, the steps are outlined below.</font>  The manual steps that an updated installer will eliminate are highlighted in <font color='blue'>blue</font>.

Your application will be inaccessible for a short period of time during the migration (during the read-only period prior to the finish of migration).

The overall steps are to (1) duplicate your application, with the duplicate using the HRD, (2) copy the bulk of your data over in the background, (3) enter a read-only period where all residual data is copied over, (4) finish the migration, switching to use the copy of the application that is running on the HRD.

During phase (3), the application will be unavailable.

## Steps ##

  1. Go to: https://appengine.google.com/
  1. Click on the application id you wish to migrate.
  1. Click on "Application Settings" in the left sidebar.
  1. Scroll down to the "Duplicate Application Settings" heading.
  1. A new application id, with "-hrd" appended, is proposed.
  1. Click on "Check availability"
  1. If 'No', then edit the proposed application id, renaming it until it is available.
  1. Otherwise, the proposed "New Application Identifier" is available. Click "Duplicate Application..."
  1. Switch to that application id in the appengine console (click here: https://appengine.google.com/ and choose the new application identifier).
  1. Click on "Billing Settings" in the left sidebar
  1. Enable billing if you have billing enabled for the application id you are migrating. Specify the same quota limits, etc. as in that existing application.
  1. Run the ODK Aggregate installer (download it here: http://code.google.com/p/opendatakit/downloads/list). <font color='red'>NOTE: if you have an ODK Aggregate 0.9.x instance, you must use the v0.9.8.1 zip.</font> To migrate from 0.9.x to 1.x, see http://code.google.com/p/opendatakit/wiki/AggregateMigration.
  1. The following instructions are specific to the ODK Aggregate 1.x installer. For 0.9.x systems, deploy the ODK Aggregate 0.9.8.1 software up to the new application id.  <font color='red'>We no longer support 0.9.8.1 therefore we have not verified that migration works on that system.</font>
    1. Be sure to specify the same "ODK Aggregate Instance Name" when prompted. Any changes to this name will invalidate all user passwords. If you have forgotten what you entered here, see the [Retrieving ODK Aggregate Instance Name](#Retrieving_ODK_Aggregate_Instance_Name.md) section below.
    1. When prompted for the "Google AppEngine application ID", specify the existing Application Identifier that you are migrating.<font color='blue'>
<ol><li>Do not choose to upload this app (uncheck the final checkbox).<br>
</li><li>Open a browser to the location where the installer files were placed.<br>
</li><li>Navigate to ODKAggregate/WEB-INF/<br>
</li><li>Open appengine-web.xml in an editor (e.g., Notepad++)<br>
</li><li>Change the value for the <code>&lt;application&gt;</code> entry from the application id that is being migrated to the "New Application Identifier" from above.  Save and exit.<br>
</li><li>Proceed with uploading this new app to Google AppEngine.  Do this by clicking on the "uploadAggregateToAppEngine..." file</font>
</li></ol>  1. Now, return to the appengine console of the application you are migrating (click here: https://appengine.google.com/ and choose the existing application identifier that you are migrating).
  1. Click on "Application Settings" on the left sidebar
  1. Under "Migrate Application", click on the "View Migration Tool..."
  1. As the "Destination Application", choose the "New Application Identifier", from earlier.
  1. Leave the "Notify me via email..." checkbox checked.
  1. Click "Start Migration"
  1. Wait until the copy completes. Depending upon the volume of data in your application, it may take considerable time to migrate to the HRD. With a Master-Slave datastore that had 0.74 GB of data, this took about 15 minutes.
  1. If you did not sit and wait for the copy to complete, but received an e-mail and at some later time are returning to the migration process, you should perhaps issue a "Launch Incremental Copy" to transfer as much of the data as possible that came in since the copy completed.
  1. Otherwise, click "Activate Readonly". At this point, users will be unable to access this ODK Aggregate instance (either through the website or via ODK Collect).
  1. For this same application with 0.74 GB of data, the read-only phase took about 6 minutes.
  1. Click the "Finish Migration" to complete the switch to the new HRD instance.


From now on, you will need to follow the install steps above to deploy updated versions to the new application id that expect communications to arrive via the old application id.

## Retrieving ODK Aggregate Instance Name ##

The easiest way to retrieve the ODK Aggregate Instance Name is:
  1. Go to: https://appengine.google.com/
  1. Click on the application id you wish to migrate.
  1. Click on "Logs" in the left sidebar
  1. Open the "Options" expander
  1. Enter `afterPropertiesSet:` in the Filter: field.
  1. Click "Search"
  1. Using your browser's find-in-page search capability, search for `afterPropertiesSet: RealmString:`
  1. you will find an entry like:
```
afterPropertiesSet: RealmString: My Test Instance ODK Aggregate 
```
  1. The ODK Aggregate instance name is everything between `RealmString: ` (beginning after the trailing space), and the final ` ODK Aggregate` (ending before the leading space).  In this case, the ODK Aggregate instance name was `My Test Instance`