# Introduction #

The ODK 2.0 tools share common libraries. This attempts to describe all the changes, by revision, across the various tools.

# Change Log #

## rev 128 (May 2015) ##
Sync Alpha-2 ONLY
  * fix for incorrectly handling gzip data

## rev 127 (April 2015) ##
Sync Alpha-2 ONLY
  * corrections to conflict resolution activity "take server changes"

## rev 126 (February 2015) ##
Survey Beta-4

Tables Alpha-4

Sync Alpha-2

ApplicationDesigner Alpha-3

(AndroidCommon)
  * change to new sync protocol using hacked-down wink client
  * move ElementType and ElementDataType into REST API jar
  * add functionality to assist in mock unit testing (StaticStateManipulator)

(Survey)
  * misc. bug fixes.
  * updated javascript libraries
  * updates for rowpath

(Tables)
  * updated javascript libraries
  * use WebLogger instead of standard logger
  * updates for rowpath

(Sync)
  * requires ODK Aggregate 1.4.5
  * use wink client libraries
  * use rowpath for file attachments and optimize sync protocol.
  * optimize for efficient compressed protocol with NOT\_MODIFIED returns

(ApplicationDesigner / SurveyJS )
  * changes to support rowpath
  * writes definition.csv and properties.csv when formId equals tableId
  * eliminate need for Mac JS execution environment (use node instead)
  * updates to numerous demos
  * prototype SMS integration (needs APKs that are not yet public)

## rev 124 (October post release) ##

(AndroidCommon)
  * unified database layer

(Survey)
  * updated javascript libraries

(Tables)
  * unified database layer
  * remove many files (moved into androidCommon)

(Sync)
  * requires ODK Aggregate 1.4.4
  * only works when syncing to AppEngine (not local Tomcat server)
  * update sync protocol
  * move some database functionality into common database layer.

(ApplicationDesigner / SurveyJS )
  * fixes for incorrect SQL filter conditions
  * updates to agriculture demo form
  * misc. display bug fixes

## rev 122 (August 2014) ##
Survey Beta-3

Tables Alpha-3

Sync Alpha-1

ApplicationDesigner Alpha-2

(AndroidCommon)
  * add androidcommontest project (push directories down)
  * add sync AIDL (and wire it up)
  * support for 4.4.2 media provider changes
  * remove directory change watchers
  * remove Sherlock dependency
  * add About... menu (and licensing info display)

(Survey)
  * extract framework and assets files from embedded zip
  * use dbShim for all database interactions
  * legacy "form download" operates at the tableId level, not formId
  * change to publish to a tableId
  * change to support multiple revisions to the same instanceId when publishing to legacy pathway

(Tables)
  * add tablestest project (push directories down)
  * update 3rd party jar versions
  * restructure to isolate sync and database classes into their own package trees
  * remove SMS and Submit functionality
  * remove Java-layer graphing and calendering activites
  * revise CSV import to use standard one from ODK Aggregate REST api
  * extract framework and assets from embedded zip
  * numerous UI display changes
  * sync is now done by Sync APK

(Sync)
  * first release
  * requires ODK Aggregate 1.4.4

(ApplicationDesigner / SurveyJS )
  * add "Save to FileSystem" button in XLSXConverter
  * restore graph javascript (Tables)
  * fix various demo files
  * add color treatments to prompt types
  * add Ajax data queries
  * change to use database shim (dbShim) and eliminate W3Sql use in WebKit
  * zero-length strings are treated as nulls
  * adding ODK Scan demo files
  * adding Jane Goodall Institute Chimp-tracking "user-as-sensor" Tables app.

## rev 120 (March 2014) ##
Survey Beta-2

Tables Alpha-2

ApplicationDesigner Alpha-1

(AndroidCommon)
  * switch to use /sdcard/opendatakit
  * add local webserver (moving away from using a file content provider)
  * update sync protocol.

(Survey)
  * change to use webserver
  * change to use survey.properties to store General and Admin settings

(Tables)
  * split conflict resolution activity into list- and row- levels
  * add edit/add via Collect/Survey to Control (from Tables) Javascript API
  * significant restructuring of data-model-related classes.

(ApplicationDesigner / SurveyJS )
  * rework to use grunt and node for ApplicationDesigner environment

## rev 116 (December 2013) ##
Survey Beta-1

  * initial release

## rev 006 (December 2012) ##
Tables Alpha-1

  * initial release