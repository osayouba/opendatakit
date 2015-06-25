# Introduction #

This page details how to get a copy of the application log off your phone or device so that it can be sent to the developers to resolve issues with ODK Collect.

# Details #

There are two ways to proceed:
  1. install the Android SDK on your computer, attach the phone to the computer, and capture the log using the Android SDK (recommended for Android 4.1 and above).
  1. install a log capture tool on your phone (recommended for older Android operating systems).

## Install Android SDK ##
Download the latest Android SDK from [http://developer.android.com/sdk/index.html](http://developer.android.com/sdk/index.html).

Install the SDK on your computer.

On older devices (2.x), go to Settings / Applications / Development and check the "USB debugging" checkbox.

On newer devices (3.x and 4.x), the Development screen is reached under Settings / System / Development. This selection is normally not visible. To reveal it, go to Settings / About Device and tap the "Build Number" field (at the bottom of the screen) about 9 times.  When you back out of the About screen, you will now see the Development selection, an can click that, and then check the "USB debugging" checkbox.

Connect your device to your computer with a USB cable.

Open a cmd window (terminal window on OSX and linux), and issue the command:
```
adb logcat
```
If 'adb' is not found, you may need to specify the full path to the adb program. On Windows, this will be something like:
```
"C:\Program Files\Android\android-sdk\platform-tools\adb" logcat
```
If the Android SDK were installed to "C:\Program Files\Android\android-sdk\"

This should produce a very long display of the log entries, and leaves the program running so that it can display additional log entries as they occur. You want to remember roughly how long it took before it was merely just logging events as they occurred.

Control-c out of that program.

Now, exercise ODK Collect on your device to reproduce the Force Close problem.

And then issue the adb command as before, but redirecting output to a file:
```
adb logcat > logfile.txt
```
And after you've given it enough time to write out the file (i.e., start logging just the events as they occur), hit Control-c.

Attach this file to an issue you open [http://code.google.com/p/opendatakit/issues/list](http://code.google.com/p/opendatakit/issues/list) describing the steps you used to produce this problem.

Also, attach the form definition file you were using when the issue occurred.

## Install a Log Capture Tool ##
This is no longer recommended for Android 4.1 and above (because devices need to be rooted on these newer operating systems).

The best tool I've found is [https://play.google.com/store/apps/details?id=com.nolanlawson.logcat](https://play.google.com/store/apps/details?id=com.nolanlawson.logcat).

Two caveats:
  * It might not be available in your country. Try [aLogcat](https://code.google.com/p/alogcat/downloads/list) instead.
  * Under Android 4.1 or greater, root is required; on those operating systems, we recommend using adb. It is more complicated to set up, but does not weaken your phone's security.

Install this app, run ODK Collect to demonstrate the Force Close issue, then open CatLog.

CatLog starts  -- it will display a color-coded scrolling list of log entries.

Choose Menu, Send, As Attachment, Gmail, and send the log to your linked e-mail account, or other account, as appropriate.

Once you receive the e-mail, download the attachment and attach it to an issue you open [http://code.google.com/p/opendatakit/issues/list](http://code.google.com/p/opendatakit/issues/list) describing the steps you used to produce this problem.

Also, attach the form definition file you were using when the issue occurred.