# Overview #

ODK Manage is a tool in the Open Data Kit (ODK) toolset. ODK Manage is designed to track and manage devices throughout an ODK deployment.

ODK Manage allows a deployment manager to automatically keep track of all the devices in a deployment, including IMEI number, phone number, username, and more, all from a web site hosted on the Google App Engine platform. Devices using ODK Manage automatically register their information with the web server and update it whenever it changes.

ODK Manage also allows managers to schedule maintenance operations on registered devices. These operations include downloading forms to ODK, and installing or updating software on the phone.

# Using ODK Manage #

<font color='red'>This is prototype code.  There are likely to be significant changes in the functionality and features of this application as we go forward.</font>

## Configuring a phone ##

Once you have ODK Manage client on a phone, you will need to configure it (unless you have compiled Manage with the correct defaults). Open the ODK Manage application on the phone, and click on Settings. You will want to set your user ID, set Server URL to the base URL of the Manage server (e.g. manage1.appspot.com), and you will probably want to turn off "Use SMS" (unless you have SMS configured on your server).

Once Manage is installed, your phone should automatically register itself with the Manage server when it is connected to the internet, but you can speed up the process by clicking on "Register Phone".

## The Manage admin web page ##

The manage admin page is located at the root of your appengine instance (e.g. manage1.appspot.com).

When you get to the admin page, you will be asked to sign in. If your gmail account is not in the account list in AdminAccountConfig.java, you will not be able to access Manage. We need to be careful about security, because Manage admins have a lot of power over their deployed phones (e.g. they can install any program on any phone).

Once you are on the admin page, you should see a table with all of your deployment's registered devices in it. If no devices are registered, the table will be empty. Each row in the table will have several different columns:

  * **IMEI:** The IMEI of the device.
  * **User ID:** The User ID entered on the settings page of the ODK Manage client.
  * **Phone #:** The phone number of the devices; blank if the SIM card is not installed.
  * **Tasks:** The number of tasks of each type (yellow=pending, green=success, red=failed) for this device.
  * **View Tasks:** Click here to view the tasks for this phone and delete tasks..
  * **SMS:** If this field says Yes, then you can send SMS to this device.
  * **Last Contacted:** The last time this device checked in with the server, either by registering, asking for new tasks, or giving a status update.

If a device has pending tasks, that device will appear yellow. If a device has failed tasks, that device will appear red. You can remove pending or failed tasks from a device by clicking **View Tasks** for that device.

## Actions/Tasks ##

At the top of the admin console there is a **Perform Action** box. The items in this drop-down menu are actions you can perform on phones. To perform an action, select the action you want to perform from the menu, and fill in the required fields. You also need to select which devices you want to perform the action on, by checking their checkboxes.

Some actions are **Task** actions; that is, they schedule a particular task to be executed on the selected devices.

The following tasks are available in the current version of ODK Manage:

  * **Add Form:** Add a form to ODK Collect's form list on the phone.
> > You need to provide the URL where the form is located on the web. This must be entered in standardized format, e.g. http://www.example.com/forms/myForm.xml.


