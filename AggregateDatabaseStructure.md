# Introduction #

This is a developer document. It is an attempt to document the database structure of Aggregate v1.x & v2.x.

<font color='red'>DATABASE STRUCTURAL REVISION NOTICE:</font> Beginning with changeset http://code.google.com/p/opendatakit/source/detail?r=56814c83118bc6d17a2323d206d202b273591b19&repo=aggregate the `VersionedBinaryContent` table has been removed from the data model.  It's fields have been moved into the `BinaryContent` table.  Also with this changeset, the `BinaryContentManipulator` class has been added to handle the details of accessing the binary content in the database.  A more recent change is the creation of the `org.opendatakit.common.ermodel` package and its classes to more easily manipulate database tables in Aggregate.

# Overview #

The table structure for Aggregate v1.0 is the same in Google `BigTable` (Google App Engine-hosted systems) and on `MySQL` and `PostgreSQL` database system.

The guiding principles of Aggregate 1.0 data model are:

  * Support the storage of arbitrary xforms with minimal restrictions on xform content or size.

  * Store integer and decimal values as integer and numeric datatypes in the underlying database so that data visualization software can directly access the underlying database and meaningfully manipulate the data without requiring any custom coding.  This will also support the eventual enhancement of aggregate to provide native data summarization and visualization tools.

  * Do not confound data from multiple xforms.  Data for each xform is maintained in its own independent set of tables.

  * Do not confound binary data from multiple fields in an xform.  Each element containing a binary attachment (image, audio, video, etc.) is stored in a separate tableset.  So if you have a form with images of the mother and of the father, the mother's image will appear in one binary content tableset, and the father's image will appear in a different binary content tableset.  They won't be comingled into a single binary content bucket (this is a change from Aggregate v0.9.x).  See binary content explanation later in this page.

  * Make it "just work."

  * Support generation of csv and kml output files for all of, or for a subset of, the submitted data for data manipulation by end-users and data visualizations through Google Earth.

  * Support reliable, restartable, publication of all or a subset of data to external services such as Google Spreadsheet, Google `FusionTables`, and eventually arbitrary third party services via POSTs.

# Details #

System tables all start with a leading underscore.  User-uploaded forms will have table names that start with an alphabetic character.

Similarly, within a table holding data for a user-uploaded form, the columns required by the system (metadata and internal columns) will begin with a leading underscore and the columns corresponding to elements (tags) in the xform will begin with an alphabetic character.

Additionally, on Google AppEngine, ODK Aggregate 1.0 table names are prefixed with "opendatakit." so as not to collide with ODK Aggregate 0.9.x tables.

The top-level table for a user-uploaded xform will end with `_CORE` or `_COREn`, where 'n' is a number. The name of the top-level table is roughly formID + "`_`" + groupName + "`_CORE`".  But that is all shrunk down to be less than 64 characters, so if you have a long form id, it will be compressed into a shorter string.  The same compression is done for column names.  If you have an xform with nested non-repeat groups, the full path of the group is preserved, but similarly compressed.

The form fields are integer, decimal and string values in the database so you can use standard visualization tools to analyze your data.

The actual table name may vary from one instance to the next depending upon the tables defined in the database at the time the form was uploaded; the server ensures that the table names do not collide and modifies table names until there are no collisions. Also, because of column count and row size limitations, larger forms, such as `eIMCI` will be split across multiple tables. `eIMCI` uses more than 6 tables, for example.  The software first attempts to split the xform data following the groupings within the xform.  Consider, for example, an xform representing a medical record where you have a group describing the provider, a group describing the patient, and a group describing the reason for the visit and the treatments proscribed.  If this information cannot fit within one database table, the system will first attempt to split it into 3 tables, one holding the characteristics of the provider, one holding the characteristics of the patient, and one holding the visit information because the xform had that structure.

The mapping of form values to table columns is maintained in the `_FORM_DATA_MODEL` table.  The `PERSIST_AS_...` columns in that table identify the schema, table name, and column name where the form element is stored.  The `ELEMENT_NAME` and `ELEMENT_TYPE` identify the xforms tag name and type of the form element.  The nesting of the element inside other elements, including repeat groups is determined by the chaining backward into this table through the PARENT\_URI\_FORM\_DATA\_MODEL column, which identifies the `_URI` of the form element inside which this element is nested.

