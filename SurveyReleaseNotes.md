# ODK Survey 2.0 BETA #

<font color='red'>Beta releases should never be used for production deployments.</font>  Beta releases are provided to gather bug reports (to make the application more robust) and for user feedback on the capabilities of the application. <font color='red'><b>Updates may result in loss of data or incompatible changes in form designs.</b></font>

We expect to release another Beta with:
  * directory changes (splitting everything into system, config and data folders)
  * Javascript framework changes to use Bootstrap instead of JQueryMobile (much more responsive and WYSIWYG).  This changes custom templates but the XLSX format does not change.
  * there may be changes to how the back button works (both the device back button and the back button within the app).
  * there will also be changes to the CSS styling of the widgets to create a flatter, more modern, look-and-feel.

## rev 126 Beta-4 ##
  * Sync changes to new efficient protocol using hacked-down wink client
  * add functionality to assist in mocked unit testing
  * misc. bug fixes.
  * updated javascript libraries

## rev 124 (not released) ##

  * unified database layer
  * updated Javascript libraries

## rev 122 Beta-3 ##

  * support for 4.4.2 media provider changes
  * remove directory change watchers
  * remove Sherlock dependency (Android 4.2 and higher required)
  * add About... menu (and licensing info display)
  * extract framework and assets files from embedded zip
  * legacy "form download" operates at the tableId level, not formId
  * change to publish to a tableId
  * change to support multiple revisions to the same instanceId when publishing to legacy pathway

## rev 120 Beta-2 ##

  * switch to use /sdcard/opendatakit
  * change to use local webserver (moving away from using a file content provider to vend WebKit content)
  * change to use survey.properties to store General and Admin settings

## rev 116 Beta-1 ##
  * new XLSX syntax
  * new device directory structure ( now under .../opendatakit/survey )
  * linked tables (subforms)
  * selects from linked tables, external csv files and web queries

### Usage ###

After downloading and installing, choose the 'down arrow' icon to access the Form Download screen. On that screen, select the following forms to download:
  * `Common Javascript Framework`
  * whatever other forms you want to try out

After downloading the forms, return to the form chooser screen by choosing the 'open folder' icon. On that screen, select the form to open.

The `Common Javascript Framework` form does not appear on this list because it is the underlying set of HTML, CSS and javascript libraries that process and render the form definitions. It appears as a form for downloading because it is separately upgradable from the Java code and because of the way the ODK Aggregate legacy integration works.

The application has been tested on Android 4.1 and higher devices. While it does work on 4.0 and earlier devices, not all features may function on those devices.  **_On the original 2.2.3 Droid, the first load of the form will leave you with a white screen_** while the system reads and interprets the javascript framework files. <font color='blue'><b>This can take minutes.</b></font>

# ODK Survey 2.0 ALPHA #

<font color='red'>Alpha releases should never be used for production deployments.</font>  Alpha releases are provided to gather bug reports (to make the application more robust) and for user feedback on the capabilities of the application. <font color='red'><b>Updates may result in loss of data or incompatible changes in form designs.</b></font>

ODK Survey 2.0 is a re-envisioning of the popular ODK Collect data collection application using the following design principles:

  1. when possible, UI elements should be designed using a more widely understood runtime language (Javascript) instead of a compile time language, thereby making it easier for individuals with limited developer skills to make customizations;
  1. the basic data structures should be easily expressible in a single row, and nested structures should be avoided when data is in display, transmission, or storage states;
  1. data should be stored in a database that can be shared across devices and can be easily extractable to a variety of common data formats; and
  1. new prompt types, sensors, data input methods and a diversity of input data types should be easy to incorporate into the data collection pipeline by individuals with limited technical skill.

ODK Survey 2.0 is the form-based data entry tool of the new ODK 2.0 tool suite.

The new ODK XLSConverter 2.0 application takes a survey description specified in an Excel spreadsheet and emits a JSON form definition that is then parsed and rendered by the ODK Survey 2.0 tool. The Excel form description now supports native Javascript code for predicates and adds labels and goto branching functionality.  Documentation and links to this app ccan be found here [XLSConverter 2.0 documentation](XLSForm2Docs.md)