> If you are using ODK Aggregate to store your forms, just enter the URL of your aggregate application (e.g. http://aggregate.appspot.com), and you will be able to choose from a list of Aggregate's forms when you choose Add Form.
  * **Install Package:** Install or re-install a software package (e.g. ODK Collect) on the devices.
> > This task makes it easy to push software updates or new software to deployed phones. You can even send new versions of ODK Manage.


> You need to be very careful in how you fill out the fields for an Install Package task. Under package URL, enter the URL where the package APK file is located in standardized format, e.g. http://www.example.com/apk/OdkCollect.apk. Under package name, you must enter the actual package name (not file name) for the apk you are installing. For example, ODK Collect has the package name org.odk.collect.android. If you don't set the package name correctly, Manage will not be able to detect when the package is installed. The easiest way to find the package name for a package is to look at AndroidManifest.xml in the source code, and look for something like 

&lt;manifest package="org.odk.manage.android"&gt;

 right at the top. If you don't have the source code, it is pretty tricky: one option is to install the package on a phone and use **adb logcat** to find out the package name when it's added. We are looking for ways to simplify this process.

There are also other actions you can perform in the actions panel that do not schedule tasks. The actions provided by the current version are:

  * **Send New Task Notification SMS:** Sends an SMS message to the selected phones with a special keyword that notifies the Manage client that there are new tasks on the server.
> > This prompts the client to connect to the server and download the task list. The SMS can also contain a message (e.g. 'enter data range as soon as possible'). This is helpful in configurations where the client is configured to check in with the server on a very infrequent basis, or never. **Note:** You can only perform this action if the Manage server is configured to use SMS - if the SMS column for your devices say Yes, then you're good to go.

  * **Send SMS Message:** Sends an SMS with your message to the selected devices. **Note:** You can only perform this action if the Manage server is configured to use SMS - if the SMS column for your devices say Yes, then you're good to go.

It is possible to develop new actions or tasks for ODK Manage. See the **Customizing Actions or Tasks** section.

## How/when tasks will be executed on the phone ##

There are various triggers that cause the ODK Manage service to download new tasks from the server and/or attempt to execute the tasks it has already downloaded.

If one of the following triggers occurs:

  * Timer: It's been longer than org.odk.manage.android.Constants.TASK\_REQUEST\_PERIOD\_MS since the phone last checked in.
  * New Tasks Notification SMS: A new tasks notification SMS is sent from the server (i.e. an SMS from the server starting with ODK-MANAGE-NT).
  * Manual Sync: The user opens the ODK Manage application and presses Sync.

then ODK Manage will attempt to download the new tasks list. If there is no connectivity, ODK Manage will wait until connectivity is available and then download the new tasks list.

When ODK Manage downloads new tasks, it immediately tries to execute them. If it cannot execute them (e.g. no internet connection, user cancels install, etc.), Manage will try again intermittently or when connectivity changes. If a task fails three times, it's status will be changed to FAILED, and it will not be attempted any additional times.

# Advanced Features #

## Using SMS with ODK Manage ##

To use SMS with ODK Manage, the Manage server needs a way to send and receive SMS. There are several possibilities for SMS-enabling the Manage server.

  1. Use an SMS web gateway like Clickatell.
  1. Hack together your own SMS gateway using a tool like FrontlineSMS or WinSMS.
  1. Use Google's internal appengine SMS API. Since this API is not public, only Google projects (or Google-supported projects) can use this option.

You will need to edit the ODK Manage server source code to connect to SMS. Here is how:
  1. Look in org.odk.manage.server.sms for an SmsService implementation that supports your gateway (e.g. ClickatellSmsService). If one exists, you can configure it and then skip to the last step
  1. If no SmsService exists, you will need to create one yourself. Make a new class in org.odk.manage.server.sms that implements SmsService. You may want to use ExampleSmsService.java as a guide for how to implement your service.
  1. In most cases, you will probably want to send an SMS by making an HTTP request, and receive an SMS by receiving an HTTP request. Therefore, (a) your sendSms method will probably use the appengine URL fetch API, and (b) you will probably need to create an additional servlet to receive SMS (remember to register its URL in web.xml) and pass it on to your SmsService which will then forward it to listeners. This is all described in ExampleSmsService.java. Open org.odk.manage.server.sms.SmsServiceFactory and set the service variable to the SmsService you want to use.
  1. Once you have the server up and running, you will need to go change the settings on the clients to Use SMS and set the server SMS number. You can either change the default in the client source code, or change the setting manually on the phones.

## Adding new Tasks or Actions ##

<font color='red'>This is prototype code.  The steps outlined below will likely be eliminated through configuration-driven dynamic loading of extensions into the service and client without requiring recompilation. </font>

### Adding a new task type on the server ###

  1. Modify org.odk.manage.server.model.Task by adding the new task type to the TaskType enum.
  1. Modify org.odk.manage.server.servlet.ManageAdminServlet to provide options/inputs for new actions (look for 'add new actions here' comments for template code). **Note:** Tasks can have 3 string properties: name, url, and extras. These fields are set by looking at the request parameters YOUR\_ACTION\_TYPE.name, YOUR\_ACTION\_TYPE.url, and YOUR\_ACTION\_TYPE.extras, respectively. Therefore, if you want the user to set the task url, for example, create an HTML field in the action inputs div like the following: 

&lt;input type='text' name='YOUR\_ACTION\_TYPE.url' /&gt;

.

### Adding a new task type on the client ###

  1. Modify org.odk.manage.android.model.Task by adding the new task type to the TaskType enum.
  1. Modify org.odk.manage.android.OdkManageService#attemptTask (look for 'add new task type' for template code). You will need to write a function to carry out your new action.

### Adding a non-task action on the server ###

You may want to add an action that is not a task (e.g. send an SMS message, add a comment, etc.) Here are the steps to do so:

  1. Modify org.odk.manage.server.servlet.ManageAdminServlet to provide options/inputs for new actions (look for 'add new actions here' comments for template code)
  1. Modify org.odk.manage.server.servlet.DoActionServlet#handleNonTaskAction to handle the action (look for 'add new actions here' comments for template code)




