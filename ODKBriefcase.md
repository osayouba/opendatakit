

# ODK Briefcase #

Briefcase is a standalone application used to download forms and submissions from ODK Aggregate 0.9.8.1 and higher and from ODK Aggregate 1.0 Production and higher.

## Recovering Data when Encrypted Form is Corrupted ##

Beginning with 1.4, ODK Briefcase attempts to recover corrupted encrypted XML files. It is not able to recover corrupted media attachments. Encrypted files can be corrupted when running on Android 4.3 systems and using ODK Collect 1.2.x or 1.3.x. The corruption is due to the failure of the Android 4.3 device to emit the final 1 -to- 15 bytes (characters) of the file.

If the XML file corruption cannot be automatically corrected, you can manually access the corrupted files by creating a 'debug' directory underneath the ODK Briefcase Storage location. If that directory exists, the corrupted XML files will be written to submission-nnnnn.xml files in that directory.

## Recovering Data when Form Definition Corrupted ##

Beginning with 1.2.1, ODK Briefcase can recover data from an ODK Aggregate server after the form definition file on that server has become corrupted.

To do this:
  1. on the server, be sure that 'Accept Submissions' is unchecked for the form with the bad form definition file.
  1. on your computer, launch ODK Briefcase 1.2.1 or later, and see where the `ODK Briefcase Storage Location` is.  Change this location to a different empty folder on your system.
  1. choose to pull the form with a corrupt form definition from your server. <font color='red'>You can only recover one such corrupted form at a time.</font>
  1. ODK Briefcase will report a download error.
  1. in the file browser, copy a correct form definition file into the `.../forms/_badForm` directory and rename that xml file to `_badForm.xml`
  1. return to the ODK Briefcase app. repeat the download.
  1. download should complete.

At this point, you should delete the form on the server (this can take some time to complete if you have many submissions).  To determine when the delete is complete, you can manually attempt to upload the good form definition to the server. This will fail until the form is fully deleted.  Once the form is deleted from the server, you can use ODK Briefcase to Push the form and data up to the server, restoring the server contents.

To process another corrupted form, you should repeat all these steps.

## Release Notes ##

### Briefcase 1.4.5 Production ###
  * update to javarosa-2015-01-10.jar
  * fix: CLI changes broke forms with repeat groups.
  * fix: handle marked-as-complete encrypted submissions (only works with updates to ODK Aggregate)
  * fix: if there is a network transmission error, drain all data on the socket.
  * fix: handle oversized formHub version strings

#### Upgrading ####

No special actions when upgrading from ODK Briefcase 1.2.3 or later.

### Briefcase 1.4.4 Production ###
  * add command-line interface (contributed by Nafundi)

#### Upgrading ####

See upgrade notes for Briefcase 1.2.3 Production.

