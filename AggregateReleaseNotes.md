# Aggregate v1.4.7 #
  1. when submissions rows are corrupted, consistently report the information necessary for end-users to delete or repair the corrupted submission. 4 error messages needed to be enhanced.
  1. correct ODK 2.0 delete-table functionality so that it does not generate errors when it is not running on AppEngine. The code had been performing an unguarded explicit cast to an AppEngine-only implementation class.

## Upgrading ##

  1. No special upgrade steps required.

# Aggregate v1.4.6 #
  1. fix for Google Sheets publisher-creation problem caused by April 20th deprecation of the Google Docs API. We were making a single call to that obsolete API to create a blank Google Sheets document.
  1. update to javarosa-2015-04-17 jar.  That jar adds OpenStreetMap support to javarosa.
  1. removal of the "Sign in with Google" mechanisms that used OpenID.
  1. widespread update to jars, custom repository configuration, and the elimination of several custom-built jars because of the elimination of OpenID and the maturity of Maven as a version repository.

## Upgrading ##

  1. No special upgrade steps required.

# Aggregate v1.4.5 #
  1. update to javarosa-2015-01-10 jar.
  1. update to jQuery 1.11.1
  1. update the ODK 2.0 Data model and Sync protocol (incompatible with device releases: rev 122 and earlier).
  1. Fix: support performing mark-as-complete on encrypted submissions (requires ODK Briefcase v1.4.5 or higher).
  1. Fix: add server preference to ignore partially inserted/deleted submissions. Logs them but ignores them so that you can access all other rows in your dataset. Disabled by default. By default, all actions fail upon encountering _any_ malformed submission. You should not ignore these failures but should correct them as soon as is practical.
  1. Attempted fix: for "Log In" issue -- insert sleep to give Google a chance to propagate clearing of session cookie. Tweak the webpage resize/layout calculations to be more efficient.
  1. fix: make incomplete deletions and insertions more recoverable.
  1. performance: change MySQL table creation to not declare primary keys and just use ordinary indices for the primary key.
  1. security: Support Enketo-express (allow non-https communications with Enketo server). When the communications are not secure, this change discloses the Enketo access token to eavesdroppers.
  1. security: Filter out forbidden characters in redirect string to prevent XSS attacks.
  1. security: Add clickjacking prevention header as detailed here: https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet

## Upgrading ##

  * If you were testing out ODK Tables, you should delete all ODK Tables files and data tables before upgrading, as the database schema has changed. Note that the server no longer works with ODK Sync 2.0 rev 122 (or earlier releases).

# Aggregate v1.4.4 #
  1. installer now asks for an ODK Aggregate username for the super-user (not a Google e-mail account). Default password is `aggregate`.
  1. banner displayed if super-user's password has not been changed from `aggregate`.
  1. fix: column name generator bug (upload of form definition failed)
  1. fix: add more detailed error messages when a submission is corrupted
  1. IE6 and IE7 are no longer supported. Upgrade your browser.
  1. allow user to specify the ODK 2.0 App Name.
  1. allow anonymous access to ODK 2.0 Sync APIs.
  1. enforce 'Administer Tables' access for ODK 2.0 Sync APIs that alter server configuration.
  1. for ODK 2.0 Sync APIs, remove JBoss Resteasy; use Apache Wink instead.
  1. extensive version updates to supporting software libraries.
  1. update CONFIGURE.txt instructions for maven builds. Define ANT scripts for external dependencies.

## Upgrading ##

<font color='red'>Upgrades require several manual interventions:</font>

  * You must have Java 7 installed - the GAE 1.9.7 SDK used within the installer now requires that version of Java. If you are upgrading from ODK Aggregate 1.4.3, you have already done this.
  * If you were testing out ODK Tables, you should delete all ODK Tables data tables before upgrading, as the database schema has changed.
  * You must flush the session cookies on the server. Session cookies are used to identify the logged-in users of the system. The security software versions were updated in this release, causing the older cookies to become invalid. See below for the upgrade steps.

