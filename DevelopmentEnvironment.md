# For Implementers #
## Installing the Emulator ##
_Note: The Android emulator is slow and buggy. If you can deploy on a real Android device, we highly recommend that approach._
  1. The emulator comes with the Android SDK. Follow the instructions at [downloading the SDK starter package](http://developer.android.com/sdk/installing.html#Installing). You do not have to install Eclipse or the ADT plugin at this stage.
  1. Install the version of Android SDK (1.5, 1.6, 2.0, 2.1, 2.2) you'd like. Follow the instructions for [adding components](http://developer.android.com/sdk/adding-components.html). We recommend SDK Platform Android 2.2, Android SDK Tools, and Documentation for Android SDK. If you are using Windows, also get the USB Driver.
  1. Create an Android Virtual Device -- the configuration information for the emulator. Follow the instructions at [for creating an AVD](http://developer.android.com/guide/developing/eclipse-adt.html#AVD). We recommend Android 2.2 with a 512MiB SD card. Leave the other settings at defaults.
  1. Once you create your AVD, you can select and start it.
  1. Email [opendatakit@googlegroups.com](http://groups.google.com/group/opendatakit) with questions.

# For Developers #

## Setup for Android ##
  1. [Install Eclipse, Android SDK, ADT Plugin](http://developer.android.com/sdk/index.html)
  1. [Install Hello World](http://developer.android.com/resources/tutorials/hello-world.html)
  1. [Read HgInit](http://hginit.com), [Install Mercurial](http://mercurial.selenic.com), [MercurialEclipse](http://www.javaforge.com/project/HGE)
  1. Email [opendatakit-developers@googlegroups.com](http://groups.google.com/group/opendatakit-developers) with questions.

## Setup for AppEngine ##
  1. [Install Eclipse, Java SDK, Google Plugin](http://code.google.com/appengine/docs/java/gettingstarted/installing.html)
  1. [Install Guestbook](http://code.google.com/appengine/docs/java/gettingstarted/creating.html)
  1. [Read HgInit](http://hginit.com), [Install Mercurial](http://mercurial.selenic.com), [MercurialEclipse](http://www.javaforge.com/project/HGE)
  1. Email [opendatakit-developers@googlegroups.com](http://groups.google.com/group/opendatakit-developers) with questions.

## Setup for ODK 2.0 Tools ##
  1. Pull required projects from the remote repository into a single parent directory, containing a child directory for each project
  1. Ensure child folders containing each project are named "opendatakit._projectname_" for each respective project
  1. Using hg update, update each project to _development tip_ (rather than default -- note that PlayServices has no development tip at this time)
  1. Import the entire directory into Eclipse using _Import Exisiting Project_