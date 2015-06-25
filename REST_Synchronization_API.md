# Introduction #

The ODK 2.0 tools utilize a REST API to exchange configuration and data values with the server.

<font color='red'>This is a preliminary description of this API. It will change up until we reach Release Candidate.</font>

# REST URL formats #

This document summarizes the API and the usage of the API. The URLs for the REST API have a common URL prefix. E.g.,
```
https://hostname:port/path/of/prefix/
```
That is assumed to be supplied by a configuration setting.

When describing the REST URL, path elements surrounded by curly braces (`{}`) indicate the use of the value for that term in that location within the path. There are a handful of these substitution terms used within the REST URLs. The most common of these are:

  * `appId`-- identifies the 'application', which is a collection of configuration files and data tables that provide a self-contained user experience. e.g., a survey campaign, a specific set of workflows, etc.
  * `tableId` -- identifies a particular data table.
  * `schemaETag` -- identifies a particular manifestation of a table. If you drop the table and recreate it, the re-creation will have a different `schemaETag` that the original table, even if it is otherwise identical.  In contrast, adding, updating or deleting individual rows in a table does not change the `schemaETag` for that table.
  * `rowId` -- the primary key for a particular row within a table.
  * `rowETag` -- identifies a particular revision of a row within a table.

When defining the REST api, we use modified version of the JAX-RS annotations to describe the interface. For example, the API to create a table on the server is described as:
```
  @PUT
  @Path("{appId}/tables/{tableId}")
  @Consumes({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*TableResource*/ createTable(TableDefinition definition)
      throws ODKDatastoreException, 
             TableAlreadyExistsException, 
             PermissionDeniedException, 
             ODKTaskLockException;
```

`@PUT`, `@POST` or `@GET` indicate the type of HTTP request.

`@Path` indicates the URL path to invoke this method, with the curly brace substitutions of the indicated substitution terms.  This is appended to the common URL prefix provided by the configuration setting.

`@Consumes` indicates the mime types of message bodies accepted by the server. In general, the server accepts JSON and XML in UTF-8 format; JSON is preferred.

`@Produces` indicates the mime types of the message bodies returned to the client. In general, the server can return JSON or XML in UTF-8 format; JSON is preferred.

