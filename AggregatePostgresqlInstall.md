See [Installation Directions](http://opendatakit.org/use/aggregate/).  The bulk of this content has been moved there.

After the application is installed:

  1. log in with your Google account.
  1. Go to Site Admin / Permissions
  1. Add additional Google accounts, if desired.
  1. Add ODK usernames.
  1. Grant those usernames permissions by checking the corresponding checkboxes for "Data Collector", "Data Viewer", "Form Manager" or "Site Administrator".
  1. Click "Save Changes"
  1. For each username, click "Change Password".  Set the password for that username.

Now, you should be able to log out and choose to sign in with an Aggregate password.  Specify one of the usernames that you granted either a "Data Viewer", "Form Manager" or "Site Administrator" right.

If you are not allowed to have any users sign in using Google's OpenID authentication, you can do a [custom configuration](http://code.google.com/p/opendatakit/wiki/AggregateDeploymentConfiguration) and define only an ODK Aggregate username as a superuser.  This requires computer skills.