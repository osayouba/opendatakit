# Migration from ODK Aggregate 0.9x to 1.x #

The latest Aggregate 0.9.x release is 0.9.8.1 as of this writing.

The latest Aggregate 1.x release is 1.0.4 as of this writing.

The latest Briefcase release is 1.0.1 as of this writing.

This details the steps needed to migrate from 0.9.x to 1.x

# Preliminaries #

First, be aware that Aggregate 1.x has stricter requirements on the form definition than 0.9.x, so you will likely need to edit your form definitions before they can be successfully uploaded into Aggregate 1.x.

Secondly, migration will necessitate creating a new AppEngine application id and migrating your ODK Collect devices to use that new AppEngine URL.  This allows you to keep the old Aggregate 0.9.x instance should you need to return to it (e.g., if there is an error discovered after data migration), and it gives you time to validate the accuracy of the data that was migrated. It is simply safer to do the migration this way rather than overwrite your older instance.

# General Steps #

Since Google is now charging for AppEngine usage, the lowest-cost sequence for the AppEngine migration is as follows:

  1. Upgrade ODK Aggregate 0.9.x to the latest 0.9.x release.
  1. Create an new application id for the 1.x instance.
  1. Download the latest installer for ODK Aggregate 1.x, run it, and deploy to the new application id.
  1. Manually download the form definitions (as XML) from Aggregate 0.9.x
  1. Manually upload the form definitions to Aggregate 1.x, editing and fixing the form definitions as needed.
  1. Change all your ODK Collect clients to use the new Aggregate 1.x instance.
  1. Download the latest ODK Briefcase application
  1. Run ODK Briefcase, choosing to pull all forms from your 0.9.x instance to the local server.  You will need enough space to store these files.
  1. For any form definitions you have changed, manually overwrite the downloaded form definition within the Briefcase directory with the one updated for Aggregate 1.x.
  1. Run ODK Briefcase, choosing to push all forms from your local server to the new Aggregate 1.x instance.
  1. Validate that your Aggregate 1.x instance has all the data you expect, and the values are complete and correct (by, e.g., publishing to Fusion Tables and comparing this dataset to the corresponding dataset produced by 0.9.x).
  1. Shut down the Aggregate 0.9.x instance.

If it is not feasible to change your ODK Collect clients to use the new instance all at once, you can run Briefcase a second time after all ODK Collect clients have been updated to use the new Aggregate 1.x server.  This does, however, cause a full scan of all data on your older instance, and that can become rather expensive.  See the section on running with two instances, below, for further details.

If you do make a second run, you must be sure to overwrite the form definitions downloaded from Aggregate 0.9.x with the revised versions used on Aggregate 1.x before uploading.

# Upgrade ODK Aggregate 0.9.x #