A conversion script takes the JSON formDef and emit an XForms form definition that can be loaded to ODK Aggregate 1.x for a backward-compatible ODK 1.0 submission pipeline.

## rev 105 ##
  * fix bugs causing downloads of forms to fail.

## rev 103 ##

  * you will need to delete the /odk/js directory and all forms.
  * this monitors and scans all folders under /odk for changes to the  /odk/xxx/forms and /odk/xxx/framework directories.  These are considered to be potential locations for ODK Survey apps.
  * ODK Survey can launch with different app names. These are directories under /odk/. Once named, and app cannot be renamed to a different app (data collection breaks).
  * database structures have changed.
  * various bug fixes, including back button retention on orientation changes.
  * forms on opendatakit-2 must be re-pulled (they have changed).

## rev 100 ##

  * initial release.

## ODK Survey Basics ##

After installing the ODK Survey 2.0 APK, you **must** download forms from the ODK Aggregate server ( https://opendatakit-2.appspot.com ).

The `Default form` **_must always be present_** on the phone. If you delete it, no forms will render. The `Default form` contains the extensive set of Javascript for rendering the form screens.  This Javascript is based upon jQuery, jQueryMobile, Handlebars templates and Backbone.

The other downloaded forms include:
  * `Example Form` -- a form with many examples of data entry widgets
  * `Custom Appearance Form` -- a form demonstrating how the look and feel of the questionnaire can change by using custom CSS styles.
  * `Initial refrigerator information` -- together with `Refrigerator information update form`, this form demonstrates that ODK Survey can have two different form definitions that edit the same data values. This functionality is incompatible with the 1.x data pipeline, but these two forms show what will be possible with ODK Survey and the 2.0 data pipeline.
  * `Refrigerator information update form` -- see description for `Initial refrigerator information` above.

See [XLSConverter 2.0 documentation](XLSForm2Docs.md) for how to develop your own form definitions.

## Backward-compatible operations with ODK Aggregate ##

Once you have produced a form definition file (formDef.json) from the new XLSConverter 2.0 form definition, you can use the backward-compatibility function of ODK Survey to publish submissions into an existing ODK Aggregate 1.x server.

To do this,
  1. from a Windows PC...
  1. create a `forms` directory on your system and
  1. within this directory, create a sub-directory with a name like `example-media` where `example` would be a brief identifier for your form name. `example` should not contain any dashes (`-`). **The name of this sub-directory must end with `-media`**.
  1. within that sub-directory, place the `formDef.json` file produced by the XLSConverter 2.0 process.
  1. if you have any additional files, such as custom css files or images, etc., copy them into this directory.
  1. if you have any nested directories of images or css files, create a zip of each such directory, leave that in this sub-directory, and remove the nested directories.  For example, the `Default form` on the https://opendatakit-2.appspot.com site has many zip files holding the various Javascript libraries and resources it uses.  ODK Survey will in-place-unzip any `.zip` files after downloading them.
  1. Download (and save to a local file) http://collect2.opendatakit.googlecode.com/hg/form-files/convertToLegacy.hta This is a scripting app that will only run on Windows systems.
  1. Double-click the convertToLegacy.hta app to launch it.
  1. Paste the full directory path to the `forms` directory into the _Forms Directory:_ field and click _Generate_.
  1. The script will scan for sub-directories ending in `-media` and parse any `formDef.json` files contained within them, producing an ODK Aggregate 1.x-compatible XForms form definition file (.xml). These will be placed in the forms directory. So if you had `example-media`, an `example.xml` file would be generated in the forms directory.
  1. Now, run the ODK FormUploader 1.x tool [available here](http://code.google.com/p/opendatakit/downloads/list) to upload the form definition files (and their `-media` folders) to your ODK Aggregate 1.x instance.
  1. You can now run ODK Survey, download these forms from ODK Aggregate 1.x and submit data into ODK Aggregate 1.x from ODK Survey.

<font color='blue'>NOTE: The converter script produces an XForms definition file (.xml) that is unusable by ODK Collect 1.x. It only contains enough information to support publishing of data into ODK Aggregate</font>