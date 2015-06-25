# Introduction #

This page explains how to download and install the latest version of Tables Alpha.

# Details #

**Clone the proper repositories:**

From the ODK website (code.google.com/p/opendatakit), go to the source tab. Scroll down to the Unreleased projects. From the Unreleased Projects, clone the following repositories:
  * ActionBarSherlock
  * PlayLicensing
  * PlayServices
  * AndroidCommon
  * Tables (Java)

**Set up tables in eclipse:**

From the Android Development Tools (ADT) open up eclipse (if you haven’t already, download ADT). In ADT, go to ADT preferences. Under Android Preferences make sure to select a Google API to be using and hit apply (API level 16+ will work). From this point, proceed to import the repositories downloaded earlier into your eclipse workspace. If at any point along the way eclipse says it cannot resolve certain packages, open the SDK manager and install the missing packages. After installing and importing everything, clean all the projects. If there still are errors, go to ADT preferences and reselect the Google API to use.

**Install tables on an Android Device:**

Plug in your android device and select ODK Tables in the Package Explorer. Then go to Run > Run As > Android Application. In the following screen, select your android device and click OK. This will install tables on your android device.


**Working with example files:**

From the ODK website clone the repository
> _https://code.google.com/p/opendatakit.tables-js_
To get the geotagger example working a couple things have to first be done to the directory structure and files stored on the android device under sdcard/odk/tables.
In this directory, make a new directory called framework. Then adb push the contents of the framework directory from the tables-js repository into the framework directory on the android device.  Next, create a directory under sdcard/odk/tables called tables. In the tables-js repository, find geoBaseNewDirectory/geobase/tables. Adb push the contents of this directory into sdcard/odk/tables/tables. This should put a folder called geotagger\_v3 into the sdcard/odk/tables/tables directory. You should now be able to run tables and import the geotagger\_v3.csv file to be able to use the geotagger example.