### Briefcase 1.4.3 Production ###
  * update to newer javarosa library.
  * add SurveyCTO support for recoding csv files to UTF-8 for data preloading
  * add tab ordering in support of scripting
  * fix [issue 676](https://code.google.com/p/opendatakit/issues/detail?id=676) - create the temp storage under the csv export directory
  * fix [issue 821](https://code.google.com/p/opendatakit/issues/detail?id=821) - if rename fials, try copy-and-delete
  * fix [issue 840](https://code.google.com/p/opendatakit/issues/detail?id=840) - eliminate punctuation characters in form names

#### Upgrading ####

See upgrade notes for Briefcase 1.2.3 Production.

### Briefcase 1.4 Production ###
  * update to newer javarosa library.
  * recovery code for [issue 918](https://code.google.com/p/opendatakit/issues/detail?id=918) - encrypted files are corrupted on Android 4.3
  * fix [issue 786](https://code.google.com/p/opendatakit/issues/detail?id=786) - repeat groups not properly analyzed.
  * fix infinite loop if you select public key file for decryption

#### Upgrading ####

See upgrade notes for Briefcase 1.2.3 Production.

### Briefcase 1.3.2 Production ###
  * relax [issue 807](https://code.google.com/p/opendatakit/issues/detail?id=807) to still use OS X dialog on all Java 1.6 apps.
  * fix crash that prevents opening of ODK Briefcase on 2nd run.

#### Upgrading ####

See upgrade notes for Briefcase 1.2.3 Production.

### Briefcase 1.3.1 Production ###

  * [issue 807](https://code.google.com/p/opendatakit/issues/detail?id=807) - change directory not working on OS X
  * code changes for compiling with 1.7 (still targets 1.6 JRE).

#### Upgrading ####

See upgrade notes for Briefcase 1.2.3 Production.

### Briefcase 1.3 Production ###

No code changes. Just a version bump to make it clear that this works with the 1.3 tools.

#### Upgrading ####

See upgrade notes for Briefcase 1.2.3 Production.

### Briefcase 1.2.3 Production ###
  * correct several issues related to updating/writing of files using non-UTF-8 character sets (e.g., chinese-language Windows systems).

#### Upgrading ####

Users of 1.2.1 that have non-latin characters in their files should first verify that the 1.2.3 Briefcase can read and export that data to CSV without corrupted strings. If they cannot, they will need to use 1.2.1 to upload the data to an ODK Aggregate instance. Then use 1.2.3 to pull down to a different Storage location -- i.e., use a different storage location for 1.2.3 onward than they were using for 1.2.1 and earlier.  Should work with all Aggregate versions.

If you downloaded 1.2.2 and have corrupted data files (this would only affect the files transferred while using 1.2.2), you may need to find, manually remove and re-pull those data files.

### Briefcase 1.2.2 Production ###
  * correct several issues related to opening of files using non-UTF-8 character sets (e.g., chinese-language Windows systems).

#### Upgrading ####

No special actions.  Should work with all Aggregate versions.

If you have been able to download and export your data, you should not be affected by this issue, as it would typically cause the parsing of the data file to fail.

### Briefcase 1.2.1 Production ###
  * add clarifying text to server connection dialog w.r.t. what to enter for a username
  * add code path for recovering data from a server where the form definition has become corrupted.

#### Upgrading ####

No special actions.  Should work with all Aggregate versions.

### Briefcase 1.2 Production ###
  * fix issue where OSX mounted SD Cards didn't work.
  * keep track of form version and update local form definition only if the form definition being pulled is newer than the local form definition.
  * new javarosa version supporting cascading selections.

#### Upgrading ####

No special actions.  Should work with all Aggregate versions.

### Briefcase 1.1 Production ###
  * code restructuring to share classes with Aggregate 1.1

#### Upgrading ####

No special actions.  Should work with all Aggregate versions.

### Briefcase 1.0.2 Production ###
  * increase server connection timeouts to 60 seconds.
  * limit number of media files uploaded to 100 per transmission.

#### Upgrading ####

No special actions need to be taken to upgrade from 1.0.x Production. For upgrades from earlier versions, see upgrade notes for 1.0 Production.

### Briefcase 1.0.1 Production ###
  * Fixes [issue 526](https://code.google.com/p/opendatakit/issues/detail?id=526) - data values misaligned to headers.

#### Upgrading ####

No special actions need to be taken to upgrade from 1.0 Production. For upgrades from earlier versions, see upgrade notes for 1.0 Production.

### Briefcase 1.0 Production ###
  * Fixes csv generation problem with repeat groups.
  * Intelligently determines the submissions that must be transferred when incrementally uploading or downloading data to an Aggregate 1.0 server. See [Incremental Transfers](ODKBriefcase#Incremental_Transfers.md) for the limitations of this feature.
  * Supports exports of encrypted forms.

#### Upgrading ####

If you are using an earlier version of Briefcase, the storage area needs to be renamed "ODK Briefcase Storage" and you need to specify the directory where that is located as the ODK Briefcase Storage Location.

### Briefcase 1.0 Beta 2.1 ###
  * Fixes datetime parsing issue that would cause +/-1 rounding errors in the millisecond digit.

### Briefcase 1.0 Beta 2 ###

  * Fixes issue that was failing to preserve dates and times as submissions were transferred across Aggregate instances.
  * Fixes several issues with respect to verifying the proper structure of the chosen local Briefcase directory.

## Running with Error Logging ##

If you have a problem with ODK Briefcase, please capture the errors that ODK Briefcase sends to its console and opening an issue with the error log attached. This will greatly assist us in diagnosing and resolving your issues.

To do this:

  1. Open a cmd window (Start Menu, type 'cmd', select it) or a Terminal window on MacOSX.
  1. cd to the directory containing the ODK Briefcase jar file (the one you normally doouble-click). The easiest way to do this on Windows is to open a file browser, navigate to that directory, click into the file path field at the top of the file browser, select-all and copy to the cut-and-paste buffer. Then click on the cmd window, type `cd ` (with a space after the cd), paste in the path, and hit Enter.
  1. verify that java is available by running `java -version`
  1. does this display the version information for java? If not, you can replace 'java' with the full path to the java.exe program (surrounded by double quotes). e.g., `"C:\Program Files\Java\jre7\bin\java.exe" -version`
  1. now, run this command after replacing the "ODK Briefcase...Production.jar" with the proper name of your jar file (keeping the double quotes around it), and making any necessary change to the 'java' portion:
```
java -jar "ODK Briefcase v1.... Production.jar" >>errors.log 2>&1
```

ODK Briefcase will now open (somewhat more slowly than before, because it is spewing a lot of data into the errors.log file). Do whatever you wanted to do that caused ODK Briefcase to crash or fail.

Then exit ODK Briefcase and open an issue http://code.google.com/p/opendatakit/issues/list and attach this file, or a zip of this file, to that issue (there is a little attach a file link below the comment box); do not copy-and-paste the content into the issue -- please attach the file. These files are extremely large. Please refrain from sending them to the opendatakit@ list.

## Future ##

Submit a feature request at http://code.google.com/p/opendatakit/issues/list.

## Incremental Transfers ##

Efficient incremental transfers only work when transferring data among Aggregate 1.0 servers.

If you are pulling data from an Aggregate 0.9.x server, you must push the data to Aggregate 1.0, and then pull that data from Aggregate 1.0 (thereby maintaining a complete local copy of all the data on the Aggregate 1.0 server), in order to gain the benefits of incremental transfer.

Similarly, if you are pulling data off an ODK Collect phone, you must push it to Aggregate 1.0, and then pull the data from Aggregate 1.0 (thereby maintaining a complete local copy of all the data on the Aggregate 1.0 server), in order to gain the benefits of incremental transfer.

This means that if you only use ODK Briefcase to bulk-upload data from a set of ODK Collect phones, you should start with an empty briefcase storage area, pull the data off each of the phones, and, finally, push the data to Aggregate.  Upon successful completion, you should then delete the briefcase storage area (archiving it before deletion if you want to maintain a local copy of all data uploaded from this local machine to Aggregate).  You should not continue to use a non-empty storage area (unless that area contains a full copy of all the data on the Aggregate 1.0 server).

Reusing a non-empty briefcase storage area without ever pulling data down from the Aggregate 1.0 server will cause all the data in that storage area to be pushed to the server every time.  The pulling of the data is the important part; if you are transferring data between Aggregate 1.0 instances, because you are pulling data from the first Aggregate 1.0 instance, all data transfers will be incremental transfers.

This behavior occurs because Briefcase relies on the Aggregate 1.0 server to determine whether or not a submission is marked as complete. This determination is different from ODK Collect's "Mark form as finalized" selection.  Finalizing a form marks it as ready for uploading. A form is complete once it and all its associated attachments have been successfully uploaded to ODK Aggregate 1.0.

Until a filled-out form (submission) is marked as complete, it will always be pulled from or pushed to an Aggregate server, in the hopes that some other tool has supplied the missing attachments and made the submission complete.

Hence, if you are pulling data from Aggregate 0.9.x or ODK Collect, that data is not marked as complete as it enters briefcase (because neither ODK Aggregate 0.9.x nor ODK Collect are authorities for the marked-as-complete attribute).  This data will continue to be pulled on every attempt.  In order to gain the benefits of incremental transfers, you must push the data to an Aggregate 1.0 server and then pull the data from the server.  The pull updates the marked-as-complete status and ensures that you have the full and complete copy of every submission on your local machine.  After that, incremental transfers will not pull any submissions marked-as-complete on your local machine, and will not push them to an Aggregate 1.0 instance if that instance also has them marked as complete.

## Source ##

The source code for Briefcase is located in the Briefcase repository.  Briefcase and FormUploader share the same project but have different main() routines.

To build briefcase from source, from within Eclipse, choose to Export..., choose Runnable jar, and select either the Briefcase main() for ODK Briefcase, or the FormUploader main() for ODK FormUploader.

During the export process, be sure to select **Package required libraries into generated JAR**. The cryptography libraries make extensive use of dynamic class loading and may perform runtime verification of their jar signatures.

If you instead choose **Extract required libraries into generated JAR**, then the resulting runnable jar will fail with
```
java.security.NoSuchAlgorithmException: Cannot find any provider supporting RSA/NONE/OAEPWithSHA256AndMGF1Padding
```
or a similar error.