xform elements containing multiple-choice answers and binary content are stored in auxillary tables.

Multiple-choice data is stored such that each record in the multiple-choice table holds a link back to the row of the table in which it is associated.  The choice value is assumed to be a string (this is the only instance where the type of the value is assumed to be a string even if it might be numeric).

Binary content is represented in 3 tables chained together downward, from top to bottom.

```
_bin -> _ref -> _blb
```

This set of 3 tables stores one binary data field in the form.  Other binary data fields are stored in their own unique set of 3 tables.  So you don't need to worry about figuring out what field the data corresponds to -- that's determined from the table name.

```
<formid>_<binaryfieldname>_bin
<formid>_<binaryfieldname>_ref
<formid>_<binaryfieldname>_blb
```

The naming of the table refers to what it describes:

`_bin` -- Binary content (`BinaryContent.java`) -- holds the filename (if any) of the binary data and the content length, content type and content hash for the binary attachment.

Binary content fields within an xform can have only one binary attachment per field, but this table allows multiple binary attachments within a single field.  This is currently used for multimedia forms, where the files in the media directory are stored in one binary content field as separate records (distinguished by their ordinals).

`_ref` -- Binary content referencing a Blob (`BinaryContentRefBlob.java`) -- for a given binary attachment, maintains the ordering of the segments of the `_blb` table that are used to reconstruct the blob.  (`_DOM_AURI` == PK into `_bin`; `_SUB_AURI` == PK into `_blb`; PART = 1..n the ordering of the `_blb` record in the reconstruction of the blob).

`_blb` -- Ref Blob (`RefBlob.java`) -- a segment of the binary data (VALUE).

# System Tables #

The tables used in the operation of the system are as follows:

## Form Representation ##

`_form_data_model` -- discussed above.

`_form_info_submission_association` -- mapping table used to look up a submission's (form id, version, uiVersion) and map that to the definition within `_form_data_model` describing the tables holding those submissions on this server.

`_form_info` -- identifies all the form ids known to the system

`_form_info_fileset` -- identifies the form files and details associated with a form id.

`_form_info_xform_bin`

`_form_info_xform_ref`

`_form_info_xform_blb` -- stores the form definition xml file.

`_form_info_manifest_bin`

`_form_info_manifest_ref`

`_form_info_manifest_blb` -- stores all the media files associated with the form definition.


## User-defined Filters ##

`_filter`

`_multi_column_filter`

`_filter_group` -- store user-defined data filters.

## Background Tasks ##

`_misc_tasks` -- tracks background tasks for form deletion, purging of submissions and external services configuration tasks (e.g., google worksheet creation).

`_persistent_results` -- tracks all export requests.

`_persistent_result_file_bin`

`_persistent_result_file_ref`

`_persistent_result_file_blb` -- stores the persistent result (csv or kml) file generated by the export request.

`_form_service_cursor` -- tracks all publishing-to-external-services requests.

`_fusion_table`

`_fusion_table_repeat` -- fusion table specific parameters

`_google_spreadsheet`

`_google_spreadsheet_repeat` -- google spreadsheet specific parameters

## Permissions Model ##

`_registered_users`  -- list of all known users of the system

`_user_granted_authority` -- permissions directly assigned to each user

`_granted_authority_hierarchy` -- permission inheritance tree

`_security_revisions` -- used for inter-process signalling of permissions changes

## Server Preferences ##

`_server_preferences` -- settings that need to be kept somewhere.

## Backend processing ##

`_backend_actions` -- used to manage Watchdog background activity under AppEngine and log the start times of the Watchdog everywhere.

## Low-level System-wide Mutex ##

`TASK_LOCK` -- AppEngine table used to coordinate distributed global locks across all threads of execution.

`_task_lock` -- table used on PostgreSQL and MySQL for the same purpose.

## Datastore utilization ##

`_COST_LOGGING_` -- only on AppEngine.  Used for dynamic control of the logging of expensive queries.

## Session ##

`_ah_SESSION` -- AppEngine table holding the set of user browser sessions to the webserver (session cookies).