`@GZIP` above the method declaration indicates that the response message body may have a GZIP content encoding (at its discretion, the server's transport framework may apply gzip compression prior to sending the response to the client).

`@GZIP` before the argument of the method indicates that the request message body may have a GZIP content encoding (at its discretion, the client's transport framework may apply gzip compression prior to sending the request to the server).

For brevity, the method will only have zero or one arguments, indicating the data type of the body of the request (in this case, the body of the HTTP PUT request is a Row object).

The return type is indicated in a comment. The `Response` return type is a generic response type that encapsulates both the successful return type (`RowResource` in this example) and the error codes for the various exceptions.  As this API gets fleshed out, the error codes for each specific exception will be documented at the bottom of this page.

# REST Data Structures #

We use Jackson 2.0 for transforming Java objects to and from XML and JSON representations. To understand the representations, it is best to use curl or any other REST client to send requests to the server and view the returned structures.

In the following presentation, we provide the Jackson 2.0 annotations used in our code.

# Data Groupings #

Before discussing the API, it is useful to identify the data on the system. The ODK 2.0 tools assume all data fall into one of four groupings:

  1. HTML, javascript and tool configuration files that are not specific to any data table. These include custom home screens, css, logo icons, and settings for the tools (e.g., default font size, what settings options to show or hide).
  1. Data table definition, properties, HTML and javascript associated with a specific data table. These include all ODK Survey forms used to create or edit this data table, ODK Tables HTML and CSS files for list views, map displays and graphical displays of the data, and ODK Scan mark-sense form definitions.
  1. Data rows and the file attachments (e.g., images, audio, video or other files) associated with specific revision(s) of each data row.
  1. Other files and data that are not synchronized with the server; e.g., the tools' internal configuration files and underlying databases; user preferences settings.

## App-Level vs Table-Specific Files ##

A naming convention identifies which files should not be sync'd to the server, which files are specific to a particular data table, which files are attachments for an individual row in a data table, and which are not (distinguishing between **Data Grouping #4**, **Data Grouping #2**, **Data Grouping #3** and **Data Grouping #1**).

<font color='red'>The naming convention is in flux as the directory structure on the client will be changing.</font>

### Future Directory Structure ###
The new directory structure will have 3 work areas, `config`, `data`, and `system`:

`config`:
  * `/sdcard/opendatakit/{appId}/config/assets` -- home screen HTML, app-wide CSS and initial settings
  * `/sdcard/opendatakit/{appId}/config/assets/csv` -- data files that can be pre-loaded into the local database upon re-initialization. More specifically:
    * `/sdcard/opendatakit/{appId}/config/assets/csv/{tableId}.csv`
    * `/sdcard/opendatakit/{appId}/config/assets/csv/{tableId}/*`
    * `/sdcard/opendatakit/{appId}/config/assets/csv/{tableId}.{qualifier}.csv`
    * `/sdcard/opendatakit/{appId}/config/assets/csv/{tableId}.{qualifier}/*`
  * `/sdcard/opendatakit/{appId}/config/tables/{tableId}/*` -- HTML, CSS and forms for a given data table

`data`:
  * `/sdcard/opendatakit/{appId}/data/db` -- common database (shared across all ODK 2.0 tools)
  * `/sdcard/opendatakit/{appId}/data/attachments/{tableId}/{rowId**}/` -- attachments specific to a given row and data table
  * `/sdcard/opendatakit/{appId}/data/output/csv` -- exports from the data tables are placed here.

`**` rowId has non-alpha-numeric symbols replaced with underscores when constructing this path.

`system`:
  * `/sdcard/opendatakit/{appId}/system/framework` -- files needed for tool operations (updated as each tool evolves)
  * `/sdcard/opendatakit/{appId}/system/logging` -- log files

Everything under '.../config' is either **Data Grouping #1** or **Data Grouping #2**.

Everything under '.../data' is **Data Grouping #3**.

Everything under '.../system' is **Data Grouping #4**

### Current Directory Structure ###
The current directory structure is more muddled:
  * /sdcard/opendatakit/{appId}/assets  -- home screen HTML, app-wide CSS and initial settings
  * `/sdcard/opendatakit/{appId}/assets/csv` -- data files that can be pre-loaded into the local database upon re-initialization. More specifically:
    * `/sdcard/opendatakit/{appId}/assets/csv/{tableId}.csv`
    * `/sdcard/opendatakit/{appId}/assets/csv/{tableId}/*`
    * `/sdcard/opendatakit/{appId}/assets/csv/{tableId}.{qualifier}.csv`
    * `/sdcard/opendatakit/{appId}/assets/csv/{tableId}.{qualifier}/*`
  * `/sdcard/opendatakit/{appId}/config/tables/{tableId}` -- HTML, CSS and forms for a given data table (slowly changing)
  * `/sdcard/opendatakit/{appId}/forms.old` -- obsolete
  * `/sdcard/opendatakit/{appId}/framework` -- files needed for tool operations (updated as each tool evolves)
  * `/sdcard/opendatakit/{appId}/framework.old` -- obsolete
  * `/sdcard/opendatakit/{appId}/logging` -- log files
  * `/sdcard/opendatakit/{appId}/metadata` -- common database
  * `/sdcard/opendatakit/{appId}/output/csv` -- exports from the data tables are placed here.
  * `/sdcard/opendatakit/{appId}/tables/{tableId}/` excluding `instances` -- HTML, CSS and forms specific to this table.
  * `/sdcard/opendatakit/{appId}/tables/{tableId}/instances/{rowId}` -- attachments specific to a given row and data table.

`**` rowId has all non-alpha-numeric symbols replaced with underscores when constructing this path.

### Table-level vs App-level files ###
In both the new and current directory structures, all table-level files are either located under:
```
.../tables/{tableId}/
```
(excluding the `instances` folder in the current structure). Or, they are files or directories under the `assets` folder:
```
.../assets/csv/{tableId}.csv
.../assets/csv/{tableId}/*
.../assets/csv/{tableId}.{qualifier}.csv
.../assets/csv/{tableId}.{qualifier}/*
```

All other app-level files (excluding the table-level files above) are in:
```
.../assets/*
```

# Overall Sync Workflow #

The overall sync workflow is:

  1. ensure that the device's set of files and the tools configuration not specific to any table (**Data Grouping #1**) exactly matches that on the server -- removing any files on the device that are not on the server.
  1. for each table, ensure that the device's table definition and table-specific configuration  (**Data Grouping #2 part A**) exactly matches that on the server and that all the files and configuration specific to that table exactly matches those on the server -- removing any extraneous files on the device.
  1. for each table on the device that is not on the server, delete that table and its table-specific files ( (**Data Grouping #2 part B**). After this step, the table configuration on the device exactly matches that of the server.
  1. for each table, perform a bi-directional sync of the data and file attachments for the rows of that table  (**Data Grouping #3**).

## Data Grouping #1 REST Synchronization API ##

The sync workflow for this step is:

  1. obtain a manifest of the application-level files suitable for this client device.
  1. compare the application-level files on the device against the manifest entry. If different, download the file, if not present on the server, delete it.

### Substitution Term `odkClientVersion` ###

The `odkClientVersion` substitution term enables different sets of files to be delivered to different clients. The primary need for this is for configuration settings files that must be linked to a specific version of an installed tool (APK), or for HTML files that invoke a javascript API exposed by a specific version of a tool (APK), so that the appropriate implementation of that interface is used for the specific version of the tool (APK) present on the device.

The usage of this term is not yet clear. For now, it is always '1'.

This term is limited to 10 characters in length.

### Manifest REST Api ###

```
  @GET
  @Path("{appId}/manifest/{odkClientVersion}")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*OdkTablesFileManifest*/ getAppLevelFileManifest();
```

Requests the manifest of all app-level files for an `appId` and `odkClientVersion`.

<font color='red'>NOTE: The XML representation for these is in flux. JSON is least likely to change.</font>

The data structure returned is:
```
@JacksonXmlRootElement(localName="manifest")
public class OdkTablesFileManifest {

  /**
   * The entries in the manifest.
   */
  @JacksonXmlElementWrapper(useWrapping=false)
  @JacksonXmlProperty(localName="file")
  private ArrayList<OdkTablesFileManifestEntry> files;

}
```

and here:

```
public class OdkTablesFileManifestEntry {

  /**
   * This is the name of the file relative to 
   * the either the 'config' directory (for 
   * app-level and table-level files) or the 
   * row's attachments directory (for row-level
   * attachments). 
   * 
   * I.e., for the new directory structure,
   * if the manifest holds configpath files, it is under:
   *   /sdcard/opendatakit/{appId}/config
   * if the manifest holds rowpath files, it is under:
   *   /sdcard/opendatakit/{appId}/data/attachments/{tableId}/{rowId}
   */
  public String filename;

  @JsonProperty(required=false)
  public Long contentLength;

  @JsonProperty(required=false)
  public String contentType;

  /**
   * This is the md5hash of the file, which will be used
   * for checking whether or not the version of the file
   * on the phone is current.
   */
  @JsonProperty(required=false)
  public String md5hash;

  /**
   * This is the url from which the current version of the file can be
   * downloaded.
   */
  @JsonProperty(required=false)
  public String downloadUrl;
}
```

e.g., for JSON:
```
{
  "files": [
    {
      "filename": "assets\/js\/plotter-home.js",
      "contentLength": 1014,
      "contentType": "application\/x-javascript",
      "md5hash": "md5:55e0d425189730cb0c2ba5dde258c509",
      "downloadUrl": "http:\/\/172.28.7.23:8888\/odktables\/tables\/files\/1\/assets\/js\/plotter-home.js"
    },
    {
      "filename": "assets\/hope.html",
      "contentLength": 2239,
      "contentType": "text\/html",
      "md5hash": "md5:0686d088ef651f4f4370c78da06dcfe5",
      "downloadUrl": "http:\/\/172.28.7.23:8888\/odktables\/tables\/files\/1\/assets\/hope.html"
    }
  ]
}
```
e.g., for XML:
```
<manifest>
    <file>
        <filename>assets/js/plotter-home.js</filename>
        <contentLength>1014</contentLength>
        <contentType>application/x-javascript</contentType>
        <md5hash>md5:55e0d425189730cb0c2ba5dde258c509</md5hash>
        <downloadUrl>http://172.28.7.23:8888/odktables/tables/files/1/assets/js/plotter-home.js</downloadUrl>
    </file>
    <file>
        <filename>assets/hope.html</filename>
        <contentLength>2239</contentLength>
        <contentType>text/html</contentType>
        <md5hash>md5:0686d088ef651f4f4370c78da06dcfe5</md5hash>
        <downloadUrl>http://172.28.7.23:8888/odktables/tables/files/1/assets/hope.html</downloadUrl>
    </file>
</manifest>
```
### Download App-Level File REST API ###

```
  @GET
  @Path("{appId}/files/{odkClientVersion}/{filePath:.*}")
  @Produces({"*"})
  public Response getFile(@QueryParam("as_attachment") String asAttachment)
         throws IOException, ODKTaskLockException;
```

If a query parameter (`?as_attachment=true`) is supplied, then a
`Content-Disposition` header is supplied to trigger a browser to
download the file rather than attempt to display it.

### Upload App-Level File REST API ###

```
  @POST
  @Path("{appId}/files/{odkClientVersion}/{filePath:.*}")
  @Consumes({"*"})
  public Response putFile(byte[] content) 
        throws IOException, ODKTaskLockException;
```

This API is only used for updating the server configuration. During the normal client synchronization workflow, this API is not invoked.

### Delete App-Level File REST API ###

```
  @DELETE
  @Path("{appId}/files/{odkClientVersion}/{filePath:.*}")
  public Response deleteFile() 
        throws IOException, ODKTaskLockException;
```

This API is only used for updating the server configuration. During the normal client synchronization workflow, this API is not invoked.


## Data Grouping #2 REST Synchronization API ##

Synchronizing table-level configuration and data involves:
  1. Getting the list of available tables from the server
  1. Verifying that the table definition on the server and client match
  1. Getting the table-level configuration and files to the client.

The first two steps involve the table API and the table definition API. The data structures used by these APIs will be discussed after the APIs are presented.

## Data Grouping #2 REST Synchronization -- Table API and Table Definition API ##

The table APIs manipulate `TableResource` objects and lists. A `TableResource` identifies the table, information about the earliest and latest update to the data rows in the table, and the `schemaETag` for the table.

The server generates a new, unique, `schemaETag` every time it creates or modifies the table schema. If you create a table, destroy it, then re-create it, the new table will be given a new `schemaETag`.

Creating a table registers a `TableDefinition` for that dataset with the server and creates the necessary database tables for it. Using the `schemaETag`, clients can request the `TableDefinitionResource` for any dataset on the server; that resource consists of the `TableDefinition` and additional information.

Deleting a table on the server involves deleting the specific `TableDefinition` for that tableId's current `schemaETag`.

To prevent data loss, clients that encounter an unexpected `schemaETag` should sync their data as if for the first time.

### List All Table Resources API ###

```
  @GET
  @Path("{appId}/tables")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*TableResourceList*/ getTables(@QueryParam("cursor") String cursor, @QueryParam("fetchLimit") String fetchLimit)
      throws ODKDatastoreException;
```

If the server does not return the entire set of tables, it will provide a `resumeParameter` in the `TableResourceList` that can be passed in as a query parameter for subsequent requests.

### Get Table Resource API ###
```
  @GET
  @Path("{appId}/tables/{tableId}")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*TableResource*/ getTable() 
    throws ODKDatastoreException,
           PermissionDeniedException;
```
### Create Table Resource API ###
```
  @PUT
  @Path("{appId}/tables/{tableId}")
  @Consumes({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*TableResource*/ createTable(TableDefinition definition)
      throws ODKDatastoreException, 
             TableAlreadyExistsException, 
             PermissionDeniedException, 
             ODKTaskLockException;
```
### Get Table Definition API ###
```
  @GET
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}")
  public Response /*TableDefinitionResource*/ getDefinition()
    throws ODKDatastoreException, PermissionDeniedException, ODKTaskLockException, AppNameMismatchException;
```
### Delete Table Definition API ###
```
  @DELETE
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}")
  public Response /*void*/ deleteTable()
    throws ODKDatastoreException,
           ODKTaskLockException, PermissionDeniedException;
```
### `TableResourceList`, `TableResource` and `TableEntry` objects ###
<font color='red'>NOTE: The XML representation for these is in flux. JSON is least likely to change.</font>
```
@JacksonXmlRootElement(localName="tableResourceList")
public class TableResourceList {

  /**
   * pass this in to return this same result set.
   */
  @JsonProperty(required = false)
  private String webSafeRefetchCursor;

  /**
   * Alternatively, the user can obtain the elements preceding 
   * the contents of the result set by constructing a backward
   * query with the same filter criteria but all sort 
   * directions inverted and pass the webSafeBackwardCursor
   * to obtain the preceding elements.
   */
  @JsonProperty(required = false)
  private String webSafeBackwardCursor;

  /**
   * together with the initial query, pass this in to
   * return the next set of results
   */
  @JsonProperty(required = false)
  private String webSafeResumeCursor;

  @JsonProperty(required = false)
  private boolean hasMoreResults;

  @JsonProperty(required = false)
  private boolean hasPriorResults;

  /**
   * The entries in the manifest.
   * This is and ordered list by tableId.
   */
  @JsonProperty(required = false)
  @JacksonXmlElementWrapper(useWrapping=false)
  @JacksonXmlProperty(localName="tableResource")
  private ArrayList<TableResource> tables;

  /**
   * If known, the ETag of the app-level files 
   * manifest is also returned.
   */
  @JsonProperty(required = false)
  private String appLevelManifestETag;
}
```
,
```
@JacksonXmlRootElement(localName="tableResource")
public class TableResource extends TableEntry {

  /**
   * URLs for various other parts of the API
   */

  /**
   * Get this same TableResource.
   */
  private String selfUri;

  /**
   * Get the TableDefinition for this tableId
   */
  private String definitionUri;

  /**
   * Path prefix for data row interactions
   */
  private String dataUri;

  /**
   * Path prefix for data row attachment interactions
   */
  private String instanceFilesUri;

  /**
   * Path prefix for differencing (changes-since) service.
   */
  private String diffUri;

  /**
   * Path prefix for permissions / access-control service.
   */
  private String aclUri;
  
  /**
   * table-level file manifest ETag (optional)
   */
  @JsonProperty(required = false)
  private String tableLevelManifestETag;
}
```
and
```
public class TableEntry implements Comparable<TableEntry> {
  /**
   * The tableId this entry describes.
   */
  private String tableId;

  /**
   * The ETag of the most recently modified data row.
   */
  @JsonProperty(required=false)
  private String dataEtag;

  /**
   * The ETag of the TableDefinition.
   */
  @JsonProperty(required=false)
  private String schemaETag;
}
```
e.g., for JSON:
```
{
  "tables": [
    {
      "tableId": "Milk_bank",
      "dataETag": "uuid:c680b8c5-6862-4440-91a2-b947f7694fe0",
      "schemaETag": "uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0",
      "selfUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Milk_bank",
      "definitionUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Milk_bank\/ref\/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0",
      "dataUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Milk_bank\/ref\/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0\/rows",
      "instanceFilesUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Milk_bank\/ref\/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0\/attachments",
      "diffUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Milk_bank\/ref\/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0\/diff",
      "aclUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Milk_bank\/acl"
    },
    {
      "tableId": "Myna_birds",
      "dataETag": "uuid:9b64bef0-ec0f-4fd1-abfc-db41b57f66e4",
      "schemaETag": "uuid:83e66bff-884a-4ff2-b341-1d382f57da24",
      "selfUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Myna_birds",
      "definitionUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Myna_birds\/ref\/uuid:83e66bff-884a-4ff2-b341-1d382f57da24",
      "dataUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Myna_birds\/ref\/uuid:83e66bff-884a-4ff2-b341-1d382f57da24\/rows",
      "instanceFilesUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Myna_birds\/ref\/uuid:83e66bff-884a-4ff2-b341-1d382f57da24\/attachments",
      "diffUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Myna_birds\/ref\/uuid:83e66bff-884a-4ff2-b341-1d382f57da24\/diff",
      "aclUri": "http:\/\/172.28.7.23:8888\/odktables\/tables\/tables\/Myna_birds\/acl"
    }
  ],
  "resumeParameter": null
}
```
e.g., for XML:
```
<tableResourceList>
    <tableResource>
        <tableId>Milk_bank</tableId>
        <dataETag>uuid:c680b8c5-6862-4440-91a2-b947f7694fe0</dataETag>
        <schemaETag>uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0</schemaETag>
        <selfUri>http://172.28.7.23:8888/odktables/tables/tables/Milk_bank</selfUri>
        <definitionUri>http://172.28.7.23:8888/odktables/tables/tables/Milk_bank/ref/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0</definitionUri>
        <dataUri>http://172.28.7.23:8888/odktables/tables/tables/Milk_bank/ref/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0/rows</dataUri>
        <instanceFilesUri>http://172.28.7.23:8888/odktables/tables/tables/Milk_bank/ref/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0/attachments</instanceFilesUri>
        <diffUri>http://172.28.7.23:8888/odktables/tables/tables/Milk_bank/ref/uuid:38842fd2-14d2-41a1-9e49-fa28fcbacce0/diff</diffUri>
        <aclUri>http://172.28.7.23:8888/odktables/tables/tables/Milk_bank/acl</aclUri>
    </tableResource>
    <tableResource>
        <tableId>Myna_birds</tableId>
        <dataETag>uuid:9b64bef0-ec0f-4fd1-abfc-db41b57f66e4</dataETag>
        <schemaETag>uuid:83e66bff-884a-4ff2-b341-1d382f57da24</schemaETag>
        <selfUri>http://172.28.7.23:8888/odktables/tables/tables/Myna_birds</selfUri>
        <definitionUri>http://172.28.7.23:8888/odktables/tables/tables/Myna_birds/ref/uuid:83e66bff-884a-4ff2-b341-1d382f57da24</definitionUri>
        <dataUri>http://172.28.7.23:8888/odktables/tables/tables/Myna_birds/ref/uuid:83e66bff-884a-4ff2-b341-1d382f57da24/rows</dataUri>
        <instanceFilesUri>http://172.28.7.23:8888/odktables/tables/tables/Myna_birds/ref/uuid:83e66bff-884a-4ff2-b341-1d382f57da24/attachments</instanceFilesUri>
        <diffUri>http://172.28.7.23:8888/odktables/tables/tables/Myna_birds/ref/uuid:83e66bff-884a-4ff2-b341-1d382f57da24/diff</diffUri>
        <aclUri>http://172.28.7.23:8888/odktables/tables/tables/Myna_birds/acl</aclUri>
    </tableResource>
    <resumeParameter/>
</tableResourceList>
```
### `TableDefinition`, `Column` and `TableDefinitionResource` objects ###
<font color='red'>NOTE: The XML representation for these is in flux. JSON is least likely to change.</font>
```
@JacksonXmlRootElement(localName="tableDefinition")
public class TableDefinition {

  /**
   * Schema version ETag for the tableId's database schema.
   */
  @JsonProperty(required=false)
  private String schemaETag;

  /**
   * Unique tableId
   */
  private String tableId;

  /**
   * The columns in the table.
   */
  @JsonProperty(required=false)
  @JacksonXmlElementWrapper(localName="orderedColumns")
  @JacksonXmlProperty(localName="column")
  private ArrayList<Column> orderedColumns;
}
```

```
@JacksonXmlRootElement(localName="tableDefinitionResource")
public class TableDefinitionResource extends TableDefinition {

  /**
   * Get this same TableDefinitionResource.
   */
  private String selfUri;

  /**
   * Get the TableResource for this tableId.
   */
  private String tableUri;
}
```

<font color='red'>NOTE: the 'configpath' and 'rowpath' type handling for <code>elementType</code> is not yet implemented; note that these paths are relative to either the <code>config</code> directory or the the row's data folder, respectively. This will impact all ODK tools.</font>

<font color='red'>NOTE: the '(len)' qualifier for <code>elementType</code> is not yet implemented.</font>

with columns defined by:
```
public class Column {
  /**
   * The fully qualified key for this element. This is the element's database 
   * column name. For composite types whose elements are individually retained
   * (e.g., geopoint), this would be the elementName of the geopoint (e.g., 
   * 'myLocation' concatenated with '_' and this elementName (e.g., 
   * 'myLocation_latitude').
   * 
   * Never longer than 58 characters.
   * Never a SQL or SQLite reserved word
   * Satisfies this regex: '^\\p{L}\\p{M}*(\\p{L}\\p{M}*|\\p{Nd}|_)*$'
   */
  private String elementKey;

  /**
   * The name by which this element is referred. For composite types whose
   * elements are individually retained (e.g., geopoint), this would be simply
   * 'latitude'
   * 
   * Never longer than 58 characters.
   * Never a SQL or SQLite reserved word
   * Satisfies this regex: '^\\p{L}\\p{M}*(\\p{L}\\p{M}*|\\p{Nd}|_)*$'
   */
  @JsonProperty(required=false)
  private String elementName;

  /**
   * This is the ColumnType of the field. It is either:
   *    integer
   *    number
   *    configpath
   *    rowpath
   *    array
   *    array(len)
   *    string
   *    string(len)
   *    typename
   *    typename(len)
   *
   *    Where:
   *
   *    'typename' is any other alpha-numeric name (user-definable data type).
   *
   *    The (len) attribute, if present, identifies the VARCHAR storage
   *    requirements for the field.
   *
   *    The server stores:
   *
   *      integer as a 32-bit integer.
   *
   *      number as a double-precision floating point value.
   *
   *      configpath indicates that it is a relative path to a file under the 'config'
   *             directory in the 'new' directory structure. i.e., the relative path is
   *             rooted from:
   *                 /sdcard/opendatakit/{appId}/config/
   *
   *      rowpath indicates that it is a relative path to a file under the row's attachment
   *             directory in the 'new' directory structure. i.e., the relative path is
   *             rooted from:
   *                 /sdcard/opendatakit/{appId}/data/attachments/{tableId}/{rowId}/
   *
   *      array is a JSON serialization expecting one child element key 
   *            that defines the data type in the array.  Array fields
   *            MUST be units of retention (isUnitOfRetention == true).
   *      
   *      string is a string value
   *      
   *      anything else, if it has no child element key, it is a string
   *            (simple user-defined data type).
   *      
   *      anything else, if it has one or more child element keys, is a 
   *            JSON serialization of an object containing those keys 
   *            (complex user-defined data type).
   *            
   */
  private String elementType;

  /**
   * JSON serialization of an array of strings. Each value in the 
   * array identifies an elementKey of a nested field within this 
   * elementKey. If there are one or more nested fields, then the
   * value stored in this elementKey is a JSON serialization of 
   * either an array or an object. Otherwise, it is either an 
   * integer, number or string field.
   * 
   * If the elementType is 'array', the serialization is an 
   * array and the nested field is retrieved via a subscript. 
   * 
   * Otherwise, the serialization is an object and the nested
   * field is retrieved via the elementName of that field.
   */
  @JsonProperty(required=false)
  private String listChildElementKeys;
}
```
e.g., for JSON
```
{
  "schemaETag": "uuid:92a02929-299a-4e4d-879f-6605656480ed",
  "tableId": "355f8bd0-31c5-11e4-8c21-0800200c9a66",
  "orderedColumns": [
    {
      "elementKey": "device",
      "elementName": "device",
      "elementType": "string",
      "listChildElementKeys": null
    },       
    {
      "elementKey": "timestamp",
      "elementName": "timestamp",
      "elementType": "string",
      "listChildElementKeys": null
    },
    {
      "elementKey": "unit",
      "elementName": "unit",
      "elementType": "string",
      "listChildElementKeys": null
    },  
    {
      "elementKey": "value",
      "elementName": "value",
      "elementType": "string",
      "listChildElementKeys": null
    }
  ],
  "selfUri": "http://146.148.34.74:8080/odktables/odktables/mezuri-10100233/tables/355f8bd0-31c5-11e4-8c21-0800200c9a66/ref/uuid:92a02929-299a-4e4d-879f-6605656480ed",
  "tableUri": "http://146.148.34.74:8080/odktables/odktables/mezuri-10100233/tables/355f8bd0-31c5-11e4-8c21-0800200c9a66"
}
```

## Data Grouping #2 REST Synchronization -- Table-level Files API ##

To support table-specific files, a new manifest API is provided

```
  @GET
  @Path("{appId}/manifest/{odkClientVersion}/{tableId}")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*OdkTablesFileManifest*/ getTableIdFileManifest();
```

The table-level files API is identical to the app-level files API.
It relies upon the file naming convention to distinguish between app-level files and table-level files.

## Data Grouping #3 REST Synchronization - Overview ##

### Attachments: BLOBs and Documents ###
BLOBs, long strings (e.g., MySQL TEXT fields) and arbitrary files can be associated with any data row. These are stored as files and viewed as 'attachments' of the row. If a row has an attachment, the row is expected to have one or more columns in its data table that contain the path to that attachment.

For example, the ODK Tools use a 'mimeUri' data type. e.g.,:
```
{ uriFragment: "tables/tableid/instances/rowId/filename.jpg",
  contentType: "image/jpg" }
```

Once the `rowpath` elementType is available (see the Column object, presented earlier), the uriFragment portion would switch to hide the storage location details, and become:
```
{ uriFragment: "filename.jpg",
  contentType: "image/jpg" }
```

Attachments are immutable. If an attachment is modified, it must be given a new, unique, filepath. The server will not accept revisions to an attachment.

### Revision States ###

It is assumed that the client maintains a set of revision states for an individual row. These states are:
  1. `synced` - no changes to an existing record obtained from the server and all attachment changes have been handled.
  1. `new_row` - a new record on the client.
  1. `changed` - the client modified an existing record obtained from the server.
  1. `deleted` - the client deleted an existing record obtained from the server.
  1. `synced_pending_files` - the client considers the row data to be in the 'rest' state, but the attachments for this row may or may not be up-to-date.
  1. `in_conflict` - the client has determined that there was both a local change to the row and another client has pushed a change to the server, so that the local change cannot be directly submitted to the server, but must instead be resolved with the server's version before being uploaded.

For a given tableId, whenever the `schemaETag` for that tableId has changed, if the client wishes to ensure that its current dataset is preserved, the client should:
  * reset all rows in the `in_conflict` state to their original local change status (i.e., one of `new_row`, `changed` or `deleted`),
  * mark all `synced` and `synced_pending_files` rows as `new_row`.
  * reset the table's last-change-processed value so that the next sync of the table's data will attempt to sync every row in the table.

This may cause all the client's rows to become in conflict with the server; it is unclear what should be the default treatment for this condition.

The server maintains a full history of all changes to a given row. Each row is identified by a `rowId`. Each row revision is identified by its (`rowId`, `rowETag`) tuple.

When a client row is sync'd with the server, the `rowETag` of the prior version of that `rowId` is sent up to the server (sending **null** if this is an `new_row` row) along with all the values in the row.

When a client row is in the `new_row` state, the client may optionally send **null** for the value of the `rowId`, in which case the server will assign an id.

An insert-or-update row request is successful if:
  * the `rowId` does not yet exist, or
  * the `rowETag` matches the value for the most recent revision to `rowId`, or
  * the `rowETag` doesn't match, but the values of the most recent version of the `rowId` on the server exactly match the values sent from the client.

A delete row request is successful if:
  * the `rowId` does not yet exist, or
  * the `rowETag` matches the value for the most recent revision to `rowId`

If successful, any changes are applied on the server, and the client is returned the updated row (and updated `rowETag`). The client should then either delete the local copy if it was in the `deleted` state, or update its corresponding row to `synced_pending_files` if there are rowpath columns in the dataset or `synced` if not, and set `rowETag` to the value returned for `rowETag` in the updated record.

If unsuccessful, an `ETagMismatchException` error is reported back to the client, and the client should mark the row as `in_conflict`.  `in_conflict` rows are not eligible to be sync'd until the client resolves the conflict state, usually through processing convention or user intervention.

If the row is in the `synced_pending_files` state, then the client must determine what actions it needs to perform to bring this row's attachment(s) state into concordance with the server.

Because data records can be sent up to the server before their associated attachments are sent, clients may obtain data records from the server that lack the attachment files that they reference. I.e., `ClientOne` may sync a row with an updated attachment to the server, but fail to send the attachment itself. `ClientTwo` may then sync with the server, obtain the row updates that `ClientOne` just posted, and therefore have a valid, current, row without the attachments that it references.

This is a normal condition and should be anticipated and gracefully handled by the client.

### `synced_pending_files` treatment ###

<font color='red'>The synchronization semantics of attachments is still in flux.</font>

There is a potential for loss of an earlier attachment if the data row is partially sync'd (transitioning into `synced_pending_files`) and the data row is then updated, changing the attachment, before the earlier version of the attachment is saved on the server.

Because the client is strictly forbidden from modifying the contents of the attachment file, we always know if a new attachment is created because the data row will always be modified to update the attachment path.

Similarly, because the `config` directory is static and dictated by the server, any `configpath` field in a data row does not require sync'ing of
that referenced file with the server. It is assumed that the server already
has that file. Only the `rowpath` fields in a data row need to have their attachments sync'd.

The server maintains a manifest of all `rowpath` attachments uploaded for all versions of the row.

The current implementation only considers attachments specified in 'rowpath' elements. If the attachment has not yet been uploaded, a NOT\_FOUND is returned should that attachment be requested.

The sync mechanism first requests all rowpath files, either specifying an ETag if the file exists locally, or omitting it, to pull the file. If a request with an ETag returns NOT\_MODIFIED, then the server has that file. If it returns NOT\_FOUND, then the client should push the file to the server. If it returns the file, then there is an exceptional condition and the client should log an error (but it is fine to download the file -- the server is still the authority for what these files should contain).

## Data Grouping #3 REST Synchronization - Workflow ##

The normal data synchronization workflow is:
  1. Request the `TableResource` for a tableId (using the Table API, defined earlier).
  1. If the `dataETag` in this resource matches the last-change-processed value maintained by the client, then there are no row-value changes. Proceed to upload our changes.
  1. Otherwise, use the `diffUri` to request the list of rows with recent changes.  If you have no last-changed-processed value, use the `dataUri` to request all rows in the table.
  1. Update client state to reflect changes on server.
  1. Update the dataETag of our table to that given in the first result set (RowResourceList) of server rows or changes pulled from the server.
  1. Push `new_row`, `changed` and `deleted` records up to server. Specify the table's dataETag in this request (RowList). If a 409 (CONFLICT) is returned, then go to step (3) above. Otherwise, update our table dataETag with that returned on the RowOutcomeList. Update our local state with the outcomes specified in the RowOutcomeList.
  1. If the above two stages complete without errors, resolve rows in the `synced_pending_files` state by pushing / pulling attachments to / from the server. If successful, transition that row into the `synced` state.
  1. Resolve any `in_conflict` rows (user-directed)

### Get All Data Changes Since... API ###
```
  @GET
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/diff")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*RowResourceList*/ getRowsSince(@QueryParam("data_etag") String dataETag, @QueryParam("cursor") String cursor, @QueryParam("fetchLimit") String fetchLimit)
      throws ODKDatastoreException, 
             PermissionDeniedException, 
             InconsistentStateException, 
             ODKTaskLockException, BadColumnNameException;
```
Unlike the other REST interfaces, this takes a query parameter specifying the `dataETag` from which to report the set of changed rows.

If the server cannot return the entire set of rows, it will provide a `resumeParameter` in the `RowResourceList` that can be passed in as a query parameter to generate the next grouping of rows.
### Get All Data Rows API ###
```
  @GET
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/rows")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*RowResourceList*/ getRows(@QueryParam("cursor") String cursor, @QueryParam("fetchLimit") String fetchLimit) 
          throws ODKDatastoreException, PermissionDeniedException, 
                 InconsistentStateException, ODKTaskLockException, 
                 BadColumnNameException;
```

If the server cannot return the entire set of rows, it will provide a `resumeParameter` in the `RowResourceList` that can be passed in as a query parameter to generate the next grouping of rows.

The `RowResourceList` returned contains the dataETag of the last change processed on the server. Note that later requests with resume cursors may return different values for this dataETag. The value in the first result should be compared with the value returned at the end of the chain of requests. If this value does change, the client should update its table dataETag to the first value and issue a new request using the first dataETag. This will pull the changes that were occurring as the first result set was being pulled and processed by the client. Only once the dataETag does not change can the client be assured that it does not have any partial changeSets.

### Get a Data Row API ###
```
  @GET
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/rows/{rowId}")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*RowResource*/ getRow()
         throws ODKDatastoreException,
                PermissionDeniedException, InconsistentStateException, 
                ODKTaskLockException, BadColumnNameException;
```

Gets the current values for a specific rowId.

### Alter Data Rows (Insert, Update or Delete)API ###
```
  @PUT
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/rows")
  @Consumes({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /*RowOutcomeList*/ alterRows(RowList rows)
         throws ODKTaskLockException, ODKDatastoreException, 
                ETagMismatchException, PermissionDeniedException, 
                BadColumnNameException, InconsistentStateException,
                TableDataETagMismatchException;
```
This REST interface takes a `RowList` that must contain the dataETag of the table that matches the one on the server. If the value does not match, the server returns 409 (CONFLICT) and the client should use the diff API to fetch changes from the server before re-attempting to alter data on the server. If the dataETag does match, a `RowOutcomeList` is returned with the actions taken by the server. Note that some row changes may fail, and some may succeed (e.g., due to permissions violations). The client should process the `RowOutcome` information to update its local database to match that on the server. For bandwidth efficiency, large portions of the `RowOutcome` object will be null upon success.

The `RowOutcomeList` contains the dataETag of the resulting change set on the server. The client should update its table dataETag to match this value.

### `Row` and `RowList`, `RowResource` and `RowResourceList`, `RowOutcome` and `RowOutcomeList` Objects ###
<font color='red'>NOTE: The XML representation for these is in flux. JSON is least likely to change.</font>

`RowList` is a list of rows:
```
@JacksonXmlRootElement(localName="rowList")
public class RowList {

  /**
   * The entries in the manifest.
   */
  @JsonProperty(required = false)
  @JacksonXmlElementWrapper(useWrapping=false)
  @JacksonXmlProperty(localName="row")
  private ArrayList<Row> rows;

  /**
   * The dataETag of the table at the START of this request. 
   */
  @JsonProperty(required = false)
  private String dataETag;
}
```
`RowOutcomeList` is a list of row outcomes:
```
@JacksonXmlRootElement(localName="rowList")
public class RowOutcomeList {

  /**
   * The URL that returns the TableResource for this table.
   */
  @JsonProperty(required = false)
  private String tableUri;

  /**
   * The entries in the manifest.
   */
  @JsonProperty(required = false)
  @JacksonXmlElementWrapper(useWrapping=false)
  @JacksonXmlProperty(localName="row")
  private ArrayList<RowOutcome> rows;

  /**
   * The dataETag for the changes made by this request.
   */
  @JsonProperty(required = false)
  private String dataETag;
}
```
`RowResourceList` is a list of row resources:
```
@JacksonXMLRootElement(localName=“rowResourceList”)
public class RowResourceList {

  /**
   * The entries in the manifest.
   */
  @JsonProperty(required=false)
  @JacksonXmlElementWrapper(useWrapping=false)
  @JacksonXmlProperty(localName=“rowResource”)
  private ArrayList<RowResource> rows;

  /**
   * The dataETag of the table at the START of this request. 
   */
  @JsonProperty(required = false)
  private String dataETag;

  /**
   * The URL that returns the TableResource for this table.
   */
  @JsonProperty(required = false)
  private String tableUri;

  /**
   * Pass this in to return this same result set.
   */
  @JsonProperty(required=false)
  private String webSafeRefetchCursor;

  /**
   * Alternatively, the user can obtain the elements preceding the contents of the 
   * result set by constructing a backward query with the same filter criteria
   * but all sort directions inverted and pass the webSafeBackwardCursor
   * to obtain the preceding elements.
   */
  @JsonProperty(required=false)
  private String webSafeBackwardCursor;

  /**
   * Pass this in to return the next set of results.
   */
  @JsonProperty(required=false)
  private String webSafeResumeCursor;

  /**
   * Indicates if there are more results.
   */
  @JsonProperty(required=false)
  private boolean hasMoreResults;

  /**
   * Indicates if there are prior results.
   */
  @JsonProperty(required=false)
  private boolean hasPriorResults;
}
```
`RowResource` extends a `Row` and supplies a self-reference URL.
```
@JacksonXmlRootElement(localName=“rowResource”)
public class RowResource extends Row {

  /**
   * The URL that returns this RowResource.
   */
  private String selfUri;
}
```
`RowOutcome` also extends `Row` with a self-reference URL and an `OutcomeType`:
```
@JacksonXmlRootElement(localName = "rowResource")
public class RowOutcome extends Row {

  /**
   * Possible values:
   * <ul>
   * <li>UNKNOWN -- initial default value</li>
   * <li>SUCCESS -- rowETag, dataETagAtModification, filterScope updated</li>
   * <li>DENIED -- permission denied -- just the rowId is returned</li>
   * <li>IN_CONFLICT -- server record is returned (in full)</li>
   * <li>FAILED -- anonymous insert conflict (impossible?) or
   *               delete of non-existent row -- just rowId is returned</li>
   * </ul>
   */
  public enum OutcomeType {
    UNKNOWN, SUCCESS, DENIED, IN_CONFLICT, FAILED
  }

  /**
   * The URL that returns this RowResource.
   */
  @JsonProperty(required = false)
  private String selfUri;

  @JsonProperty(required = false)
  private OutcomeType outcome = OutcomeType.UNKNOWN;
}
```
`Row` contains the data for a row.
```
public class Row {

  /**
   * PK identifying this row of data.
   */
  @JacksonXmlProperty(localName=“id”)
  @JsonProperty(value=“id”, required=false)
  private String rowId;

  /**
   * identifies this revision of this row of data.
   * (needed to support updates to data rows)
   * (creation is a revision from 'undefined').
   */
  @JsonProperty(required=false)
  private String rowETag;

  /**
   * identifies the service-level 
   * interaction during which this 
   * revision was made. Useful for 
   * finding coincident changes 
   * and prior/next changes.
   */
  @JsonProperty(required=false)
  private String dataETagAtModification;

  /**
   * deletion is itself a revision.
   */
  @JsonProperty(required=false)
  private boolean deleted;

  /**
   * audit field returned for 
   * archive/recovery tools.
   */
  @JsonProperty(required=false)
  private String createUser;

  /**
   * audit field returned for 
   * archive/recovery tools
   */
  @JsonProperty(required=false)
  private String lastUpdateUser;

  /**
   * OdkTables metadata column.
   *
   * The ODK Survey form that 
   * was used when revising this
   * row.
   *
   * This can be useful for 
   * implementing workflows.
   * I.e., if savepointTyp is
   * COMPLETE with this formId,
   * then enable editing with
   * this other formId.
   */
  @JsonProperty(required=false)
  private String formId;

  /**
   * OdkTables metadata column.
   *
   * The locale of the device 
   * that last revised this row.
   */
  @JsonProperty(required=false)
  private String locale;

  /**
   * OdkTables metadata column.
   *
   * One of either COMPLETE
   * or INCOMPLETE. COMPLETE
   * indicates that the formId
   * used to fill out the row
   * has validated the entered 
   * values.
   */
  @JsonProperty(required=false)
  private String savepointType;

  /**
   * OdkTables metadata column.
   *
   * For Mezuri, the timestamp
   * of this data value.
   *
   * For ODK Survey, the last
   * save time of the survey.
   *
   * For sensor data,
   * the timestamp for the 
   * reading in this row.
   */
  @JsonProperty(required=false)
  private String savepointTimestamp;

  /**
   * OdkTables metadata column.
   *
   * For ODK Survey, the user
   * that filled out the survey.
   *
   * Unclear what this would be 
   * for sensors.
   *
   * For Mezuri, this would be
   * the task execution ID that 
   * created the row.
   */
  @JsonProperty(required=false)
  private String savepointCreator;

  /**
   * FilterScope is passed down to device.
   *
   * This is a placeholder for 
   * security filtering.
   * 
   * It is passed down to the 
   * device so that the 
   * device could do best-effort
   * enforcement of access control
   * (trusted executor)
   */
  @JsonProperty(required=false)
  private Scope filterScope;

  /**
   * Array of user-defined column name to 
   * the string representation of its value.
   * Sorted by ascending column name.
   */
  @JsonProperty(required=false)
  @JacksonXmlElementWrapper(localName=“orderedColumns”)
  private ArrayList<DataKeyValue> orderedColumns;
}
```
where `Scope` is:
```
public class Scope {
  /**
   * Type of Scope.
   *
   * Limited to 10 characters
   */
  public enum Type {
    DEFAULT, USER, GROUP,
  }

  @JsonProperty(required=false)
  private Type type;

  @JsonProperty(required=false)
  private String value;
}
```
and `DataKeyValue` is:
```
public class DataKeyValue {
  @JacksonXmlProperty(isAttribute=true)
  public String column;
  
  @JacksonXmlText
  public String value;
}
```
e.g., for JSON
```
{
  "rows": [
    {
      "rowETag": "uuid:1bd9e6c8-3a83-47b6-8dd9-b8f074f0981b",
      "dataETagAtModification": "uuid:945458dd-c595-46bc-9bb7-bc3d08fd3a5e",
      "deleted": false,
      "createUser": null,
      "lastUpdateUser": null,
      "formId": null,
      "locale": null,
      "savepointType": null,
      "savepointTimestamp": null,
      "savepointCreator": null,
      "filterScope": {
        "type": "DEFAULT",
        "value": null
      },
      "orderedColumns": [
        {
          "column": "device",
          "value": "A564839E01"
        },
        { 
          "column": "timestamp",
          "value": "11:06:23 09/01/2014"
        },
        {
          "column": "unit",
          "value": "C"
        },         
        {
          "column": "value",
          "value": "23.50"
        }
      ],
      "selfUri": "http://146.148.34.74:8080/odktables/odktables/mezuri-10100233/tables/355f8bd0-31c5-11e4-8c21-0800200c9a66/ref/uuid:92a02929-299a-4e4d-879f-6605656480ed/rows/bc720740-31c7-11e4-8c21-0800200c9a66"
     },
    {
      "rowETag": "uuid:bbea085a-4115-4ab7-87cd-a83ceab75eec",
      "dataETagAtModification": "uuid:22cdabd4-1dc5-4119-b667-b8454c883fbd",
      "deleted": false,
      "createUser": null,
      "lastUpdateUser": null,
      "formId": null,
      "locale": null,
      "savepointType": null,
      "savepointTimestamp": null,
      "savepointCreator": null,
      "filterScope": {
        "type": "DEFAULT",
        "value": null
      },
      "orderedColumns": [
        {
          "column": "device",
          "value": "A56483AE01"
        },
        {
          "column": "timestamp",
          "value": "11:08:29 09/01/2014"
        },
        {
          "column": "unit",
          "value": "C"
        },     
        {
          "column": "value",
          "value": "25.50"
        }
      ],
      "selfUri": "http://146.148.34.74:8080/odktables/odktables/mezuri-10100233/tables/355f8bd0-31c5-11e4-8c21-0800200c9a66/ref/uuid:92a02929-299a-4e4d-879f-6605656480ed/rows/5e9857e0-31c8-11e4-8c21-0800200c9a66"
    }
  ],
  "dataETag": "uuid:22cdabd4-1dc5-4119-b667-b8454c883fbd",
  "tableUri": "http://146.148.34.74:8080/odktables/odktables/mezuri-10100233/tables/355f8bd0-31c5-11e4-8c21-0800200c9a66","id":"bc720740-31c7-11e4-8c21-0800200c9a66",
  "webSafeRefetchCursor": null,
  "webSafeBackwardCursor": "H4sIAAAAAAAAAG2Py2rDMBREfyV4G2RdqSaOjSIITgKFkIIx3YYbWUlM_UK6wv38lLrQB13M5syZxSgTnB_c4r1re7-J7kRjzvk0TfEw2r5GwreG4sHd-CxGWiGRay6B7Ak7q89Fud9Wzy-n825b7RX_3X7Lr9gGqyWIhEHGQFRC5JDmiYwBYPkR-LGdZRVcc0RPpaXgelvP9GJSCWkC7EmYlAlhE7Y2UjBYA0gAk-Fqpfi_U9X4w-AmdHXxeUZfsfVW8b9YfZ3VDwCur8keAQAA",
  "webSafeResumeCursor": "H4sIAAAAAAAAAG2PTYvCQBBE_4p4lcl0Z6MmYRwQ1wVBFCR4DbNJs4bVRDo9xJ-_ixH8wENdXr06lCk8tw0PLqdj3c6GB5FzqnXXdUFzprp04n4rCRr-0b04tMaJcPXthTbuRDZf7JbzbLXd5J_zbGn0c3uX9-7oyYaAkYJEAWaIKSRphAEAjP4DD9teNp6rtWtlR-K5prKnY0ri8ZRAfWARK0SKVFyEqCAGCAGKxE0mRr-dmqr9arhzXC6uZ6ywJ6Nfqbl9tX-hHzrTHQEAAA",
  "hasMoreResults": false,
  "hasPriorResults": false
}
```
The `dataETagAtModification` field tracks the change entry that can be used with the **Get All Data Changes Since... API** to return the changes in the data table from this row's last data change (as indicated by the `rowETag`).

The `createUser` and `lastUpdateUser` fields may be set and returned by the server.  These are intended for data-dump and data-restore functionality and are not normally provided by a client.

The `formId` field identifies the ODK Survey form that last modified this record.  This is useful for implementing multi-stage client workflows.

The `locale` field tracks the last ODK Survey locale in which the form was opened and perhaps modified.

The `savepointType` is one of `INCOMPLETE` or `COMPLETE`; it indicates whether the data is considered to be in a possibly-incomplete state or if it is complete (i.e., in ODK Survey, if it has been validated and marked as finalized). Together with the `formId`, this can indicate whether the client processing can advance from one workflow stage (`formId`) to another (i.e., when the record is 'COMPLETE' in the current stage) or whether to stall within the current workflow stage (`formId`). For autonomous data publishing (e.g., ODK Sensors Framework), this should be set to `COMPLETE`.

The `savepointTimestamp` is the timestamp of the last save of this data record, as reported on the client (whose time clock may be inaccurate).

The `savepointCreator` is the entity modifying/writing this data row. For ODK Survey, this is the user as identified by the Android device.

The `filterScope` should default to `{type: 'Default', value: null}`. It is used to control access to the data record. Future updates to this protocol will likely make this unmodifiable on the server unless the requesting user has appropriate permissions. The contents, interpretation and use of this field is evolving at this time.

The `values` map holds the data values that the user has defined.

### Get Manifest of Attachments API ###
```
  @GET
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/attachments/{rowId}/manifest")
  @Produces({"application/json", 
             "text/xml;charset=UTF-8", 
             "application/xml;charset=UTF-8"})
  public Response /* OdkTablesFileManifest */ getManifest(@QueryParam("as_attachment") String asAttachment)
     throws IOException;
```
This returns all attachments (both current and historical) for the given `rowId` on the server.

This uses the same return structure as the Table-level and App-level manifest, but the path is relative to the directory in which the `rowId` attachments are stored on the client.

### Get Attachment API ###
```
  @GET
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/attachments/{rowId}/file/{filePath:.*}")
  @Produces({"*"})
  public Response getFile(@QueryParam("as_attachment") String asAttachment)
        throws IOException;
```
The `filePath` is relative to the folder holding attachments for the `rowId`.

### Put Attachment API ###
```
  @POST
  @Path("{appId}/tables/{tableId}/ref/{schemaETag}/attachments/{rowId}/file/{filePath:.*}")
  @Consumes({"*"})
  public Response putFile(byte[] content)
        throws IOException, ODKTaskLockException;
```