# Introduction #

You are investigating why something is not working or you've found an issue and the ODK team would like to look at your AppEngine logs and tables to diagnose the problem.

# Investigating a problem on AppEngine #

Aggregate writes many lines of progress and activity information to its application log.  You can look in these logs for errors and warnings that might indicate why your application is not performing as expected.

There are 2 sets of Logs produced by AppEngine:
  * The frontend logs track actions performed through the webpages, submission and download actions of ODK Collect and ODK Briefcase, and the publishing of data to external services (e.g, Google Spreadsheet or Google Fusion Tables).  Keep in mind that there can be up to a 60-second delay from receiving a submission to publishing that data to an external service.
  * The backend logs (i.e., the 'background' version) report actions performed through the built-in periodic watchdog process (that restarts any publishing tasks or file exports that have failed) and all export-to-file actions (export to CSV, KML or as JSON file formats).

To access your application logs:
  * Go to your dashboard via: https://appengine.google.com/
  * Choose your application id.
  * Click on "Logs",
  * To view the frontend log, choose "Version" and select '1' or choose 'All Versions' to look at all frontend logs (this choice excludes the backend logs).
  * To view the backend log, choose "Version" and select 'background'
  * Choose 'Show:' Logs with minimum severity: 'Error'
  * Expand 'Options'
  * Select 'Search'

You will likely see some logging entries for the time period in which you experienced the errors.

You may be able to correlate these errors with performance problems reported on the Google Appspot 'System Status' page here: http://code.google.com/status/appengine

# Inviting Someone to be a Developer #

Go to https://appengine.google.com and select the application id you're having trouble with.  This presents the **Dashboard** for this application.

On the left sidebar, select **Permissions** under the **Administration** heading.

Enter the full email address (e.g., mitchellsundt@gmail.com) under the **Invite a user to collaborate on this application** heading.

Choose **Developer** as the **Role**.

Click **Invite User**

Please also send an e-mail identifying the appengine application id and issue.  While we can correlate problems with invitations by looking at the administrators of the application, it helps if we have an e-mail from you identifying your application id and issue.

# Adding Someone as Site Admin #

Log onto your ODK Aggregate instance using the super-user e-mail or as a user with Site Administrator privileges.

Click on the **Site Admin** tab, and select the **Permissions** sub-tab.

In the text area under the **Add Users** heading, enter the full e-mail of the user being added (e.g., mitchellsundt@gmail.com).

Click **Add**

In the table under **Edit Users**, check the checkbox under the **Site Administrator** column heading beside the user you've added.

Click **Save Changes**

Please also send an e-mail identifying the appengine application id and issue and notifying us that we've been added as site administrators.  It is the only way we'll know that this has been done.

# Repairing your AppEngine Database Yourself #

This is occasionally necessary because Google can kill the server process during a form update before the newer form definition is fully processed, or before the older form definition is fully removed from the system.

The same thing can happen during the submission of a filled-in form.

These failures are more likely to occur if you are trying to run using just free quota, or if you are heavily using your system and the form being uploaded is large or contains many repeat groups.

If this disruption occurs while uploading a form definition to ODK Aggregate, the symptom is an infinite refresh of the ODK Aggregate website page.

If the disruption occurs while uploading a filled-in form to ODK Aggregate,
any publishers or attempts to Export to CSV, or use ODK Briefcase to pull that data will fail with an error message like:
```
Error: Problem persisting data or accessing data (roster2@parentAuri(uuid:dd119aa7-447d-4dc0-8262-f4d2d409c5d9) is missing a (repeat) group instance OR has an extra copy of one)
```

If a filled-in form is corrupted, you can repair the corrupted submission yourself.

If a form definition is corrupted (and you are seeing infinite refreshes), we recommend that you give us developer access to allow us to repair your database. If you don't want to give developer access (access to all of your data), then you can follow the steps below to repair the database on your own.

## Repairing the Form Definition Table ##

<font color='red'>If you erroneously delete a record, your previously-collected data for that form, in all likelihood, will be lost.</font> We strongly recommend giving us developer access to do this, unless you are very systematic and detail-oriented.

The steps are as follows:

Go to the appengine dashboard for your app via https://appengine.google.com/

Click on 'Datastore Viewer' on the left side panel

The Kinds (data tables) that you will need to look at and clean up are:

  * `opendatakit._form_info`
  * `opendatakit._form_info_fileset`
  * `opendatakit._form_info_xform_bin`
  * `opendatakit._form_info_xform_ref`
  * `opendatakit._form_info_xform_blb`
  * `opendatakit._form_info_submission_association`

(1)  select the `opendatakit._form_info_submission_association`  Kind (data table).

There will be 2 records in this table for one form id (`submission_form_id`) -- this is the form that needs to be repaired.

