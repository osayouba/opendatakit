# Introduction #

This is a technical note on the security shortcomings and features in Aggregate 1.0 and Collect 1.1.7.

# User logins are Always Secure #

User logins through browsers or via ODK Collect 1.1.7 **are always secure**. ODK Aggregate 1.0 uses digest authentication for the ODK Aggregate usernames and Google's OpenID for Google accounts.

# Configurations that are Always Secure #

**_Verify_** that your installation **is always secure** by confirming that the main ODK Aggregate web page (Aggregate.html) is accessed over https:

ODK Aggregate 1.0 deployed to Google's AppEngine cloud services **is always secure**.

ODK Aggregate 1.0 deployed with MySQL and/or PostgreSQL **is secure if configured with an SSL certificate** (by answering _Yes_ during the installation wizard).  If you later install an SSL certificate, you must re-run the installation wizard, answer _Yes_, and deploy the reconfigured ODKAggregate.war file in order for ODK Aggregate 1.0 to become secure.  Verify that it is secure by confirming that the main ODK Aggregate webpage is accessed over https:

# Security Shortcomings #

ODK Aggregate 1.0 deployed with MySQL and/or PostgreSQL **is vulnerable when operating without an SSL certificate**.

Without an SSL certificate, a well-placed eavesdropper can observe all data being sent between the ODK Aggregate 1.0 webserver and any browsers or ODK Collect application. That data will be plainly visible.

## Changing Passwords ##

The **vulnerability of greatest concern** when operating without an SSL certificate is during the setting or changing of user passwords.  The browser does this by sending the username and a _hashed password_ to the ODK Aggregate 1.0 webserver.  This makes it difficult for an eavesdropper to reconstruct the user's actual password. However, a competent hacker can still use the _hashed password_ to gain access to your webserver -- they do not need to reconstruct the actual password.

Your risk of exposure to malicious eavesdroppers can be minimized by:
  * never using public WiFi connections when changing passwords.
  * change passwords only over a VPN connection to your webserver if it is located in a hosted webfarm, or
  * change passwords only from within your organization's firewall if the server is managed by your own IT department (ask them).

## Observing Submitted Data ##

A secondary vulnerability when operating without an SSL certificate is that a well-placed eavesdropper could see all data being submitted to or accessed from the ODK Aggregate 1.0 webserver.

## Altering User Access Privileges ##

If you are running multiple servers (as occurs when deployed to Google's AppEngine cloud services), each ODK Aggregate 1.0 instance will check every second to see if there has been a change made to the security configuration and will reload its configuration if a change has been made.  Every 2 minutes, the security configuration is reloaded completely, regardless of whether there was a recorded change or not.

During normal operations, this creates a one-second window (two-minutes in abnormal operations) during which configuration changes might not have propagated across your cluster.

The server APIs that alter user access privileges are protected from cross-site request forgery by requiring the session cookie be passed as an argument to the request.

# Data Transmission Security Design #

The design of the communications between ODK Collect and ODK Aggregate ensures that, at most, the name of a form might be transmitted in the clear before the communications channel becomes secured (converted from an http: to an https: channel).

This is accomplished during data submission by first issuing a HEAD request to the server -- that request contains no data -- which allows the server to negotiate a secure (https) channel and, optionally, to authenticate the user.  ODK Collect then verifies that the negotiated channel is still directed to the same host as before, and, if so, then sends the data to the server.

# Data Storage Security Design #

Data stored in Google's AppEngine is not encrypted by Aggregate before storage.  This means that it is freely visible to anyone with access to the AppEngine datastore and it may be in plain text if Google does not itself apply an encryption scheme to it.  Parties with access to this data are, of course, governed by Google's internal personnel access-management systems.

Similarly, data stored in MySQL or PostgreSQL databases will be stored unencrypted.  In this case, you are responsible for securing the hardware, user, and network access to that database machine.