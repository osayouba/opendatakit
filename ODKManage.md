# ODK Manage #
**Manage is in an early stage of development and has not been officially released.**

## Overview ##
ODK Manage consists of a mobile application and a web service.

The ODK Manage web service maintains a centralized list of phones, the actions (e.g., tasks) assigned to the phones, and the status of each of those actions (pending, successful, failed).

The design intent is to use SMS to signal the phone that there are tasks to perform.  <font color='red'>The current implementation does not have a functioning SMS implementation.</font>  The intent is to integrate clickatell.com or some similar web-based SMS service into the product.

In lieu of a functioning SMS service, and as a fallback if SMS service is not available (e.g, no SIM card), the mobile application periodically polls the ODK Manage service to detect new tasks.

Tasks that are currently supported are:
  1. trigger the download of a form to the phone, <font color='red'>This functionality, while present, does not work on the Android side on some versions of ODK Collect.  The current ODK Collect code tip should now find these Managed-downloaded forms.</font>
  1. trigger install of an application on the phone, (e.g., for in-the-field updates)

Additionally, the server also supports sending free-form SMS messages to a phone (subject to a functional SMS implementation).

<font color='red'>This software is a prototype for a more-complete task-assignment mechanism.</font>  In particular, the formats of the SMS messages and the http communications have not been openly documented and will certainly change.

It is likely that assigning tasks to the phone user would leverage the form-download mechanisms to send an xform with read-only values for some fields (e.g., assignment, location, deadline) and writable values for others (e.g., time completed, is follow-up required).  Submission of this form would indicate completion of the task assigned to the phone user.

The original design document for this application is [ODKManageDesignDocument](ODKManageDesignDocument.md).

The readme on its usage is [ODKManageReadMe](ODKManageReadMe.md).

## Installation ##
ODK Manage has two components: the **Manage Client**, which runs on the <a href='http://www.android.com/'>Android</a> platform, and the **Manage Server**, which runs on <a href='http://code.google.com/appengine/'>Google App Engine</a>.

### Download ODK Manage ###
ODK Manage can be downloaded from the <a href='http://code.google.com/p/opendatakit/'>
Google Code site</a>. Click on the <strong>Source</strong> tab for access to the Mercurial repository. This contains folders for the mobile component, the server, and a pre-built server war tree. The <strong>mobile</strong>
folder contains the Android client code; the <strong>server</strong> folder contains the build tree for the server code for Google App Engine. The <strong>war</strong> folder contains a deployment-ready war tree for deploying the server to the Google App Engine.

### Setting up the Manage Server ###
  1. <a href='http://appengine.google.com'>Sign up</a> for an App Engine Account. Create an application to host ODK Manage.
  1. <a href='http://code.google.com/appengine/downloads.html'>Download</a> the <strong>Java</strong> app engine SDK.
  1. Edit {odk-manage}/war/WEB-INFO/appengine-web.xml, and insert your appengine application name in the <strong>Application</strong> field.
  1. Edit {odk-manage}/war/WEB-INFO/appengine-web.xml, and edit the list of allowed users in the <strong>allowedUsers</strong> system property.  This is a comma-, space- or semicolon- delimited list of email addresses.  An attempt is made to sanity-check the list for badly-formed e-mails upon receipt of the first well-formed request to the Manage server.  If any email address in the list is invalid, all access to the server will be denied.
  1. In the terminal, go to the folder where you installed the Google App Engine SDK (it will probably be called <strong>google_appengine</strong>). Type <strong>.\appcfg.cmd update {odk-manage}\war</strong> in Windows or <strong>./appcfg.sh update {odk-manage}/war</strong> in Unix/Mac. You will be prompted for your gmail address and password. After entering them, it should successfully install the ODK Manage Server.
  1. Do a test run to make sure everything's working. Visit the server at <strong>{application-name}.appspot.com</strong>. You should be prompted for gmail credentials; you'll need to enter a gmail account that you listed in appengine-web.xml! You can also see the status of your application by going to <a href='http://appengine.google.com'><a href='http://appengine.google.com'>http://appengine.google.com</a></a> and clicking on the application. The <strong>Logs</strong> tab is a great way to see what's happening and diagnose any problems.
  1. You now have an empty ODK Manage server. To use the form-download feature, you should also configure an ODK Aggregate instance (ODK Manage does not store forms itself, but references an ODK Aggregate instance to obtain the list of forms, etc.).
  1. Next, you'll want to install the ODK Manage client on your Android phone.

### Setting up the Manage Client ###

If you just want to install a client with all the default settings, you can download and install a compiled APK by pointing your Android browser at the latest Odk Manage Client release on the <a href='http://code.google.com/p/opendatakit/downloads/list'>downloads page</a>.

  1. Open the web browser on your android phone. Go to http://code.google.com/p/opendatakit/downloads/list.
  1. Click on the version of OdkManageClient\_xxx.apk that you want.
  1. In the applications folder, click on ODK Manage, go to settings, and configure them.

If you want to customize the client and compile it on your own, follow these instructions:
  1. <a href='http://developer.android.com/sdk/1.5_r3/index.html'>Download</a> the Android SDK.
  1. <a href='http://developer.android.com/sdk/eclipse-adt.html'>Download</a> the ADT plugin for Eclipse. If you don't have Eclipse, you can download it <a href='http://www.eclipse.org/'>here</a>. You can technically compile/develop Android code without Eclipse, we wouldn't advise it.
  1. Create a new Android Project in Eclipse, and use Existing Source, pointing Eclipse to {odk-manage}/mobile. In the Eclipse Android Project setup wizard, you can use any project name and any application name. Use the package name <strong>org.odk.manage.android</strong>, and do not create an Activity.
  1. In Eclipse, open up {ODK Manage}/src/org.odk.manage.android/Constants.java. At the top of the file, there are some fields that you may want to customize. In particular, you can customize the defaults for what server Manage will connect to, and whether it will use GPRS, SMS, or just Wifi for its communications. Read the <a href='#comm_strategy'>Manage communication strategy</a> section for more information on how Manage uses GPRS, SMS, and Wifi.
  1. Right click on the project, and go to <strong>Run->Android Application</strong>. This should run the application in an emulator or on your phone if its plugged in. You can find the APK at {odk-manage}/mobile/bin/**.apk. You can post this APK online and point Android browser at it to download it. If you want to install an APK on a non-development phone, you will need to**<a href='http://developer.android.com/guide/publishing/app-signing.html'>sign the application</a>.

### Having Problems? ###
First, check the [bug tracker](http://code.google.com/p/opendatakit/issues/list) to see if the issue you are having has been reported. If it has not, please file a bug. Urgent questions should go to the [mailing list](http://groups.google.com/group/opendatakit).