Assuming you have done (1) and (2) above, the upgrade steps after those are as follows:

  1. Open a browser and go to your Google AppEngine dashboard via: [dashboard](https://appengine.google.com)
  1. Click through to your application id. Then click on "Memcache Viewer" under the "Data" heading in the left sidebar. Keep this window open.
  1. Now, run the installer and deploy ODK Aggregate 1.4.4 to this application id.
  1. After it has deployed, click on `Flush Cache` in the dashboard window's Memcache Viewer screen.
  1. Follow the instructions [here](http://opendatakit.org/use/aggregate/#Using_the_Application) for changing the password of the super-user username.

Other than the need to flush this cache, and the need to delete any ODK Tables data before upgrading, this should be a seamless upgrade from ODK Aggregate 1.4.x

# Aggregate v1.4.3 #
  1. updated javarosa jar
  1. add stub Tomcat/MySQL Eclipse project with readme
  1. update selenium test environment and various 3rd party jars
  1. update the GAE SDK inside the installer to 1.9.0
  1. new ODK Tables sync protocol and UI. Breaks ODK Tables Alpha 2 sync.
  1. new sync protocol exchanges SAVEPOINT\_TYPE, FILTER\_TYPE, FILTER\_VALUE

## Upgrading ##

<font color='red'>Upgrades require a manual intervention:</font>

You must have Java 7 installed - the GAE 1.9.0 SDK used within the installer now requires that version of Java.

If you were testing out ODK Tables, you should delete all ODK Tables data tables before upgrading, as the database schema has changed.

Otherwise, this should be a seamless upgrade from ODK Aggregate 1.4.

# Aggregate v1.4.2 #

  1. Fixes to ODK Tables sync protocol for the combined release.
<font color='red'>Due to interactions with Google AppEngine, the ODK Tables Synchronization protocol does not currently work on Google AppEngine servers. You must install a local server or a VM image in order to use that mechanism</font>

## Upgrading ##

If you were testing out ODK Tables, you should delete all tables before upgrading, as the database schema has changed.

Otherwise, this should be a seamless upgrade from ODK Aggregate 1.4.

# Aggregate v1.4.1 #

Summary of changes:

  1. [Enketo](https://enketo.org/) Webforms integration. You can now use Enketo's browser-based Webforms to fill-in and publish submissions directly into ODK Aggregate. This feature was developed and donated by [SDRC India](http://sdrc.co.in/). To enable Enketo integration, go to the `Site Admin / Preferences` tab and click on `Enketo API Configuration`.
  1. fix the `Z-ALPHA JSON` publisher and the `JSON File` export to emit an array of zero or more objects, one object per submission, with proper treatment of embedded quotes, etc. Confirmed that the output passes JSLint.
  1. fix the `CVS File` export functionality to double-up all occurrences of double-quotes in a field before surrounding that field with double quotes (per RFC 4180).
  1. clean up date and time handling in REDCap publisher and enforce GMT time zone interpretation when rendering date and time strings.
  1. various GWT interfaces have been changed to use concrete ArrayList types (reduces code size).
  1. new permissions have been added in support of ODK Tables -- `Synchronize Tables` and `Administer Tables` and for most ODK Tables interactions, the user is required to have `Synchronize Tables` permissions.
  1. extensive changes to ODK Tables sync protocol and database structures. There will be further changes in the next update.
  1. fix sizing calculations and CSS so that the ODK logo does not get clipped or overwritten.

## Upgrading ##

If you were testing out ODK Tables, you should delete all tables before upgrading, as the database schema has changed.

Otherwise, this should be a seamless upgrade from ODK Aggregate 1.4.

# Aggregate v1.4 #

Summary: Major changes to Aggregate's publishers to update to the latest jars and protocols, add new publishers, and improve quota usage.

  1. changed behavior: simple JSON publisher now POSTs an application/json entity body; added option for how to treat binary content. <font color='red'>Incompatible change; see Upgrading section if you were using the Z-ALPHA JSON Server</font>
  1. changed representation: ODK Tabled storage schema has been revised.  <font color='red'>Incompatible change; see Upgrading section if you used your server to upload or download data to ODK Tables</font>. Syncing with ODK Tables is broken in this release. Only v1.3.4 on Google AppEngine works with the ODK Tables alpha (we are in the middle of changing table schemas).
  1. fix: file exports were not properly writing UTF-8 character sets.
  1. fix: postgreSQL failures on some forms (column names must be less than 63 characters)
  1. Upgrade Aggregate’s Google publishers to use the updated Oauth2 libraries (e.g., Google Fusion Tables, Google Spreadsheet). <font color='red'>Publishing from Google Apps domains <b>does not work</b></font> (it never has). This required a significant rewrite of the publishers so please notify the ODK core team of bugs!
  1. NEW: Add support for publishing data to Google Maps Engine, more information can be found at GoogleMapsEngineInstructions
  1. rework Spreadsheet and Fusion Table publishers to use Google libraries (consistent with the new Google Maps Engine publisher)
  1. additional active-paused state in publisher to extend the delay in publishing attempts (and reduce quota usage) if the destination publisher is reporting an error.
  1. improved how failures during form definition uploads are rolled back to increase the likelihood that the database is restored to a clean state.

# Upgrading #

You may need to clear your browser cache to complete the upgrade. If the browser screen flickers after upgrading, first clear your browser cache and reload the page.

If you were using the **Z-ALPHA JSON Server**, you must delete all instances of that publisher before upgrading. After upgrading, the updated publisher will send a single application/json entity body to the external server instead of a multi-part form containing that entity.

If you were using **ODK Tables** with ODK Aggregate, you need to delete all the ODK Tables data on ODK Aggregate before upgrading.


# Aggregate v1.3.4 #
  1. fix for the v1.3.3 fix for Google AppEngine -- The original fix caused the creation of new publishers to Google Spreadsheets to fail, export to files to fail, form deletions to fail, and purge-sent-submisions actions to fail. This fix should rectify those issues.

# Aggregate v1.3.3 #
  1. fix for Google AppEngine -- entity keys (unique identifiers assigned by Google AppEngine and used internally by ODK Aggregate) may now contains slashes. Submissions that have been assigned an entity key containing a slash were breaking the 'SubmissionKey' parsing used when publishing, retrieving images, accessing repeat groups, or retrieving submissions using ODK Briefcase.

# Aggregate v1.3.2 #
  1. expose the ODK Tables data and management tabs.
  1. when installing for first time, ODK Aggregate will not require you to log in. Access restrictions are not altered when upgrading. This only affects new deployments. [issue 710](https://code.google.com/p/opendatakit/issues/detail?id=710) - upon an initial install, configure Anonymous User with Data Collector and Form Manager (and Data Viewer) permissions.
  1. watchdog sweep interval shortened to 30 seconds in fast-publishing mode (from 60 seconds).  Ensure watchdog is scheduled to be fired when there are records remaining to be published.
  1. fix publisher failure to Google Spreadsheets and Fusion Tables by prepending 'n' to element names beginning with digits.
  1. fix publishing failure with Fusion Tables when a form with repeat groups has submissions without any repeats.
  1. [issue 824](https://code.google.com/p/opendatakit/issues/detail?id=824) - fix for malformed json publishing with null media attachments
  1. [issue 776](https://code.google.com/p/opendatakit/issues/detail?id=776) - attempt to create Spreadsheets and Fusion Tables on behalf of the owner (vs. reassign ownership after the fact).  Potentially fixes Google Apps Domain publishing issues (if service account is granted the permissions).
  1. fix publisher-creation failure that can cause cycling UI refresh.
  1. update javarosa library, adding format-date-time().
  1. [issue 806](https://code.google.com/p/opendatakit/issues/detail?id=806) - briefcase not downloading instances with repeat groups containing images.
  1. update to selemium 2.33.0 to resolve Firefox ESR 17.0.7 failures.

# Aggregate v1.3.1 #

  1. change watchdog to run more frequently if there is an active publisher. Provide a 'disable' button on the Site Admin / Preferences page to restore older behavior (to conserve GAE quota).
  1. [issue 794](https://code.google.com/p/opendatakit/issues/detail?id=794) - form upload failed for some forms on MySQL with stack exhaustion.
  1. [issue 761](https://code.google.com/p/opendatakit/issues/detail?id=761) - Fix to simple JSON publisher. had caused instability when used.
  1. [issue 697](https://code.google.com/p/opendatakit/issues/detail?id=697) - popups don't show centered in screen when displayed on top of scrolling regions.
  1. [issue 786](https://code.google.com/p/opendatakit/issues/detail?id=786) - forms with repeat groups cannot be versioned.
  1. [issue 768](https://code.google.com/p/opendatakit/issues/detail?id=768) - rows-per-page value keeps getting reset on refresh

# Aggregate v1.3 #

A major motivation for this release was the impending OAuth 1.0 deactivation for Google Fusion Tables and, soon to follow, all Google services in general; Google's transition has been delayed because of a usage scenario that is not met with OAuth 2.0. The 1.3 release of ODK Aggregate makes a wholesale transition from OAuth 1.0 to OAuth 2.0, **breaking all publishers.** A lesser motivation is the end of support for Google Maps v1.0 API, scheduled for March 2013; but that shouldn't significantly impact users.

This release also contains 2 changes to the JSON file export and 3 new Alpha-quality publishers.

  1. Installer now supports migrated AppEngine instances (for the Master-Slave -to- High-Replication Datastore migration).
  1. Google Fusion Tables publisher now provides the links to the tables of all the repeat groups, the top-level record, and a left-outer-join view of the first repeat group and top-level record.  This gives a 'flat' view of the data.
  1. Google Spreadsheets and Google Fusion Tables publishers are now using OAuth 2.0 for authentication. This breaks all existing publishers (you need to republish). Additional user configuration is needed to register the necessary OAuth 2.0 credentials via the Site Admin / Preferences page.  When setting up a publisher, you are no longer redirected to Google to allow access; the access credentials defined on the Site Admin / Preferences page bypass that step in the process; instead, you will receive an e-mail from Google notifying you of the newly published data having been shared with you.
  1. Google Maps v3 API is now used for the visualization features.
  1. [issue 728](https://code.google.com/p/opendatakit/issues/detail?id=728) 0 siteKey and userServiceKey have been removed from the installer and siteKey is now saved and accessed from the datastore (this enables easily-replicated AWS VM images to be constructed).
  1. [issue 718](https://code.google.com/p/opendatakit/issues/detail?id=718), [issue 730](https://code.google.com/p/opendatakit/issues/detail?id=730) - some large forms cause table subdivision algorithm to fail, preventing the form from loading into ODK Aggregate. This alters the names of tables that are created and their contents.
  1. Added a 'Published Through' and an 'Owner' column to the 'Published Data' table to communicate the progress of the publisher and who is receiving the data (really, the initial owner).
  1. [issue 724](https://code.google.com/p/opendatakit/issues/detail?id=724) - move the watchdog to the frontend instance to minimize the use of the background thread. This change may cause issues on servers with a very large number of publishers or pending export jobs.
  1. [issue 653](https://code.google.com/p/opendatakit/issues/detail?id=653) - spelling typo fix
  1. [issue 712](https://code.google.com/p/opendatakit/issues/detail?id=712) - fix Fusion Tables link; changed Google Spreadsheets to be links, and to have these links open in new windows.
  1. [issue 558](https://code.google.com/p/opendatakit/issues/detail?id=558) - Google Spreadsheet publisher failed badly if name was blank.
  1. [issue 587](https://code.google.com/p/opendatakit/issues/detail?id=587) - extra comma in JSON file export (in repeat groups)
  1. JSON file export now exports multiple-choice values as a JSON array of string values, rather than a space-separated string.
  1. [issue 648](https://code.google.com/p/opendatakit/issues/detail?id=648) - arbitrary intermingling of http and https requests are problematic
  1. [issue 714](https://code.google.com/p/opendatakit/issues/detail?id=714) - cannot save filter unless display metadata is checked.
  1. [issue 732](https://code.google.com/p/opendatakit/issues/detail?id=732) - version string should be an integer that fits in an int.
  1. [issue 742](https://code.google.com/p/opendatakit/issues/detail?id=742) - unable to change the title of an existing form
  1. forms are now listed alphabetically
  1. MySQL media attachments are stored as BLOB types, allowing the default MySQL configuration to work (the LONG\_BLOB setting had required changes to the out-of-the-box MySQL database configuration)
  1. annual update of all jars and jar dependencies (extensive); removed all OAuth 1.0 jars.
  1. NEW! <font color='red'>Alpha release</font> REDCap (XML) publisher
  1. NEW! <font color='red'>Alpha release</font> Simple JSON publisher
  1. NEW! <font color='red'>Alpha release</font> Ohmage JSON publisher

See [Publisher Details](http://code.google.com/p/opendatakit/wiki/AggregateToJSonXmlREDCapPublishers) for information about the new publishers.

## Upgrading ##

The upgrade will **break all existing publishers.** After the upgrade, ODK Aggregate needs to be configured with OAuth 2.0 credentials on the Site Admin / Permissions page. Once configured, you will then be able to create new publishers for your data (it is not possible to resume or restore publishing to the original publishers).

To avoid having to create new publishers that re-publish already-published data, you can:
  1. Before upgrading:
    1. go to the Form Management page
    1. uncheck `Accept Submissions`
    1. verify that all submissions appearing on the submissions tab have been successfully published to Fusion Tables and Google Spreadsheets.
  1. at this point, because ODK Aggregate is not accepting any new submissions, your surveyors are unable to send filled-in forms and we can be assured that no data is in transit during the upgrade process.
  1. Deploy ODK Aggregate 1.3.0
  1. Go to the Publishers page, and create replacement publishers using 'Stream New Submissions ONLY`
  1. Now go to Fusion Tables or Spreadsheets and copy the data from the 1.2 tables into the newly-created publisher tables.
  1. Finally, go to ODK Aggregate 1.3.0's Form Management page and check `Accept Submissions`
  1. at this point, new submissions will stream into the new publishers and you have manually copied the old data into the new publisher, so these new publishers now have all of your data.

The database tables for the new publishers and older publishers do not overlap, so if you roll back to the ODK Aggregate 1.2 release, you will not see the new publishers, but the earlier 1.2 publishers will 'reappear.'  If you want to, after the upgrade, in MySQL or PostgreSQL, you can drop the unused old tables:
  * `_server_preferences` (has been replaced by `_server_preference_properties`)
  * `_form_service_cursor` (has been replaced by `_form_service_cursor_2`)
  * `_fusion_table` (has been replaced by `_fusion_table_2`)
  * `_fusion_table_repeat` (has been replaced by `_fusion_table_repeat_2`)
  * `_google_spreadsheet` (has been replaced by `_google_spreadsheet_2`)
  * `_google_spreadsheet_repeat` (has been replaced by `_google_spreadsheet_repeat_2`)

# Aggregate v1.2 #
  1. Updated javarosa library with cascading select support (as with KoBo Collect).
  1. Add a Delete button to the Exports list to enable deleting the generated files.
  1. Exported files using filters now export the metadata if displayed  by the filter.
  1. Improved Map visualization display and pop-ups (showing images)
  1. Improved filter, export and publishing pop-ups.
  1. Update to use LONGBLOB and LONGSTRING on MySQL (new tables only) <font color='red'>See MySQL Usage Note Below</font>
  1. cache thumbnail images for 1 hr for improved performance and lower AppEngine datastore usage.
  1. Form definition files and media attachments can now be altered and those changes uploaded to the ODK Aggregate server. The server still maintains only one version of the form, and all alterations must not affect the number of questions in the form or change the data type of any field (e.g., from int to decimal or string, etc.).
  1. Whenever a form or any of its media files are modified, the version attribute in the top-level element (where the form id is defined) must be changed.  Version attributes are recommended to be of the form "yyyymmddnn", e.g., 2012060400 -- the last two digits are the form iteration within the given day.  They must be integer values and the new value must compare lexically greater than the prior value (this means, for example, since "9" compares lexically greater than "10", you cannot update a version from 9 to 10 -- but you could upgrade from "09" to "10").
  1. There is a 15-minute grace period for uploading revisions after which the version must be incremented (e.g., incremented to 2012060401).
  1. Note that you will need the soon-to-be-released updates to ODK Briefcase and ODK Form Uploader in order to use those tools with this version of ODK Aggregate.
  1. Fix odd start-up failures on Google AppEngine
  1. [issue 596](https://code.google.com/p/opendatakit/issues/detail?id=596) - timestamp comment in wrong location

### MySQL Usage Note ###
The use of LONGBLOB and LONGSTRING requires a configuration change in the MySQL server. The server requires the transmisison packet size to be configured large enough to hold the largest LONGBLOB or LONGSTRING you will ever send to the server.  See http://dev.mysql.com/doc/refman/5.5/en/connector-net-programming-blob.html section **22.2.5.10.1. Preparing the MySQL Server** for configuring the max\_allowed\_packet size.

Alternatively, after creating your tables, you can use the MySQL ALTER TABLE command to change the LONGBLOB field to a BLOB field. This was the pre-1.2 setting, and will be the 1.2.1 setting for image fields, returning the system to use 65kB image chunks and avoiding the need to change the server configuration.  If you do this, you will need to stop and restart your ODK Aggregate server for the change to be detected and take effect.

## Upgrading ##

**Existing 1.0 installations can upgrade to the 1.2 release, but, once upgraded, if you use the new form-versioning feature,**<font color='red'>these installations cannot downgrade from 1.2 to the earlier 1.0 releases.</font>

<font color='red'><b>NOTE: MySQL and PostgreSQL require special upgrade steps.</b></font>

### Upgrading Google AppEngine ###
See v1.1 Production Upgrading instructions.

### Upgrading MySQL or PostgreSQL ###
The `_filter_group` table has a new column. If you are running ODK Aggregate 1.0 or 1.1, you will need to issue an "alter table" command on this table to add this column.

#### MySQL ####
The alter table command would be:
```
ALTER TABLE `_filter_group` ADD COLUMN `INCLUDE_METADATA` char(1) NULL;
```

#### PostgreSQL ####
The alter table command would be:
```
ALTER TABLE "_filter_group" ADD COLUMN "INCLUDE_METADATA" boolean NULL;
```
Depending upon your database management tool, you may need to qualify the table name with the schema.

# <font color='red'>PREVIEW:</font> Aggregate v1.1 #
  1. <font color='red'>PREVIEW RELEASE</font> Production deployments should remain on the v1.0.9 release unless the features of this release are critical to your deployments.
  1. Form definition files and media attachments can now be altered and those changes uploaded to the ODK Aggregate server. The server still maintains only one version of the form, and all alterations must not affect the number of questions in the form or change the data type of any field (e.g., from int to decimal or string, etc.).
  1. Whenever a form or any of its media files are modified, the version attribute in the top-level element (where the form id is defined) must be changed.  Version attributes are recommended to be of the form "yyyymmddnn", e.g., 2012060400 -- the last two digits are the form iteration within the given day.  They must be integer values and the new value must compare lexically greater than the prior value (this means, for example, since "9" compares lexically greater than "10", you cannot update a version from 9 to 10 -- but you could upgrade from "09" to "10").
  1. There is a 15-minute grace period for uploading revisions after which the version must be incremented (e.g., incremented to 2012060401).
  1. Note that you will need the soon-to-be-released updates to ODK Briefcase and ODK Form Uploader in order to use those tools with this version of ODK Aggregate.  Compatibility issues may arise as we understand the full ramifications of this change.
  1. Note that ODK Collect 1.1.7 has buggy support for versioned forms. If you make use of versioning, you should use ODK Collect 1.2 RC1.
  1. An updated ODK Aggregate 1.1 will be released at the time ODK Collect 1.2 becomes the official production release.

## Upgrading ##

This is a preview release. The form-versioning capability may be tweaked between now and the 1.1.1 release.

**Existing 1.0 installations can upgrade to the 1.1 release, but, once upgraded, if you use the new form-versioning feature,**<font color='red'>these installations cannot downgrade from 1.1 to the earlier 1.0 releases.</font>

# Aggregate v1.0.9 Production #
  1. fix [issue 598](https://code.google.com/p/opendatakit/issues/detail?id=598) - AppEngine over-quota page showing erroneously.

## Upgrading ##

See v1.0.4 Production Upgrading instructions.

# Aggregate v1.0.8 Production #
  1. fix erroneous release of TaskLock that could cause 60-second lock-out of "Add Forms", causing slow uploads of multimedia forms using FormUploader or Briefcase
  1. fix performance bugs in the TaskLock for GAE.
  1. fix correctness bugs in the TaskLock for MySQL and PostgreSQL that can cause two TaskLocks to be obtained for the same resource if they are both requested at exactly the same second.

## Upgrading ##

See v1.0.4 Production Upgrading instructions.

# Aggregate v1.0.7 Production #
  1. fix [issue 505](https://code.google.com/p/opendatakit/issues/detail?id=505) - double "Add Forms" leaves UI in inoperable state
  1. fix [issue 506](https://code.google.com/p/opendatakit/issues/detail?id=506) - double "Add Forms" leaves UI in inoperable state
  1. JSON file export supported
  1. update installer to use Google AppEngine 1.6.4 SDK
  1. remove uiVersion comparison tests (deprecated functionality).

## Upgrading ##

See v1.0.4 Production Upgrading instructions.

# Aggregate v1.0.6 Production #
  1. fix [issue 542](https://code.google.com/p/opendatakit/issues/detail?id=542) - Add a **Refresh Credentials** button on publishers. This button appears when the publishing to an external service (Google Spreadsheet or Fusion Tables) fails with a 401 (Unauthorized) error.  Clicking the button will walk through the authorization screens for publishing to that service, yielding a new credential for that publisher.
  1. fix [issue 512](https://code.google.com/p/opendatakit/issues/detail?id=512) - Add a **Purge Submission Data** button on the Form Management / Submission Admin tab.  This button enables you to delete submission data up to a specified date.  Incomplete submissions are not deleted.
  1. fix [issue 422](https://code.google.com/p/opendatakit/issues/detail?id=422) - digest auth failing in 1.0.5 Release Candidate.
  1. Includes all functionality from the 1.0.5 release candidate.

## Upgrading ##

See v1.0.4 Production Upgrading instructions.

# Aggregate v1.0.5 Release Candidate #

  1. Reduce memory requirements for KML and CSV generation to enable generation of larger datasets.
  1. Restore bookmark navigation after login challenges - v1.0.4 did not navigate to a requested bookmark page if the user was challenged to log onto the site.
  1. Add support for Oauth 1.0 publishing from Build into Appspot-hosted Aggregate instances.  Oauth 1.0 tokens do not work on Tomcat servers.
  1. Add support for Oauth 2.0 access to Aggregate. Aggregate will accept any Google token granting access to `https://www.googleapis.com/auth/userinfo.email` with access rights equivalent to the indicated Google user.

## Upgrading ##

<font color='red'>This is a <i>Release Candidate</i> and should be considered less reliable than a <i>Production</i> release; please test it thoroughly before deploying.</font>

The primary changes in this release are in the areas of authentication, permissions and page navigation. All the typical interactions have been tested, but failures may produce unexpected errors.

No special upgrade steps are required when upgrading from v1.0.4; see v1.0.4 Production Upgrading instructions when upgrading from versions prior to v1.0.4.

# Aggregate v1.0.4 Production Release #

  1. [issue 483](https://code.google.com/p/opendatakit/issues/detail?id=483) - unable to log into MySQL or PostgreSQL deployments with openId.
  1. [issue 484](https://code.google.com/p/opendatakit/issues/detail?id=484) - streaming does not function.
  1. [issue 486](https://code.google.com/p/opendatakit/issues/detail?id=486) - odk:length not respected on Google AppEngine
  1. Fix layout issue with HelpBook and misc. popups.
  1. Update all security jars and misc. others to latest releases.
  1. Update to Google AppEngine 1.6.1 SDK

## Upgrading ##

<font color='red'><b>NOTE: Google AppEngine requires special upgrade steps if you utilize <code>odk:length</code>.</b></font>

### Upgrading Google AppEngine ###
See v1.0 Production Upgrading instructions.

If none of your forms use odk:length to alter the maximum string length, then **the upgrade is complete**.

Otherwise, <font color='red'>for ONLY those forms that use odk:length</font>:
  1. Download the forms that use `odk:length` using the ODK Briefcase application.
  1. Delete the forms on Google AppEngine (this may take several minutes or hours if you have many submissions).
  1. Upload the forms from ODK Briefcase back onto your Google AppEngine instance.

### Upgrading MySQL or PostgreSQL ###
See v1.0 Production Upgrading instructions.

`odk:length` has always been respected on MySQL and PostgreSQL; there are no additional steps to be performed on those systems.

# Aggregate v1.0.3 Production Release #

**NOTE: MySQL and PostgreSQL deployments of 1.0.3 suffer from [issue 483](https://code.google.com/p/opendatakit/issues/detail?id=483)**. As a work-around, if you can live without OpenID logins for google accounts, you can install 1.0.2, configure an ODK Aggregate username with password and Site Admin privileges, then upgrade to 1.0.3 and use the ODK Aggregate username to administer your site. Otherwise, continue to use 1.0.2; the fix for this issue will be in 1.0.4.

  1. updates to make ODK Aggregate OpenRosa 1.0 compliant provided the version is an integer value.
  1. [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416) - avoid full scan of submissions on the `Submission Admin` sub-tab.
  1. [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416) - improvements to submission processing and publishing to minimize quota usage.
  1. [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416) - change attachment handling to use lazy fetches, reducing quota.  Eliminate immediate refetch upon change.
  1. [issue 468](https://code.google.com/p/opendatakit/issues/detail?id=468) - form delete consuming significant quota.
  1. [issue 478](https://code.google.com/p/opendatakit/issues/detail?id=478) - excessive quota during submission processing.
  1. [issue 478](https://code.google.com/p/opendatakit/issues/detail?id=478) - improvements to submission processing to regulate the number of background tasks launched.  Fixes to limit rate of Watchdog firings.
  1. [issue 478](https://code.google.com/p/opendatakit/issues/detail?id=478) - move upload-submission background task to run on the foreground instance.
  1. [issue 474](https://code.google.com/p/opendatakit/issues/detail?id=474) - case-sensitive hostname compare fails.
  1. update libraries to the latest Apache commons and others
  1. plumb missing quota exceptions up through services API
  1. improve logging during background task activities
  1. add logging and improve tracking of watchdog executions

## Upgrading ##

See v1.0 Production Upgrading instructions.

## Downgrading ##

See v1.0.2 Production Downgrading instructions if downgrading to a version prior to v1.0.2.

# Aggregate v1.0.2 Production Release #
  1. fix [issue 458](https://code.google.com/p/opendatakit/issues/detail?id=458) - incorrectly presenting `&frasl;` for slashes in form ids.
  1. fix [issue 457](https://code.google.com/p/opendatakit/issues/detail?id=457) - forms containing slashes or characters that should be escaped could not be deleted.
  1. fix [issue 426](https://code.google.com/p/opendatakit/issues/detail?id=426) - publishing of dates and date-time values to Google Spreadsheet and Fusion Tables -- now recognized as dates.
  1. fix bug in publishing that was using wrong date during retries. Could cause some data to not be published to Google Spreadsheets and/or Fusion Tables.
  1. fix bug in image thumbnails that improperly set the size of the thumbnail
  1. fix bug in kml export that didn't properly escape strings
  1. fix bug in Google Spreadsheet publishing that delayed publishing start
  1. log application version during start-up.
  1. adjust refresh rates of the UI pages to lessen database accesses.  [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)
  1. add over-quota error pages.
  1. plumb up to the UI all over-quota exceptions  -- as distinct from generic datastore failures.
  1. optimize all queries for GAE operations. [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)
  1. move background process (/gae/watchdog) to run in backend instance.
  1. move background activities (watchdog, publishing, csv/kml export, purge submissions and delete form) to backend instance.
  1. watchdog is now run every 40 minutes, but only when activity is occuring on the webserver.  If there is no work pending, the watchdog is not run.  [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)
  1. enable multi-threaded webserver operations (should reduce instance-hour consumption on heavily-used sites).
  1. improve low- and out-of-memory performance within GAE QueryImpl class.  Lower the pre-fetch size and reduce the fetch limit to avoid timeouts on large dataset reads.  [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)
  1. change publishing to use longer timeout values to Fusion Tables and to dynamically adjust for slower (or faster) external service connections.
  1. reduce the batch size during publishing so as to minimize unnecessary datastore reads.  [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)
  1. improve error logging during publishing and form deletion.
  1. move Form creation and cache-management into FormFactory. Reduce cache lifetime to 3 seconds. [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)
  1. better encapsulate and hide FormDefinition object within Form object.
  1. track and emit datastore query, query-read, get, put and delete usage counts (does not yet flush to log during webserver shutdown). [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416)

## Upgrading ##

See v1.0 Production Upgrading instructions.

## Downgrading ##

On Google AppEngine, should you need to roll back to an earlier release (e.g., v1.0.1 Production), you will need to manually delete the "background" backend.  This can be done through the AppEngine dashboard, via the Backends link.

On MySQL and PostgreSQL, no special action is required.

# Aggregate v1.0.1 Production Release #
  1. add 30-second cache for Form definition [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416).
  1. change GAE queue configuration to limit retries on failure [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416).
  1. add logging on datastore accesses [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416).  Disabled for performance.
  1. double watchdog interval to 6 minutes [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416).
  1. double webpage background-refresh interval to 10 seconds [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416).
  1. change the default number of records to display to 100 (from 500) [issue 416](https://code.google.com/p/opendatakit/issues/detail?id=416).
  1. fix [issue 410](https://code.google.com/p/opendatakit/issues/detail?id=410) publishing does not flush all data to external services.
  1. fix spurious error message on Form Management tab when Aggregate 1.0 has no form loaded (noted on [issue 412](https://code.google.com/p/opendatakit/issues/detail?id=412)).
  1. fix integration test breakage.
  1. fix datetime string parse error that was causing rounding errors in the millisecond digit.  Impacts all data display, extraction, publishing and Briefcase activities.

## Upgrading ##

See v1.0 Production Upgrading instructions.

# Aggregate v1.0 Production Release #
  1. add version information on Site Admin / Preferences tab
  1. fix date/time parsing problems that were affecting Briefcase data transfers.

## Upgrading ##

If you are running RC4, RC3, RC2, or RC1 (on Google's AppEngine or with MySQL or PostgreSQL), there are no special upgrade steps.  It should just work.

If you are using Beta 4, follow the upgrade instructions described under RC1.

For all other Alpha or Beta releases, you **must** either use a new appspot instance or delete ALL data in your appspot instance; if you are using MySQL or PostgreSQL with an Alpha or Beta release, you should start with an empty database.

# Aggregate v1.0 Release Candidate 4 (RC4) #
  1. update javarosa library
  1. allow null URI in resume cursor
  1. fix [issue 387](https://code.google.com/p/opendatakit/issues/detail?id=387) ([issue 380](https://code.google.com/p/opendatakit/issues/detail?id=380)) - Briefcase download generates invalid Submission XML
  1. NOTE: date/time data may not be properly parsed during Briefcase transfers.

## Upgrading ##

If you are running RC3, RC2, or RC1 (on Google's AppEngine or with MySQL or PostgreSQL), there are no special upgrade steps.  It should just work.

If you are using Beta 4, follow the upgrade instructions described under RC1.

For all other Alpha or Beta releases, you **must** either use a new appspot instance or delete ALL data in your appspot instance; if you are using MySQL or PostgreSQL with an Alpha or Beta release, you should start with an empty database.

# Aggregate v1.0 Release Candidate 3 (RC3) #
  1. fix [issue 345](https://code.google.com/p/opendatakit/issues/detail?id=345) - unable to change user passwords on Tomcat deployments
  1. fix [issue 317](https://code.google.com/p/opendatakit/issues/detail?id=317) - bookmarked pages weren't working because the hashes and query parameters were being stripped during authentication.
  1. fix [issue 375](https://code.google.com/p/opendatakit/issues/detail?id=375) - csv downloads (and kml downloads) only emited the first 500 records, not the entire dataset.
  1. filters could not be defined and/or were not working on Tomcat (MySQL, PostgreSQL) deployments.
  1. KML file exports now honor the active filter and emit only what that allows (plus the image, title and location fields).
  1. conform to OpenRosa Form Discovery API by handling the verbose and formID parameters.
  1. include a specially-built javax imageIO library that is redistributable so that Tomcat instances show thumbnail images out-of-the-box.
  1. integration tests are now working in the Maven build, using seliniumhq 2.8.0.  Coverage reports are not yet available and test suite only verifies the app starts, the superUser can log in, and a form can be uploaded.

## Upgrading ##

If you are running RC2 or RC1 (on Google's AppEngine or with MySQL or PostgreSQL), there are no special upgrade steps.  It should just work.

If you are using Beta 4, follow the upgrade instructions described under RC1.

For all other Alpha or Beta releases, you **must** either use a new appspot instance or delete ALL data in your appspot instance; if you are using MySQL or PostgreSQL with an Alpha or Beta release, you should start with an empty database.

# Aggregate v1.0 Release Candidate 2 (RC2) #
  1. improved help content, including links to youtube videos.
  1. show only "complete" submissions on the Submissions tab.
  1. row filters now properly implement hide logic
  1. now paginates results on Submissions tab.
  1. fix for Google Spreadsheets publishing issue.
  1. new "Submission Admin" tab under "Form Management" supports marking as complete an incomplete submission (incomplete submissions are those lacking one or more of their audio, image or video captures).
  1. only submissions that are marked as complete are published.
  1. media file count on Forms List tab is now clickable and displays the filename, file type and file size of each uploaded media file associated with a form.
  1. fix bug causing csv and kml downloads to not automatically open Excel and Google Earth, respectively.
  1. fix bug causing Change Password to report failures (now waits up to 15 seconds for a server response before failing this operation).
  1. fix bug causing Permissions page to only display 15 usernames.
  1. update to gdata 1.46.0 libraries

## Upgrading ##

If you are running RC1 (on Google's AppEngine or with MySQL or PostgreSQL), there are no special upgrade steps.  It should just work.

If you are using Beta 4, follow the upgrade instructions described under RC1.

For all other Alpha or Beta releases, you **must** either use a new appspot instance or delete ALL data in your appspot instance; if you are using MySQL or PostgreSQL with an Alpha or Beta release, you should start with an empty database.

# Aggregate v1.0 Release Candidate 1 (RC1) #

  1. further improvements to filters
  1. csv file export now applies the display filter active at the moment the user presses the `Export` button.
  1. restructure external services to make it easier to add new publishers
  1. restartable cursors for paging through large sets of submissions (implemented at the datastore layer, but not yet up into the UI).
  1. allow aggregate username to be designated as a superUser (not exposed in Installer; partial support in UI).
  1. updated to GWT 2.4 and AppEngine SDK 1.5.4
  1. remove Briefcase applet (functionality will be replaced by the Briefcase application).
  1. add large dataset (310,000 record) tests for restartable cursor.

Beginning with Release Candidate 1, there will be a data migration path across all further upgrades; we don't expect any further incompatible schema changes.  However, in the event that there is an incompatible schema change, you will be able to migrate your data to
a newer ODK Aggregate 1.0 instance via a Briefcase application that
can download submissions from ODK Aggregate 1.0 RC1 and onward and
upload those submissions into future ODK Aggregate releases.

An initial release of the Briefcase application will be available soon.

## Upgrading ##

To upgrade from Beta rev.4, follow the instructions below.

For all other Alpha or Beta releases, you **must** either use a new appspot instance or delete ALL data in your appspot instance before
deploying the RC1.  If you are using MySQL or PostgreSQL, you should start with an empty database.

### On GAE ###

You don't need to do anything.  It should just work.

### On MySQL or PostgreSQL ###

The persistent results tables that hold the exported csv and kml files have changed (adding support for emitting csv files filtered by the active filter).

  1. Stop Tomcat
  1. In your database's administration tool, connect to the database and:
    * `drop table _persistent_results;`
    * `drop table _persistent_result_file_bin;`
    * `drop table _persistent_result_file_ref;`
    * `drop table _persistent_result_file_blb;`
  1. copy the new WAR to the /webapps directory.
  1. Start Tomcat


# Aggregate v1.0 Beta rev.4 #

  1. split management tab into 2 tabs (Management and Site Admin).
  1. hide tabs that the user doesn't have permission to access.
  1. add help tooltips and documentation pop-ups.
  1. single-click hide/show of metadata in UI.
  1. delete individual submissions through the UI.
  1. make explicit that string fields are truncated to 255 characters.
  1. allow upload of forms that do not have data types defined for all fields (with suitable warnings).
  1. add odktables features.
  1. added a new metadata field, `_MARKED_AS_COMPLETE_DATE` that tracks the date the submission was last marked as complete.
  1. fix [issue 311](https://code.google.com/p/opendatakit/issues/detail?id=311) that causes infinite redraw loop if a form definition is corrupted.
  1. fixed several bugs related to incomplete uploaded submissions -- submissions with only some of their media attachments present.
  1. gain a global lock during instance start-up to ensure that only one instance might be changing the configuration during that time.
  1. improve GAE query and task locking logic; introduce datastore settle-time logic, remove read-after-write attempts and inserted retries in key places to improve real-world performance.
  1. extend timeouts for FusionTables to minimize publishing failures.
  1. refactor MiscTasks, PersistentResults and FormInfo to no longer themselves be form and submission structures.
  1. rework treatment of metadata so that it is more like other data.
  1. clear session cookies more completely when logging out.

## Upgrading ##

Changes to the database schema necessitate either using a clean appspot instance or deleting ALL data in your appspot instance before
deploying the beta 4.  If you are using MySQL or PostgreSQL, you should start with an empty database.

# Aggregate v1.0 Beta rev.3 utf-8 #

This fixes 4 issues:

  1. utf-8 characters are not displaying or downloading properly from a Google AppEngine server. ([Issue 286](https://code.google.com/p/opendatakit/issues/detail?id=286))
  1. filtering on metadata fields does not work. ([Issue 299](https://code.google.com/p/opendatakit/issues/detail?id=299))
  1. filtering does not work with date fields or select1 fields. ([Issue 299](https://code.google.com/p/opendatakit/issues/detail?id=299))
  1. insert a 3 second delay in order for the datastore to settle before streaming newly-submitted data records to external services.  TBD: This may not be enough time when GAE datastore is having performance issues.

This update also exposes the "**meta-is-complete**" metadata on a submission.  If a submission with a media attachment (e.g., image, audio recording or video capture) is uploaded but the attachment is not, the submission will be marked as not complete (false).  Only completed submissions are published to external services.

## Upgrading ##

If you are running Beta rev.3, you should just be able to deploy this on top of that installation.  If UTF-8 characters do not display properly in your form definition, you will need to delete the form (and submitted data) then upload the form anew.

Otherwise, if you are not running Beta rev.3, then follow the steps required for upgrading to Beta rev.3 prior to moving to Beta rev.3 utf-8.

# Aggregate v1.0 Beta rev.3 #

## Upgrading ##
To upgrade from earlier Alpha or Beta releases, you **must**...


### On GAE ###

  1. If running builds prior to Beta 2 on Google AppEngine cloud services, any forms with Decimal data will need to be deleted and reloaded.  Decimal data had been represented as strings and is now represented properly as double-precision numbers in Google AppEngine.  Note: while Briefcase can be used to download data, there is currently no way to upload your downloaded data into the beta 3.
  1. Disable writes to the datastore (via Application Settings / Disable Datastore Writes )
  1. Deploy to GAE.
  1. Delete all the records in these kinds (using Datastore Viewer):
    * `opendatakit._granted_authority_hierarchy`
    * `opendatakit._registered_users`
    * `opendatakit._user_granted_authority`
  1. Disable your application (via Application Settings / Disable Application )
  1. Enable writes,
  1. Enable your application,

### On MySQL or PostgreSQL ###

  1. Stop Tomcat
  1. In your database's administration tool, connect to the database and:
    * `drop table _granted_authority_hierarchy;`
    * `drop table _registered_users;`
    * `drop table _user_granted_authority;`
  1. copy the new WAR to the /webapps directory.
  1. Start Tomcat

### Upgrade Impacts ###

The primary impact is the loss of all registered users and their privileges (what the manual steps above do).  Beta 3 changes the user configuration.  Registered users are now either gmail accounts or ODK Aggregate usernames.  ODK Aggregate now supports only anonymous access and/or registered users.

## Known Issues ##
  * [issue 265](https://code.google.com/p/opendatakit/issues/detail?id=265) -- IE 8 doesn't refresh the submissions page on adding or deleting forms.
  * [issue 261](https://code.google.com/p/opendatakit/issues/detail?id=261) -- logins can time out while sitting on permissions page, causing unsaved changes to be lost.
  * [issue 231](https://code.google.com/p/opendatakit/issues/detail?id=231) -- large submission sets may cause UI to time out.

## Changelist ##

  * recognize OpenRosa `meta/instanceID` tag and use it if present.  Generalize to use the first `meta/instanceID` as the unique ID for a submission (don't require a namespace).
  * change-password now uses https when available (JSOP).
  * digest auth (ODK Aggregate logins) now have nonce timeout set to 30 minutes.
  * better logging of submissions and reasons for failures (for GAE diagnosis, since ODK Collect doesn't necessarily report this)
  * disable or hide UI functionality if user does not have required permissions for that functionality.
  * update to AppEngine SDK 1.5.1
  * update installer screens; https automatic on GAE deployment.
  * installer now deletes its temp directory
  * update security model (extensive changes)
  * hide password when deploying to Appspot ([issue 222](https://code.google.com/p/opendatakit/issues/detail?id=222))
  * fix launching of scripts and display of README on windows ([issue 223](https://code.google.com/p/opendatakit/issues/detail?id=223))
  * show the number of media files associated with a form ([issue 224](https://code.google.com/p/opendatakit/issues/detail?id=224))
  * fix crash on MacOS when some fonts are present ([issue 236](https://code.google.com/p/opendatakit/issues/detail?id=236))
  * fix lockouts when changing super-user ([issue 242](https://code.google.com/p/opendatakit/issues/detail?id=242))
  * hide forms that are queued for deletion from the UI ([issue 255](https://code.google.com/p/opendatakit/issues/detail?id=255))
  * remove the old UI (servlet) pages.
  * remove old applets
  * fix operations behind proxies ([issue 212](https://code.google.com/p/opendatakit/issues/detail?id=212))
  * fix lingering UTF-8 character display issues ([issue 218](https://code.google.com/p/opendatakit/issues/detail?id=218), 215)
  * fix new form and upload submissions links ([issue 243](https://code.google.com/p/opendatakit/issues/detail?id=243); 257) and IE page display and upload submissions bug.
  * fix PostgreSQL create-database scripting errors. ([issue 256](https://code.google.com/p/opendatakit/issues/detail?id=256))
  * changes to improve client-side javascript operations, especially the refresh timer operations.
  * changes to poll for configuration changes so that instances running in multi-server farms all eventually synchronize their security config and forms list.
  * changes to get working (depressingly...) minimal unit tests for gae, mysql, postgresql under maven.


# Aggregate v1.0 Beta rev.2 #

  * fixes for Tomcat operations (missing images, styles)
  * updates to tabs, stylesheets and layout of new UI
  * configuration wizard improvements ([Issue 222](https://code.google.com/p/opendatakit/issues/detail?id=222), 223)
  * Postgresql install works ([issue 227](https://code.google.com/p/opendatakit/issues/detail?id=227))
  * updated to AppEngine SDK 1.5.0.1
  * add Tomcat image preview bean
  * simplified permissions tab
  * change Google AppEngine decimal value storage to use double.

Limitations:
  * will only display 1000 records
  * will only export 1000 records to csv and kml files
  * publishing broke as of Oct 2011.

To work around the publishing failures and the 1000-record limitation, use the Briefcase applet to download the data to a local csv file.  Access the Briefcase applet through the URL:
```
https://your-application-id-here.appspot.com/Briefcase
```
# Aggregate v1.0 Beta #

Useful URLs during the install process:

[Google AppEngine](https://appengine.google.com/)

[Apache Tomcat 6](http://tomcat.apache.org/download-60.cgi)

[PostgreSQL 9.0](http://www.postgresql.org/)

[MySQL 5.1 Community Edition](http://www.mysql.com/)

## Installer Special Instructions ##

You must have the Java runtime environment (JRE) installed.  To download:
  * Browse to: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  * Choose the **Download JRE** button.
  * Follow the instructions for installing the JRE.

## Installer Known Issues ##
  * Can only select install folder; can't create it.

## Aggregate 1.0 Beta Known Issues ##
  * Security and Multiple-Running-Instance Concerns -- see AggregateSecurity

## Aggregate 1.0 Beta Feature Requests ##
  * Need indication of whether a form has associated media files

## Determining Aggregate 1.0 Version ##

n the 1.0 Production release, the Version appears on the Site Admin Tab, under the Preferences sub-tab.

Publishing to Google is broken prior to RC2 (due to a change in Google infrastructure over the summer).

Here's my cheat sheet:

  * Beta 1 and earlier: permissions tab looks nothing like the tab in the Production release.
  * Beta 2: permissions tab looks like the Production release; Forms tab does not show the number of media files associated with a form.
  * Beta 3: display the number of media files associated with a form; publishing to Google is broken.
  * Beta 4: there are "Form Management" and "Site Admin tabs; publishing to Google is broken.
  * RC1: csv exports apply the active Filter to the exported columns; publishing to Google is broken.
  * RC2: submissions tab is paginated (shows 500 records per page by default); publishing to Google works.
  * RC3: kml exports apply the active Filter to the exported data.  exports all data -- no longer truncates to the first 500 records.
  * RC4: submissions for forms with non-repeating groups are now properly reconstructed into Briefcase. date fields are broken.
  * Production: Version info is shown on the Site Admin / Preferences sub-tab; date fields work with Briefcase.