Look at the `is_persistence_model_complete` field of these records.

Whichever one is FALSE should be removed -- the system was interrupted while processing that form definition and that definition cannot be repaired, it must be removed.
If both records are TRUE, then the newer form definition was successfully uploaded -- you want to remove the older one.

Make note of the creation timestamps of both records, and the ID/Name of the record that needs to be removed.

The records in the other tables with similar older/newer creation timestamps are the ones you need to delete.

(2) go to `opendatakit._form_info` Verify that there is only one entry in this table for that form id.  This is just a sanity check.  Make note of the ID/name of this record -- the md5:2342asa... portion.

(3) go to `opendatakit._form_info_fileset` -- find the records that match the `_parent_auri` with this md5:... value of the `opendatakit._form_info` record.  Determine which one correspond to the record of the `opendatakit._form_info_submission_association` table that needs to be removed..

Make note of the ID/name ( particularly the uuid:234d-... part) of the two entries -- and the creation timestamp. We want to correlate the records by their approximate creation timestamps.

(4) go to `opendatakit._form_info_xform_bin` -- match the records here via `_parent_auri` and the ID/name of the records in the `opendatakit._form_info_fileset`.  Record creation timestamps

(5) go to `opendatakit_form_info_xform_ref` -- match the records here via the `_dom_auri` field and the ID/name of the `opendatakit._form_info_xform_bin` entry (above). Record creation timestamps  Record the `_sub_auri` field. If your forms are extremely large, there may be multiple entries in this table sharing the same `_dom_auri`. Confirm that you have found them all.

(6) go to `opendatakit._form_info_xform_blb` -- match the records here via their ID/name and the `_sub_auri` field of the `opendatakit._form_info_xform_ref` records above. Record the creation timestamps.

(7) Now, figure out the set of records that need to be deleted from these tables such that the newest TRUE `opendatakit._form_info_submission_association` entry is preserved.

(7) Go through, starting at `opendatakit._form_info_fileset`, and delete all the records that need to be deleted.

## Reparing a Filled-in Form Submission ##

The steps are as follows:

(1) Open your AppEngine dashboard here: https://appengine.google.com/ And click on the 'Logs' link in the left sidebar. Then choose to view logs with errors. On AppEngine, there are two `Versions` of the server. You need to look at the logs for both the version **1** and version **background**. If running on Tomcat or another webserver, look in your server logs.

(2) If you are running ODK Aggregate 1.4.5 or higher, there will be a detailed error message in them indicating the exact location of the problem. Something like:
```
Error: Problem persisting data or accessing data [ChoiceSubmissionType:getValueFromEntity] SQL: SELECT * FROM opendatakit.YOUR_DATA_TABLE_NAME WHERE _PARENT_AURI = XXXXXX ORDER BY _PARENT_AURI
```
Make note of all such detailed errors in your log file. In particular, you need to make note of all YOUR\_DATA\_TABLE\_NAME and XXXXXX values. In most cases, `_PARENT_AURI` will be referenced. In some cases, there might be two clauses, `_TOP_LEVEL_AURI` and `_PARENT_AURI`, and in others, there might be a reference to `_DOM_AURI`. Make note of all of these variations.

(3) Click on the Datastore Viewer link in the left sidebar. Then click on the link for the 'new Datastore Query page'. This will take you to a URL like:
```
https://console.developers.google.com/project/YOUR_APPLICATION_ID/datastore/query?src=acinvite&versionId=1&moduleId=default
```

(4) Select Query using GQL. For non-AppEngine installations, open your database admin tool (e.., MySQL Workbench or PgAdmin).

(5) Now, for each data table name and XXXXX value in step (2), execute a query of the form:
```
SELECT * FROM `opendatakit.YOUR_TABLE_NAME` WHERE _PARENT_AURI = "XXXXXX"
```
Take special note of the backquote around the opendatakit.-qualified table name, and the double-quotes around the XXXXXX value.  If the original query specified `_DOM_AURI` instead of `_PARENT_AURI`, use that. If this query does not return a value, you may also need to add the `AND _TOP_LEVEL_AURI = "YYYYY"` qualifier (but that should not be necessary), if it was present in the original error message.

(6) Look at the `_ORDINAL_NUMBER` column. There should be exactly one entry for a given ordinal number. The error occurs because there are two or more rows for at least one of these ordinal numbers.

(7) Select the rows with the earliest (oldest) `_LAST_UPDATE_DATE` and choose 'Delete'

(8) Use ODK Briefcase to Pull data from your server. If it fails, repeat all these steps. Continue repeating until ODK Briefcase completes without errors.

If your form has many repeat groups or multiple-choice questions, there may be a considerable number of duplicates.