Get the latest Aggregate release from the ODK Downloads page [here](http://code.google.com/p/opendatakit/downloads/list).

If you have any 0.9.x release, you can upgrade to the latest 0.9.x release without any changes (the newer updates are backward compatible).

# Create an new application id #

This can be done from the Google AppEngine dashboard [here](https://appengine.google.com/)

# Install Aggregate 1.x #

Download the latest installer for ODK Aggregate 1.x from the ODK Downloads page [here](http://code.google.com/p/opendatakit/downloads/list).  Choose the installer for your system (Windows, OSX, Linux (32-bit), or Linux-x64 (64-bit)).

The installer requires that Java be installed on your system.  You can download Java from [here](http://www.java.com/en/download/index.jsp).

The installer presents a graphical user interface (a wizard) that guides you through the configuration steps.  It does not alter any registry settings, access the internet, or modify your system in any way.  It just produces a set of files under the installation directory you specify.

On the final screen, if you choose to launch the script to deploy to AppEngine, it then launches that script, which prompts you for an AppEngine user e-mail and password and commences the upload process.

Once again, <font color='red'><b>Install Aggregate 1.x to a different AppEngine application id than the one used for your Aggregate 0.9.x instance.</b></font>

# Manually Download Form Definitions #

Browse to your Aggregate 0.9.x instance and choose the "View XML" button on the main forms page.  Then, from the resulting page, choose the "Download XML" button.  Repeat for every form you have on the system.

You may wish to specify a new folder for the downloads of your forms so that you can easily locate and edit them.

# Manually Upload Form Definitions #

Browse to your Aggregate 1.x instance.

Choose to "Sign in with Google" and specify the e-mail address you gave to the installer (Super-user E-mail Address).

Go to the `Form Management/Forms List` sub-tab.

Click the "Add New Form" button.

Select the form to upload, and click on "Upload Form".

If the form uploaded without complaint, great, you are done.  Repeat with the remaining forms.

Otherwise, the two issues that are likely to have tripped you up are:

  1. Your form definition may be using `xmlns="myformid"` to define the `Identifier` (Aggregate 0.9.x) now referred to as the `Form Id` (Aggregate 1.x) of the form.  In Aggregate 1.x, we enforce that all `xmlns` assignments conform to URL syntax. This is a w3c standard and a requirement of some Xml parsers and editing systems.  Since most users have just a simple short alphanumeric id for their forms, the fix is to change from using `xmlns` to using `id` to define your "form id".  E.g., use `id="myformid"` instead. This variant allows "myformid" to be any alphanumeric string with additional non-space characters (e.g., dashes and underscores).
  1. Aggregate 1.0.1 warns you if you have fields in the form that do not have defined data types.  I.e., they lack any `<bind>` expressions or their `<bind>` expression does not specify a `type="datatype"` expression, with `datatype` being one of the known data types.  This is a concern because Aggregate can filter results based upon the values of individual columns.  The ranking order for the string "111" would place it before (less than) "13", but that would be incorrect if this field were actually an integer value.  The warnings are to catch omissions like this so that filters can work properly and so that data can be properly published to Fusion Tables.

Modify your form definition until it is uploaded and has as few warnings as you desire.

Additionally, if you want a string field to hold more than 255 characters, modify your form as specified [here](http://code.google.com/p/opendatakit/wiki/XFormDesignGuidelines#Datastore_String_Length).  Note that space is reserved for values up to the length you specify, so choosing shorter lengths makes data storage more efficient (this applies equally for AppEngine, MySQL, and PostgreSQL).  For forms with many questions, specifying lengths shorter than 255 (e.g., for select1 columns where the values are known) can dramatically improve the structure of the generated table(s).

# Change ODK Collect Clients #

Up until this step is complete, clients will be submitting form data to Aggregate 0.9.x.  This step will update all those clients to use the server URL (and optional authentication) of the new Aggregate 1.x instance.

Until this is done, you will have new values trickling into the old Aggregate 0.9.x instance.

ODK Briefcase does not efficiently handle incremental pulls of changed data from an Aggregate 0.9.x instance.  To minimize datastore access charges, this means that migrating data from your old 0.9.x instance to your new instance should be done after all the clients are referencing the new instance.

The key changes are to:
  * configure the Aggregate 1.x instance to accept submissions from ODK Collect and
  * revise each ODK Collect server URL to point to the new Aggregate 1.x instance.
  * revise each ODK Collect with a username and password if Aggregate 1.x requires authenticated submissions.

## Configure Aggregate 1.x Access Rights ##

If you are running ODK Collect 1.1.5, you will need to configure Aggregate 1.x to grant the `anonymousUser` the `Data Collector` privilege.  This allows unauthenticated users to submit data to Aggregate 1.x.

If you are running ODK Collect 1.1.7 or higher, you can choose to define one or more local usernames and passwords, and configure ODK Collect 1.1.7 to identify itself with one of those usernames and passwords when accessing the server.

To do either of these steps:

Go to the `Site Admin/Permissions` sub-tab.

If you are creating new local user(s), type the username(s) in the text box in the `Add Users` section of the page and click `Add`.  You can add multiple users by separating the usernames with spaces or commas.

The table above will update with the added users.

Ensure that the `Account Type` is `ODK` for each of these users.

Check the `Data Collector` check box beside each username that will be used by ODK Collect.  If you have ODK Collect 1.1.5, check the check box beside the `anonymousUser`

Click `Save Changes`  Your changes will not be applied until you do this.

Then, for each username you created, you must click `Change Password` and set the password for that username.

## Configure ODK Collect ##

On both ODK Collect 1.1.5 and 1.1.7, ensure that the Server URL points to the new instance and begins with `https` (for AppEngine instances).  For MySQL or PostgreSQL systems, specify `https` only if the server has an SSL certificate.

For ODK Collect 1.1.7, if you are requiring authentication, enter one of the configured usernames and passwords into the phone.

<font color='red'><b>NOTE:</b></font> If you are configuring your phones in anticipation of rolling out secure access after all your ODK Collect 1.1.5 instances have been upgraded to 1.1.7, <font color='red'><b>it is not possible to verify proper configuration of the username and password until after the <code>Data Collector</code> privilege is revoked from the <code>anonymousUser</code>.</b></font>

# Download ODK Briefcase #

Get the latest Briefcase application from the ODK Downloads page [here](http://code.google.com/p/opendatakit/downloads/list).

While there is a Briefcase applet on Aggregate 0.9.x, that applet only downloads form data into csv files.

The new Briefcase application downloads form data in the same format as it initially existed on ODK Collect -- as it was initially submitted to Aggregate (with a few extra metadata fields specified).

This means you can also send these downloaded files to other ODK tools (e.g., KoBo) for processing should they prove more effective for your needs.  Note, however, that the namespace of the form will be different from that of the filled-in forms from ODK Collect, so this migration to other tools may require custom XSLT steps.

# Use Briefcase to Download from 0.9.x #

The Briefcase application requires that Java be installed on your system.  You can download Java from [here](http://www.java.com/en/download/index.jsp).

<font color='red'><b>Ensure that there is enough space to store your form data.</b></font> Briefcase will pull data to the `ODK Briefcase Storage Area` you specify.  Please ensure that you have sufficient space on that disk volume to hold all the data on your instance. To gauge the required space, go to the AppEngine dashboard for your 0.9.x instance.  The space used by your 0.9.x instance is shown on the `Billing Status` portion of that page. Either ensure that you have double this amount of free disk space on your system before continuing or consider transferring forms one-by-one.

Run Briefcase by double-clicking its file.

Go to the `Pull` tab.

Choose to get data from `Aggregate 0.9.x`.

Choose `Connect...`

Fill in the information requested and hit `Connect`.

The forms available on that `Aggregate 0.9.x` instance will be populated into the `Forms to Pull` box.

Choose the forms you wish to pull down to your local machine.

Choose `Pull`

"Downloading..." progress text appears to the left of the `Pull` button and `Pull Status` will show `SUCCESS` or `FAILURE` for each form. You can click the `Details...` button for that form to see specifics. Each form is downloaded in turn.

If the form transfer was not successful, the `Details...` information can be helpful to diagnose the problem.

# Overwrite Downloaded Form Definitions with Updates #

If you had to modify any form definitions for Aggregate 1.x, you need to first overwrite the form definitions that Briefcase downloaded with these revised versions prior to uploading to Aggregate 1.x.

The `ODK Briefcase Storage Area` has a `/forms` directory under it, and, in that, it has directories for each form downloaded from Aggregate 0.9.x.  The form definition is within that directory and must have the same name (with a file extension of `.xml`) as the directory containing it.

Overwrite all modified form definitions now.

# Use Briefcase to Upload to 1.x #

Run ODK Briefcase by double-clicking it.

Go to the `Push` tab.

Choose to send data to `Aggregate 1.0`.

Choose `Connect...`

Specify the URL of your Aggregate 1.x instance.  For AppEngine instances, this should begin with `https:` (ODK Aggregate 1.x uses a secure communications channel whenever one is available, and it is for AppEngine).

Specify a local username and password that you have defined on the `Site Admin/Permissions` sub-tab of your Aggregate 1.x instance.  This username should have `Form Management` permissions to the instance.

After filling in the requested information, hit `Connect`.

Choose the forms to upload to this Aggregate 1.x instance.

Choose `Push` to begin the upload process.

"Uploading..." progress text appears to the left of the `Push` button and `Push Status` will show `SUCCESS` or `FAILURE` for each form. You can click the `Details...` button for that form to see specifics. Each form is pushed, in turn, to the ODK Aggregate v1.0 instance.

Once complete, you can look at these progress traces by clicking the `Details...` button to the right of the form's name.  This can be helpful if there was a processing error.

# Validate Transferred Data #

The transferred data will preserve the submission datetime of the original submission into the Aggregate 0.9.x server.  You can use that datetime to verify the agreement of random submissions across the old and new Aggregate instances.

# Running with two Active Instances #

If you have to do this migration mid-survey, and need to be able to incrementally pull data from ODK Aggregate v0.9.x, you should follow these steps:

  1. Pull data from ODK Aggregate v0.9.x
  1. Push data to ODK Aggregate v1.x
  1. Pull data from ODK Aggregate v1.x
  1. Repeat periodically.

The extra pull of data down from ODK Aggregate v1.x sets a flag within ODK Briefcase that identifies the record as being "complete" (having all of its attachments).  This does not prevent ODK Briefcase from downloading all the data each time from ODK Aggregate v0.9.x, but it does enable it to avoid repeatedly uploading this data to ODK Aggregate v1.x.

# Shut Down Old Instance #

Once you are comfortable with the operations of the Aggregate 1.x instance you should go to the Aggregate 0.9.x dashboard and delete that application id.

Click on the `Application Settings` link on the left sidebar under the `Administration` heading.

Click on the `Disable Application..` button on this page.

Choose to disable it, and then, when prompted, choose to delete the application.

This is more cost-effective than deleting the forms (and data) from the old instance and trying to reuse it -- that would incur usage charges.  Deleting the entire instance accomplishes that without running up any charges.