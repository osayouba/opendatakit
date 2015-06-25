# Collect 1.4.5 #

Production Release

## rev 1048 ##
  * numerous bug fixes and extreme performance improvements to the form evaluation logic resulting from a close collaboration between the ODK core team and SurveyCTO (javarosa-2015-01-10 jar). Among the changes to javarosa:
    * code rewritten to eliminate use of Vector and other synchronized data structures that were required for J2ME operations.
    * minimize space usage when allocating data structure.
    * dramatically rewrite evaluation logic; many fixes for the handling of repeat groups (see below).
  * Added Admin Setting for "Form Processing Logic" to select among different form evaluation logic implementations:
    * Recommended form evaluation logic (default - whatever logic is the current best going forward)
    * January 2015 (fastest) form evaluation logic
    * January 2015 (safest) form evaluation logic
    * Mid 2014 form evaluation logic (ODK Collect 1.4.4 and 1.4.3)
    * Early 2014 form evaluation logic (ODK Collect 1.4.2 and earlier)
  * update to newest GME API (October 2014)
  * new function: enclosed-area() (or area()) contributed by SurveyCTO
  * display friendlier error messages when evaluations fail (contributed by SurveyCTO)
  * fix: form crashes when deleting a repeat group.
  * fix: [issue 1057](https://code.google.com/p/opendatakit/issues/detail?id=1057) - exception when server returns 401
  * add Japanese translation
  * update translations for ca, cs, es, fr, id, it, ja, lo, ne, pl, pt, ro
  * fix: various crashes reported thru Google Play
  * fix: custom splash screen picker not working in Android 4.4.2 and higher
  * allow user to move the installation (APK file) to the "external storage" partition (data location is unchanged and is hardcoded as /sdcard/odk).

# Collect 1.4.4 #

Production Release

## rev 1046 ##
  * updated translations for ca, es, it, lo, ne, nl, pl, ro, ru, th
  * fixing form delete so that it deletes the proper tables and data associated with external itemsets for the selected form.

## rev 1045 ##
  * fix [issue 959](https://code.google.com/p/opendatakit/issues/detail?id=959) - crash if two forms reference the same external itemset.
  * fix - internationalized display names in external itemsets.
  * minor updates to translations

## rev 1044 ##
  * new: saveIncomplete() functionality. Contributed by Nafundi
  * new: delete-submission-after-upload functionality. Contributed by Nafundi
  * new: submit data to Google Maps Engine, pictures to Picasa, and host form definitions on Google Drive (no need for ODK Aggregate). Contributed by Nafundi
  * fix - when changing credentials, clear the cookie cache (prevents new user from operating with the credentials of the previous user). Contributed by SurveyCTO
  * fix [issue 1037](https://code.google.com/p/opendatakit/issues/detail?id=1037) - changing settings does not change property values in form.
  * add czech translation
  * add polish translation
  * fix french translation (had caused Forced Close)
  * minor updates to italian, spanish.
  * consolidate JavaRosa initialization to a method in FormController.

# Collect 1.4.3 #

Production Release

## rev 1042 ##
  * restore efficiency of form downloads to not always pull unchanged files.
  * more crash protection for when Android 2.x devices have their SDCard mounted (and therefore ODK Collect cannot properly start up).
  * fix for Force Close during a Mark-as-Finalized action.
  * fix for Nexus 7 tablet video capture not working.
  * updated translations: es, it, ro, ru, th, tr

## rev 1041 ##
  * [issue 1003](https://code.google.com/p/opendatakit/issues/detail?id=1003) - media capture broken on 4.1 and earlier devices.
  * fix for edit-saved-instances count not being properly updated when instances added by external means.

## rev 1040 ##
  * dynamic search-and-select feature - contributed by SurveyCTO.
  * pulldata() feature for pre-loading survey data - contributed by SurveyCTO.
  * [issue 631](https://code.google.com/p/opendatakit/issues/detail?id=631) - external app usage extension for getting and setting sets of values - contributed by SurveyCTO.
  * upon form download, do not 'install' the form until all supporting media files have also been downloaded -- contributed by SurveyCTO
  * improved performance via background save of form (rather than in foreground) - contributed by SurveyCTO
  * force landscape mode in 'annotation' widget
  * [issue 953](https://code.google.com/p/opendatakit/issues/detail?id=953) - enable hi-res video capture (and setting to choose low-res)
  * update javarosa jar
    * new 'current()' qualifier in XPath expressions that acts like the 'instance('name')' qualifier, but refers to the current survey (and defaults to referencing the current node). Useful for the constraints within cascading selects within repeat groups.
    * new 'once(expr)' function that only saves the given expression in this field if the field does not yet have a value - contributed by SurveyCTO
    * new 'version()' function to access form version ([issue 949](https://code.google.com/p/opendatakit/issues/detail?id=949))
    * new 'geotrace' and 'geoshape' data types (not plumbed through to ODK Collect or ODK Aggregate)
    * fix [issue 563](https://code.google.com/p/opendatakit/issues/detail?id=563), [issue 435](https://code.google.com/p/opendatakit/issues/detail?id=435) - cannot use geopoint in a calculate expression. Now: boolean true is that there is a collected data point, number is the accuracy, and string is the full string representation of the geopoint.
    * fix [issue 894](https://code.google.com/p/opendatakit/issues/detail?id=894) - resolve substitution strings when we do jr:choice-name() look-ups.
    * fix [issue 888](https://code.google.com/p/opendatakit/issues/detail?id=888) - calculates and relevant conditions do not evaluate correctly when a group first becomes relevant. The outer group conditions now properly trigger re-evaluations of the nested fields.
    * remove J2ME support
    * merge changes from Commcare
  * fix issues with KitKat (Android 4.4) media chooser / capture
  * [issue 827](https://code.google.com/p/opendatakit/issues/detail?id=827) - fix display of values in Hierarchy view (contents view) when using repeat groups.
  * fix crashes during background sync due to network outages - contributed by Survey CTO
  * fix french translation which was causing crashes on the 'Send Finalized Form' screen.
  * [issue 183](https://code.google.com/p/opendatakit/issues/detail?id=183) - catch more sdcard availability failures
  * numerous crash fixes -- contributed by SurveyCTO
  * updated translations.
  * [issue 42](https://code.google.com/p/opendatakit/issues/detail?id=42) - (was solved several revisions ago) support translations of constraint and relevant messages
  * [issue 78](https://code.google.com/p/opendatakit/issues/detail?id=78) - (was solved several revisions ago) you can specify an accuracy constraint on a geopoint via the accuracyThreshold="" attribute.

# Collect 1.4 #

Production Release

## rev 1038 ##
  * fix french translation which was causing crashes on the 'Send Finalized Form' screen.

## rev 1037 ##
  * fix manifest and opencsv.jar so that Google Play can distribute app to devices.

Release Candidates

## rev 1036 ##
  * <font color='red'>partial fix <a href='https://code.google.com/p/opendatakit/issues/detail?id=918'>issue 918</a></font> - If Android 4.3 and higher devices include the Bouncy Castle encryption provider, then encrypted forms will be saved and encrypted. **If the Bouncy Castle provider is not available, then the forms will be saved without encryption.** This addresses encryption failures on Android 4.3. It is unclear if Bouncy Castle will always be present on Android 4.3 systems (on the tested systems, it is present) or if it will ever become unavailable in the future. **Without this fix, data corruption will occur on Android 4.3 and higher systems.** Fix accomplished with significant assistance by CTOSurvey.
  * fix silent failures when encrypted forms do not specify a server URL.
  * add a pow(a,b) function to raise 'a' to the power 'b'  contributed by Nafundi.

## rev 1035 ##
  * fix for dynamic itemset interaction with jr:choice-name (where select choices are stored in separate named instances within the XML).
  * add JR function:  property(name) to retrieve the property from the property manager. Eliminates the need to first store the property in a field -- they can now be used directly within a formula. NOTE: only works with immutable values -- i.e., does not work with start or end time.

## rev 1034 ##
  * fix date widget appearance for month-year and year-only on newer 4.x devices.
  * add 'no-calendar' appearance to force older spinner-only display on newer 4.x devices.
  * eliminate extra blank line that was displayed if a question did not have a text label.

## rev 1033 ##
  * update to newer 3.x and 4.x user interface standards.  Legacy 2.x devices should continue to work as before.  Key changes are to the menu (the icons are no longer shown) and while filling out a form, the save and hierarchy menu items are reachable via the icons on the 'action bar' at the top of the app.
  * update icons to conform to pixel dimensions of their respective dpi classes. This shrinks some icons for some displays, and enlarges others for other displays. Add xhdpi icons.
  * supply 'largeHeap' attribute to enable ODK Collect to run with a larger heap (including stack) on some devices (dependent upon hardware manufacturer). Can enable more complex regex() functions.
  * expose the InstanceUploaderList intent for direct access to the 'Send Finalized Form' screen by other apps.
  * new 'placement-map' appearance for geopoint widget. This presents a map display where you can drag the geolocation marker on the map, and save the new hand-placed geolocation back into the form (contributed by Guillaume Salmon).
  * improved gzip'd download of files from server (contributed by Sassafras Tech Collective)
  * new General Settings option to defer all validation checks until the end of a form; ability to hide this option using the Admin Settings (contributed by Sassafras Tech Collective).
  * the External String, Integer, Decimal widgets now pass the current data value into the external intent via the "value" field.
  * new "align:outerRectWidth outerRectHeight innerRectWidth innerRectHeight innerRectXOffset innerRectYOffset" appearance for image widget. Displays a red registration box overlay on the camera image for aiding image capture. Uses the http://code.google.com/p/opendatakit/source/browse?repo=alignedcamera app.
  * new "query...." appearance for string widget. Will provide select choices from csv data (contributed by Nafundi) Changes to XLSForm and documentation of this feature are in progress - no firm ETA.
  * update to javarosa library.
    * improved reporting of incorrect number of function arguments.
    * [issue 852](https://code.google.com/p/opendatakit/issues/detail?id=852) - improved traceback of validation errors to line in xml file. (contributed by SurveyCTO)
    * [issue 846](https://code.google.com/p/opendatakit/issues/detail?id=846), [issue 832](https://code.google.com/p/opendatakit/issues/detail?id=832), [issue 822](https://code.google.com/p/opendatakit/issues/detail?id=822) - suppress spurious validation failures (contributed by SurveyCTO)
    * ensure that uuid(), now(), today() and random() compute new values at each use within a form (previously, if you had two calculate expressions of 'uuid()', they would both return the same UUID (or instant of time, or random number).
    * [issue 630](https://code.google.com/p/opendatakit/issues/detail?id=630), [issue 808](https://code.google.com/p/opendatakit/issues/detail?id=808), [issue 855](https://code.google.com/p/opendatakit/issues/detail?id=855) - datetime values truncated to date in computations. Fixed by adding new functions:
      * date-time(x) -- convert a decimal time or string to a dateTime value without setting the time-of-day to 00:00:00.000 local time (i.e., preserve the incoming fractional day / time-of-day in the resulting dateTime) value.
      * format-date-time(df, fmt) -- format a dateTime field (df) according to the fmt string without first truncating the dateTime field value to a date-only (00:00:00.000 time) value.
      * decimal-date-time(x) -- convert a dateTime, date or string value to a decimal time value (integer days since 1970-01-01 plus fractional time of day).
      * decimal-time(x) -- convert a time value to a decimal time value (0.0 + fractional (local) time of day).
    * indexed-repeat(repeatedfield, repeatgroup, index): References a field or group that is inside a prior repeat group. The first parameter specifies the prior field or group in which you are interested; the second specifies the prior repeat group within which thefield or group of interest is located; and the third specifies the instance number, within the prior repeat group, to use. For example, the calculate expression "indexed-repeat(${name}, ${names}, 1)" will return the first name available when the "name" field is inside a prior repeat group named "names". From inside a later repeat group, the calculate expression "indexed-repeat(${name}, ${names}, position(..))" will pull the xth name from the prior repeat group, where x is the instance number of the current repeat group (e.g., if currently in the fourth instance of a repeat group, it will return the fourth name from the earlier repeat group). If you need to reference a field or group within multiple nested repeat groups, you can supply additional parameters to indicate the instance numbers to use for each level of nesting. For example, the calculate expression "indexed-repeat(${name}, ${families}, ${familynumber}, ${names}, ${membernumber})" will pull a particular family member's name when family member names are inside a repeat group that is itself inside a repeat group of families. (contributed by SurveyCTO)
  * fix: Draw, Annotate widget: the set-color action was not functional on ldpi screens (pop-up is too big for screen).
  * fix: potentially unsafe re-initialization of JavaRosa libraries
  * behavior change: csv files, when downloaded as part of the form definition's set of media files, are parsed and inserted into SQLite database for use in 'query...' appearance string widget.
  * fix: add guards to prevent crashes due to SDCard not being available.
  * behavior change: if the instance directory might be located under an ODK Tables or ODK Survey apps directory structure, when we delete the instance from ODK Collect, do not delete the media files (they are owned by Tables or Survey). This directory structure is under /sdcard/odk/xxxx/instances/yyyy/zzzz/.
  * fix: updating instance information did not update the last-status-change-date in the database.
  * fix: crash when no instanceName field present in metadata block (or, potentially, instanceId).
  * [issue 851](https://code.google.com/p/opendatakit/issues/detail?id=851) extension: if the bind element contains a jr:requiredMsg="" attribute, process this like a jr:constraintMsg and display it when data is not supplied. This is the new mechanism for supporting translations and customizations of these messages for, e.g., field-list groups.
  * fix: ActivityLogger (normally not enabled) could throw an error when logging some actions due to an unexpected null value.
  * project now depends upon playservices http://code.google.com/p/opendatakit/source/browse?repo=playservices (for new maps V2.0 API).

# Collect 1.3 #

## rev 1030 ##
  * fix force close during submissions.
  * [issue 804](https://code.google.com/p/opendatakit/issues/detail?id=804), [issue 805](https://code.google.com/p/opendatakit/issues/detail?id=805), [issue 817](https://code.google.com/p/opendatakit/issues/detail?id=817) - continued attempt to fix visibility issues in Google Play store.

## rev 1029 ##
  * [issue 804](https://code.google.com/p/opendatakit/issues/detail?id=804), [issue 805](https://code.google.com/p/opendatakit/issues/detail?id=805) add uses-feature declarations in attempt to restore lost visibility of ODK Collect in Google Play store.
  * [issue 800](https://code.google.com/p/opendatakit/issues/detail?id=800) - updated dutch translations.

## rev 1027 ##

  * fix Force Close when uploading submissions and there is an authentication failure.

## rev 1025 ##
Production release of ODK Collect.

Significant enhancements:
  * printer support on Android 3.1 and higher systems. See http://opendatakit.org/help/form-design/examples/#printing_widgets and http://opendatakit.org/use/sensors-framework/zebra-printer-driver/
  * configurable display and use of 'back' and 'next' buttons, and the enabling or disabling of swipe treatment within form pages (contributed work by Nafundi).
  * configurable background transmission of submissions when network connections are detected (contributed work by Nafundi).
  * application settings can now be saved to disk and loaded from disk (makes configuring device fleets easier). (contributed work by Nafundi).
  * [issue 401](https://code.google.com/p/opendatakit/issues/detail?id=401) - string prompts now can render a configurable-height text entry box, via the `rows` attribute on their `<input>` tag.
  * [issue 751](https://code.google.com/p/opendatakit/issues/detail?id=751) - compact (a.k.a. grid view) select-one and select-multiple prompts now can render text label arrays or icon arrays.
  * compact (a.k.a. grid view) select-one and select-multiple prompts, if given a fixed column width (e.g., compact-4), will rescale the images so that they exactly fit the natural width of the device. Images are not rescaled when the device is then rotated 90 degrees.
  * if a meta/instanceName field is defined in a form, the non-empty value of that field will be used as the instanceName when the form is saved (and the user will be unable to alter it). Otherwise, the pre-1.3 instance naming treatment will be used. By defining this as a calculated field, the form designer can control the naming of the submission instances.
  * if itext contains constraintMsg or requiredMsg forms, then these will be used for the constraint failure and required-but-missing error messages.

Other changes:
  * counts are now displayed on the main menu for the number of saved and unsent forms present on the device.
  * [issue 754](https://code.google.com/p/opendatakit/issues/detail?id=754)- improved network error handling when connections time out.
  * [issue 695](https://code.google.com/p/opendatakit/issues/detail?id=695) - color picker used by annotation, free drawing and signature prompts now works on smaller devices (supports scrolling).
  * [issue 692](https://code.google.com/p/opendatakit/issues/detail?id=692) - fix several issues around upload failures (may not be complete).
  * move strings that shouldn't be translated out of strings.xml
  * bug fix for Forced Close when clearing already-cleared form field.
  * [issue 780](https://code.google.com/p/opendatakit/issues/detail?id=780) - delay reporting a geolocation until the second location update. Attempted fix for stale geolocations being reported on some devices.
  * [issue 740](https://code.google.com/p/opendatakit/issues/detail?id=740) - updates to french strings.
  * [issue 739](https://code.google.com/p/opendatakit/issues/detail?id=739) - updates to lt strings
  * [issue 722](https://code.google.com/p/opendatakit/issues/detail?id=722) - updates to ka strings
  * [issue 690](https://code.google.com/p/opendatakit/issues/detail?id=690) - when ODK Aggregate protocol is selected, formList and submissions settings were not being reset to the paths ODK Aggregate uses.

# Collect 1.2.2 #

Production release of ODK Collect.

Addition of admin mode

## rev 1023 ##
  * fix admin preferences to properly default to historical non-admin behavior.
  * [issue 734](https://code.google.com/p/opendatakit/issues/detail?id=734), 735 - update string translations.
  * [issue 709](https://code.google.com/p/opendatakit/issues/detail?id=709) - hide "Get Location" button when geopoint widget is read-only.
  * [issue 713](https://code.google.com/p/opendatakit/issues/detail?id=713) add audio to "compact" appearance select1/select widgets (pressing icon will play associated audio clip)
  * various fixes for warnings reported by Android Lint tool
  * [issue 715](https://code.google.com/p/opendatakit/issues/detail?id=715) - retain filipino, copy translations to tagalog as well.

## rev 1022 ##
  * add audio to "compact" appearance select1/select widgets (pressing icon will play associated audio clip)

## rev 1021 ##
  * [issue 28](https://code.google.com/p/opendatakit/issues/detail?id=28) - provide admin mode restrictions on some user preferences
  * [issue 715](https://code.google.com/p/opendatakit/issues/detail?id=715) - filipino translation
  * [issue 722](https://code.google.com/p/opendatakit/issues/detail?id=722) - georgian translation

The admin mode functionality was sponsored by eHealth Nigeria. Admin mode enables you to customize what features in ODK Collect are available to your data collectors. For example, you can choose what buttons appear on the main screen so you can prevent collectors from accidentally editing or deleting saved forms. You can also prevent collectors from jumping to arbitrary prompts, renaming saved forms, or changing any of ODK Collect's settings unless they know the admin password.

# Collect 1.2.1 #

Production release of ODK Collect.

New widgets for: draw, signature, annotate

## rev 1020 ##
  * [issue 707](https://code.google.com/p/opendatakit/issues/detail?id=707), [issue 708](https://code.google.com/p/opendatakit/issues/detail?id=708) - fix more crashes...
  * [issue 700](https://code.google.com/p/opendatakit/issues/detail?id=700) - swahili translations (2nd rev)
  * remove extraneous URLDecodes (W3C correctness)
  * onPause issue -- not saving values on current screen

## rev 1019 ##
  * [issue 707](https://code.google.com/p/opendatakit/issues/detail?id=707), [issue 708](https://code.google.com/p/opendatakit/issues/detail?id=708) - Crash when swiping from one select one autocomplete screen to the next.
  * [issue 700](https://code.google.com/p/opendatakit/issues/detail?id=700) - swahili translations

## rev 1018 ##
  * fix for overlaid screen images on return from external app or hierarchy view

## rev 1017 ##
  * [issue 701](https://code.google.com/p/opendatakit/issues/detail?id=701) - Crash when using video prompts within a form

## rev 1016 ##
  * [issue 694](https://code.google.com/p/opendatakit/issues/detail?id=694) - out animation (and recycle of images) not being executed
  * [issue 698](https://code.google.com/p/opendatakit/issues/detail?id=698) - GPS accuracy was hardcoded to 5m - use accuracyThreshold="1.5" attribute in the `<input>` tag to change this to 1.5m
  * [issue 687](https://code.google.com/p/opendatakit/issues/detail?id=687) - keyboard does not drop
  * [issue 686](https://code.google.com/p/opendatakit/issues/detail?id=686) - collect crashes if SD card being 'checked'

## rev 1015 ##
  * updated german translations.
  * [issue 678](https://code.google.com/p/opendatakit/issues/detail?id=678) - crash on gathering signatures
  * sporadic crash on 4.x systems

## rev 1014 ##
  * widgets for new image appearances: draw, signature, annotate
  * [issue 663](https://code.google.com/p/opendatakit/issues/detail?id=663) - support autoplay of audio or video clips on forward swipe
  * [issue 662](https://code.google.com/p/opendatakit/issues/detail?id=662) - add version and id to form download result dialog

  * [issue 650](https://code.google.com/p/opendatakit/issues/detail?id=650) - crashes when deleting repeat -- updated javarosa
  * [issue 490](https://code.google.com/p/opendatakit/issues/detail?id=490), [issue 671](https://code.google.com/p/opendatakit/issues/detail?id=671), [issue 668](https://code.google.com/p/opendatakit/issues/detail?id=668) - crashes with many image captures
  * [issue 661](https://code.google.com/p/opendatakit/issues/detail?id=661) - groups with no relevant members shown on backward swipe.
  * fix authentication problems when a Google Account is specified (this account is not used for authentication, but, if specified, the e-mail address can be prepopulated into the form).
  * [issue 658](https://code.google.com/p/opendatakit/issues/detail?id=658) - remove Google Forms choices (feature not yet available)
  * [issue 620](https://code.google.com/p/opendatakit/issues/detail?id=620) - reduce width of scroll bar to not overlap most screen elements.
  * upgrade to use Apache 4.2.1 http client libraries (improved debuggability)

# Collect 1.2.0 #

Production release of ODK Collect.

## Upgrading from ODK Collect 1.1.7 ##

To upgrade, first ensure that all saved forms have been finalized and that there are no not-yet-sent forms remaining on the phone.  Then remove the old app and install the new one.

## New Features ##
  * Write savepoints after each swipe.  Savepoints enable ODK Collect to recover all the answers for an in-progress form after unexpected terminations or closures. When the user chooses Edit Saved Form, the savepoint will be used if it is newer than the last save of the form. If a form was never explicitly saved, if the user chooses Fill Blank Form, ODK Collect will restore to the savepoint of the last unsaved copy of that form. Savepoints take less than 1 second to perform for eIMCI on Droid and Ideos phones.
  * encrypted forms support http://opendatakit.org/help/encrypted-forms/.  Encrypts the form when marked as complete.  Data is in plaintext on the phone prior to marking as complete and plaintext data may remain afterward (under some conditions, captured media files may continue to be accessible via other apps on the device).
  * enable cascading selects as in KoBo Collect.
  * improve the display of select1 with "minimal" appearance.
  * add menu item on the Send Finalized Form screen to toggle display of either just the unsent forms or all the sent and unsent forms (allows users to more easily resend forms).
  * add preload properties for username, email, and uri-prefixed variants of these properties. The valid properties (case insensitive) are:
    * deviceId, uri:deviceId
    * subscriberId, uri:subscriberId
    * simSerial, uri:simSerial
    * phoneNumber, uri:phoneNumber
    * username, uri:username
    * email, uri:email
  * add "month-year" and "year" appearance to the date widget to display only the month and year, or only the year spinner, respectively.
  * do not list read-only widgets in the hierarchy view if they have no label string.
  * add support for launching an external app to obtain a string, integer or decimal value.  Support is via "ex:intentpath" appearance attribute on string, integer or decimal input widgets.  An example external app is BreathCounter here http://code.google.com/p/opendatakit/source/browse?repo=androidextras
  * support user interaction logging for all application interactions. This is disabled by default, but can be used to capture interactions for usability studies. See ActivityLogger.java for details.

## Bug Fixes / Enhancements ##

### rev 1013 ###
  * [issue 651](https://code.google.com/p/opendatakit/issues/detail?id=651) fix for errors relating to editing saved files that complained about ../ or ./ references. (updated javarosa library).
  * [issue 647](https://code.google.com/p/opendatakit/issues/detail?id=647) add position() predicate
  * fix for incorrect evaluation of XPath expressions with or conditions.
  * updated translations (ca, fr, vi, id)

### rev 1012 (1st release published to Google Play) ###

  * further cleanup around removal of image, audio and video files from content provider when interacting with those widgets and during deletion of saved forms.
  * remove audio and video files from content provider when deleting a filled-in instance.
  * [issue 391](https://code.google.com/p/opendatakit/issues/detail?id=391), correctly handle relaunching ODK Collect if your device has memory constraints and the OS forces ODK Collect to shut down during the launching of an external intent (e.g., video capture).
  * fix french translations (caused crash on save in 1010).
  * [issue 624](https://code.google.com/p/opendatakit/issues/detail?id=624) - null values have confusing display in minimal select1
  * [issue 619](https://code.google.com/p/opendatakit/issues/detail?id=619) - back swipe skips up out of simple group rather than to previous question within group
  * [issue 606](https://code.google.com/p/opendatakit/issues/detail?id=606) - editing a saved form does not do so in the chosen default form language.
  * [issue 604](https://code.google.com/p/opendatakit/issues/detail?id=604) - date and time widgets not saving values
  * [issue 601](https://code.google.com/p/opendatakit/issues/detail?id=601) - unused strings in ODK Collect
  * [issue 593](https://code.google.com/p/opendatakit/issues/detail?id=593) - support cascading selects -- 1.2.0 uses the KoBo Collect version of javarosa, so cascading selects are now possible.
  * [issue 571](https://code.google.com/p/opendatakit/issues/detail?id=571) - fixes for DateTime widget crashes on some devices
  * improvements around image/audio/video/gps capturing on field-list pages.
  * more consistent use of text size settings across all displays.
  * wider, always-visible, scroll bar
  * less confusing user experience -- clear user credential cache when changing username/password
  * change version to string value, handle version tag in formList (OpenRosa compliance)
  * remove all !uiVersion handling from code.
  * [issue 586](https://code.google.com/p/opendatakit/issues/detail?id=586) - display formlist in sorted form-name order (by date listing is not supported).
  * [issue 580](https://code.google.com/p/opendatakit/issues/detail?id=580) - fix for calculations returning integers causing fatal casting errors.
  * [issue 579](https://code.google.com/p/opendatakit/issues/detail?id=579) - fix form discovery bug that could result in multiple forms in the forms database.
  * improve display of forms using version attribute
  * [issue 532](https://code.google.com/p/opendatakit/issues/detail?id=532) - fix device id reporting for WiFi-only devices
  * [issue 570](https://code.google.com/p/opendatakit/issues/detail?id=570) - enable display of already-sent finalized forms via long-click on the Toggle button (supports resending those forms).
  * [issue 568](https://code.google.com/p/opendatakit/issues/detail?id=568) - fix preferences screen to not auto-capitalize, auto-complete.
  * general cleanup of cursors to close transient ones.
  * [issue 550](https://code.google.com/p/opendatakit/issues/detail?id=550) - string appearance "numbers" not handled properly
  * change to build against API level 8 (2.2) as 2.1 is obsolete.
  * add image widget "web" appearance to use WebView to enable panning and zooming (without clicking and launching the image viewer). Swipe gestures for advancing to the next question are disabled over the image area (swipe above or below the captured image).
  * silently retry file downloads before reporting failure (better user experience when using poor-quality WiFi).
  * hide buttons of read-only image/audio/video prompts.
  * upload at most 100 media attachments per transmission.
  * tweaks to ensure that HTTP entity streams are fully consumed.
  * share a common ClientConnectionManager.
  * [issue 282](https://code.google.com/p/opendatakit/issues/detail?id=282) image overlaps long text prompt
  * [issue 246](https://code.google.com/p/opendatakit/issues/detail?id=246) deletion of forms and instances occur in background tasks
  * some strings and code cleanup ([issue 494](https://code.google.com/p/opendatakit/issues/detail?id=494), [issue 488](https://code.google.com/p/opendatakit/issues/detail?id=488))
  * [issue 515](https://code.google.com/p/opendatakit/issues/detail?id=515) - crash during save when app pauses (screen goes dark)

### rev 1011 Pre-release ###

  * rename XML files that fail to parse so that you don't repeatedly get warned.
  * correct fr translations
  * fix rendering of forms with repeats that are not field-lists but contain groups that are (would display incorrectly).

### rev 1010 Pre-release ###

  * add missing copyright notices to all files
  * merge in activity logging (disabled by default). Tracks all clicks, swipes etc. application-wide (for user studies).
  * [issue 567](https://code.google.com/p/opendatakit/issues/detail?id=567), [issue 619](https://code.google.com/p/opendatakit/issues/detail?id=619) -- field-list navigations and display refresh after external app returns (e.g., barcode reader).
  * for the 'minimal' appearance select1 (spinner), display 'Select One Answer' in drop-down, in dark brown, if there is no selection (null).
  * [issue 369](https://code.google.com/p/opendatakit/issues/detail?id=369) -- add menu option to allow user to select already-sent submissions for re-submission.
  * send uri:deviceId value up to server on submission request.
  * fix displays when working with 0.9.x servers
  * [issue 472](https://code.google.com/p/opendatakit/issues/detail?id=472), [issue 604](https://code.google.com/p/opendatakit/issues/detail?id=604) ensure data is saved before moving off of page (different behaviors across 2.x and 4.x devices)
  * [issue 282](https://code.google.com/p/opendatakit/issues/detail?id=282) - prompt image overlaps long prompt text
  * clean up double/integer conversions
  * update to KoBo-based javarosa library (supporting cascading selects)
  * [issue 606](https://code.google.com/p/opendatakit/issues/detail?id=606)
  * many language translations updated

### rev 1009 Pre-release ###

  * add ExString, ExDecimal, ExInteger widgets for launching external apps to retrieve a string, decimal or integer value.
  * [issue 432](https://code.google.com/p/opendatakit/issues/detail?id=432) -- add 'month-year' and 'year' appearances for Date widget.
  * [issue 532](https://code.google.com/p/opendatakit/issues/detail?id=532) -- fix of update deviceId to use WiFi MAC address for phones that don't have imei cards.

### rev 1008 RC2 ###

  * correct a few bugs in the database upgrade logic from 1.1.7 to 1.2.x databases (allows seamless upgrades from 1.1.7 to 1.2, though we still recommend getting all data off the phone before updating).
  * translations

### rev 1008 RC1 ###

  * [issue 532](https://code.google.com/p/opendatakit/issues/detail?id=532) -- use MAC address for WiFi-only devices.
  * OpenRosa compliance: change to use string-valued version strings (vs. integer-valued ones).
  * make vertical scroll bar wider, always-on, and more visible.
  * [issue 571](https://code.google.com/p/opendatakit/issues/detail?id=571) - fix date widget
  * [issue 572](https://code.google.com/p/opendatakit/issues/detail?id=572) - missing translations
  * [issue 570](https://code.google.com/p/opendatakit/issues/detail?id=570) - add functionality to select already-sent forms for sending.
  * [issue 568](https://code.google.com/p/opendatakit/issues/detail?id=568) - do not auto-cap or complete preferences screen entries
  * improve credential-changing logic and logging
  * display forms alphabetically in form lists by form's display name
  * improve robustness of swipe detection logic (bad values on some phones)
  * fix issues where 2 or more disk sync tasks could report the same form as present repeatedly, causing database errors.
  * apply question font size preference to all widgets

### rev 1007 RC1 ###

  * string widget with 'numbers' appearance can accept up to 255 characters (was capped at 10).

### rev 1006 RC1 ###

  * change image view to use webView for displaying images (reverted later to the old functionality and retained this as a special apppearance tag).
  * updated translations

### rev 1005 RC1 ###

  * try downloading files twice before abandoning the attempt (for slow connections).

### rev 1004 RC1 ###

  * hide capture and choose buttons on audio, video and image widgets when they are read-only

### rev 1003 RC1 ###

  * fix: always save answers in-order when advancing off of a field-list.

### rev 1002 RC1 ###

  * no such tag

### rev 1001 RC1 ###

  * fix crash in date, datetime widgets on Android 3.0+.
  * fix sleep/pause crash
  * improve display/status tracking in management screens
  * fix save-to-disk error (along one path).

### rev 1000 RC1 ###

  * branch from 1.1.7