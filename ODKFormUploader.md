

# ODK Form Uploader #

Form Uploader is a standalone application used to upload form definitions and their associated media files to ODK Aggregate 1.0 Production and higher.

## Release Notes ##

### Form Uploader 1.4.5 Production ###
  * updated to javarosa-2015-01-10 jar.

### Form Uploader 1.4.4 Production ###
  * no significant changes. Just updating to match Briefcase version.

### Form Uploader 1.4.3 Production ###
  * no significant changes. Just updating to match Briefcase version.

### Form Uploader 1.3.2 Production ###
  * relax [issue 807](https://code.google.com/p/opendatakit/issues/detail?id=807) to still use OS X dialog on all Java 1.6 apps.
  * fix crash that prevents opening of ODK Briefcase on 2nd run.

### Form Uploader 1.3.1 Production ###

  * [issue 807](https://code.google.com/p/opendatakit/issues/detail?id=807) - change directory not working on OS X
  * code changes for compiling with 1.7 (still targets 1.6 JRE).

### Form Uploader 1.3 Production ###

No code changes. Just a version bump to make it clear that this works with the 1.3 tools.

#### Upgrading ####

No special upgrading instructions.

### Form Uploader 1.2.2 Production ###
  * correct several issues related to opening of form definition files using non-UTF-8 character sets (e.g., chinese-language Windows systems).

#### Upgrading ####

No special upgrading instructions.

### Form Uploader 1.2.1 Production ###
  * add clarifying text to server connection dialog w.r.t. what to enter for a username

#### Upgrading ####

No special upgrading instructions.

### Form Uploader 1.2 Production ###
  * updated with code changes from ODK Aggregate 1.2 and Briefcase 1.2

#### Upgrading ####

No special upgrading instructions.

### Form Uploader 1.1 Production ###
  * code restructuring to share classes with ODK Aggregate 1.1

#### Upgrading ####

No special upgrading instructions.

### Form Uploader 1.0.2 Production ###
  * increase server timeout to 60 seconds.
  * limit number of media files uploaded to 100 per transmission.

### Form Uploader 1.0 Production ###
  * initial release

## Source ##

The source code for FormUploader is shared with Briefcase and is located in the Briefcase repository.  Briefcase and Form Uploader share the same project but have different main() routines.

To build, from Eclipse, build Briefcase then choose Export..., choose Runnable jar, and specify the FormUploader main().