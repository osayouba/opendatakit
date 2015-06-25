# Introduction #

This page is for experienced software developers and builders. As always, refer to the actual files (located under src/main/resources) for their current contents.

Aggregate 1.0 uses properties files to define its database connectivity and application security settings.

In the source tree, the relevant files are:

src/main/resources/common/security.properties

src/main/resources/gae/jdbc.properties

src/main/resources/mysql/jdbc.properties

src/main/resources/postgres/jdbc.properties

The corresponding -unit directories contain equivalent configuration for use during integration testing.

Perhaps the easiest way to modify these is to first install the app using the installer, then navigate to the `WEB-INF/lib/` directory and explode the `ODKAggregate-settings.jar`.  Make modifications, then reconstruct the jar.

Otherwise, these are easily built using maven or you can copy and modify the Ant `build.xml` script under `odk-gae-settings` that is used during Eclipse development. That script builds the jar and copies it into the `eclipse-aggregate-gae` war tree as `odk-gae-settings-latest.jar`.

To replace the jar in the war file, after running the scripts to build the jar files, you would create the directory path `WEB-INF/lib` in the scratch directory containing the war (here referred to as '`myDistro.war`'), copy the above `odk-gae-settings.jar` into that `WEB-INF/lib` directory, renaming it to `ODKAggregate-settings.jar`, and issue the command:

`jar -uf myDistro.war WEB-INF/lib/ODKAggregate-settings.jar`

This will replace the jar in the war with the one you specify.  You can list the contents of the war with:

`jar -tf myDistro.war`

# JDBC Properties #

The JDBC properties are configured in the relevant jdbc.properties file.  There are separate files for Google AppEngine, MySQL and Postgres.  These are incorporated into separate jar files for each
of these systems, so you only need to modify the file you're using.

For MySQL, the settings recognized (and their default values) are:

```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.resourceName=jdbc/odk_aggregate
jdbc.url=jdbc:mysql://127.0.0.1/odk_test?autoDeserialize=true
jdbc.username=odk_test
jdbc.password=odk_test
jdbc.schema=odk_test
```

This configures ODK Aggregate to talk to a MySQL database located on the same PC (localhost) named odk\_test with a username odk\_test with the password odk\_test.  Similar configuration is available for postgres.

If you need to direct ODK Aggregate to point to a MySQL database located on a different host, or change the passwords or database name, you can do that by changing the values in this file.

# Security Properties #

The security properties are configured in the security.properties file.  The settings available (and their default values) are:

```
# unique keys for this site -- should be an unguessable string
security.server.siteKey=371adf05-3cea-4e11-b56c-3b3a1ec25761
security.server.userServiceKey=371ade03-3dea-4e11-b56c-3b3b1ec25761
# either basic or digest
# basic auth does not support changing passwords through the UI
security.server.deviceAuthentication=digest
# choose whether to secure everything with https or allow http access.
#
# NOTE: changes also needed to:
# -- server.xml (Tomcat configuration file) to set up SSL
#
# should be REQUIRES_SECURE_CHANNEL but can't 
# (and will be ANY_CHANNEL) if SSL is not available.
security.server.secureChannelType=REQUIRES_SECURE_CHANNEL
# either ANY_CHANNEL to secure nothing
# or REQUIRES_SECURE_CHANNEL to secure everything
security.server.channelType=REQUIRES_SECURE_CHANNEL
#
# When running under Tomcat, you need to set the hostname and port for 
# the server so that the background tasks can generate properly-constructed
# links in their documents and in their publications to the 
# external services.
#
# This is configured during install.  If blank, discovers an IP address
security.server.hostname=opendatakit.appspot.com
#
# any port pairings can be used.
security.server.port=80
security.server.securePort=443

# e-mail of designated superuser. This must be a user that has an OpenID
# login hosted by a remote server (i.e., this must be a gmail account).
# this should be of the form: 'mailto:user@gmail.com'
security.server.superUser=mailto:mitchellsundt@gmail.com
# Define a superUserUsername to insert an ODK Aggregate username that can
# access the server.  The initial password for this username is 'aggregate'
security.server.superUserUsername=

# realm definition
# realmString -- what should be sent to users when BasicAuth or DigestAuth is done
security.server.realm.realmString=opendatakit.org ODK Aggregate
#
#
```

While the website can be configured to require secure channel (https) for all communications, if that is done, you should specify 'https://' as the prefix of the URL when configuring ODK Collect.  This is required when using ODK Collect 1.1.5 or earlier.

The super-user can never be deleted from the system. When you restart the system, it always verifies that the super-user exists and has super-user privileges.  Normally, a Google OpenID e-mail is configured as the super-user. However, you can also manually define an ODK Aggregate username as a super-user.  The user interface may be quirky with this user, as little testing has been done for this feature (it is there as a work-around should Google OpenID fail).  To define an ODK Aggregate username as a super-user, specify the username for the key:
```
security.server.superUserUsername=
```

# Security Implementation #

This section is background information for how security is configured.  Changes to the following sections of configuration should generally be avoided, or, if needed, please consider submitting your revisions for incorporation into the main code branch.

Security is implemented using the Spring Security Framework.  Aside from the configuration screens and database tables used to store the security configuration, all access control and channel control (http/https) decisions are done before any application code is reached.

These decisions are executed based upon the configuration defined in
`war/WEB-INF/applicationContext-security.xml`

## Channel Security ##

The determination of whether or not to require website communications be sent over HTTP or HTTPS is governed by the `channelProcessingFilter` configuration, shown below:

```
	<beans:bean id="channelProcessingFilter" class="org.springframework.security.web.access.channel.ChannelProcessingFilter">
	  <beans:property name="channelDecisionManager" ref="channelDecisionManager"/>
	  <beans:property name="securityMetadataSource">
	    <filter-invocation-definition-source path-type="ant">
	        <intercept-url pattern="/favicon.ico" access="ANY_CHANNEL" />
	        <intercept-url pattern="/odk_color.png" access="ANY_CHANNEL" />
	        <intercept-url pattern="/index.html" access="REQUIRES_INSECURE_CHANNEL" />
	        <intercept-url pattern="/logout.html" access="REQUIRES_INSECURE_CHANNEL" />
	        <intercept-url pattern="/openid_login.html" access="ANY_CHANNEL" />
	        <intercept-url pattern="/res/**" access="ANY_CHANNEL" />
		    <intercept-url pattern="/ssl/**" access="${security.server.secureChannelType}"/>
            <intercept-url pattern="/local_login.html" access="${security.server.secureChannelType}" />
		    <intercept-url pattern="/**" access="${security.server.channelType}"/>
	    </filter-invocation-definition-source>                
	  </beans:property>
	</beans:bean>
```

You can make exceptions or require secure or insecure channels be used by adding `<intercept-url/>` definitions to this configuration section.

## Authentication ##

Authentication is done via `OpenID` or `BasicAuth` or `DigestAuth`.  The decision of which to use is partially automatic and partially a manual decision on the part of the end user.  The automatic decision tree is governed by the `exceptionTranslationFilter` configuration, shown below:

```
	<beans:bean id="exceptionTranslationFilter"
		class="org.springframework.security.web.access.ExceptionTranslationFilter">
		<beans:property name="authenticationEntryPoint">
			<beans:bean
				class="org.springframework.security.web.authentication.DelegatingAuthenticationEntryPoint">
				<beans:constructor-arg>
					<beans:map>
                        <beans:entry key="hasHeader('X-OpenRosa-Version','1.0')" value-ref="${security.server.deviceAuthentication}EntryPoint" />
					</beans:map>
				</beans:constructor-arg>
				<beans:property name="defaultEntryPoint" ref="openIdEntryPoint" />
			</beans:bean>
		</beans:property>
	</beans:bean>
```

Here we see that if the request has an `X-OpenRosa-Version` header with a value of `1.0`, then the request will use either the `basicEntryPoint` (BasicAuth) or `digestEntryPoint` (DigestAuth) for authentication.  This header value is required for OpenRosa 1.0-complaint requests from the device (ODK Collect) to the server.

Otherwise, if that header is not present or does not have the value `1.0`, the request will use the `openIdEntryPoint` which will direct the user to the web page `war\openid_login.html` where the user can choose to click to log in using gmail or log in using the locally-held device credentials (if they have them).

If you wish to use a different OpenID provider, you should change this web page to provide a button to that alternate provider.  Note that the provider is required by Aggregate 1.0 to return the user's e-mail via the `http://axschema.org/contact/email` attribute.  If your provider uses a different attribute name, you will need to modify the `openIdFilter` configuration to specify the alternative attribute name.

## Authorization ##

Authorization decisions are determined with boolean expressions.  The specific requirements for certain web pages are defined in the `securityPolicy` configuration setting:

```
	<filter-security-metadata-source id="securityPolicy" use-expressions="true">
		<intercept-url pattern="/favicon.ico" access="true" />
		<intercept-url pattern="/odk_color.png" access="true" />
		<intercept-url pattern="/index.html" access="true" />
		<intercept-url pattern="/logout.html" access="true" />
		<intercept-url pattern="/res/**" access="true" />
		<intercept-url pattern="/openid_login.html" access="isAnonymous()" />
		<intercept-url pattern="/local_login.html" access="isFullyAuthenticated() and hasRole('AUTH_LOCAL')" />
		<intercept-url pattern="/UploadSubmissionsApplet" access="isFullyAuthenticated() and hasRole('ROLE_SUBMISSION_UPLOAD')" />
		<intercept-url pattern="/upload-submissions/**" access="isFullyAuthenticated() and hasRole('ROLE_SUBMISSION_UPLOAD')" />
		<intercept-url pattern="/UploadXFormApplet" access="isFullyAuthenticated() and hasRole('ROLE_FORM_ADMIN')" />
		<intercept-url pattern="/upload-xform/**" access="isFullyAuthenticated() and hasRole('ROLE_FORM_ADMIN')" />
		<intercept-url pattern="/Briefcase" access="isFullyAuthenticated() and hasRole('ROLE_ANALYST')" />
		<intercept-url pattern="/briefcase/**" access="isFullyAuthenticated() and hasRole('ROLE_ANALYST')" />
		<intercept-url pattern="/xformsDownload" access="isFullyAuthenticated() and hasRole('ROLE_FORM_DOWNLOAD')" />
		<intercept-url pattern="/xformsList" access="isFullyAuthenticated() and hasRole('ROLE_FORM_LIST')" />
		<intercept-url pattern="/xformsManifest" access="isFullyAuthenticated() and hasRole('ROLE_FORM_LIST')" />
		<intercept-url pattern="/formList" access="hasRole('ROLE_FORM_LIST')" />
		<intercept-url pattern="/formXml" access="hasRole('ROLE_FORM_DOWNLOAD')" />
		<intercept-url pattern="/submission" access="hasRole('ROLE_SUBMISSION_UPLOAD')" />
		<intercept-url pattern="/www/**" access="isFullyAuthenticated() and hasRole('ROLE_USER')" />
        <intercept-url pattern="/ssl/user-manage-passwords" access="isFullyAuthenticated() and hasRole('USER_IS_REGISTERED') and hasRole('ROLE_ACCESS_ADMIN')" />
		<intercept-url pattern="/ssl/**" access="isFullyAuthenticated() and hasRole('USER_IS_REGISTERED') and hasRole('ROLE_USER')" />
		<intercept-url pattern="/view/csvFragment" access="hasRole('ROLE_ANALYST')" />
		<intercept-url pattern="/view/binaryData" access="hasRole('ROLE_ATTACHMENT_VIEWER')" />
		<intercept-url pattern="/view/**" access="isFullyAuthenticated() and hasRole('ROLE_ANALYST')" />
<!--	TODO: gae should be: hasRole(USER_IS_DAEMON) -->
		<intercept-url pattern="/gae/**" access="true" /> 
		<intercept-url pattern="/extern/**" access="isFullyAuthenticated() and hasRole('ROLE_SERVICES_ADMIN')" />
		<intercept-url pattern="/admin/upload" access="hasRole('ROLE_FORM_ADMIN')" />
		<intercept-url pattern="/admin/**" access="isFullyAuthenticated() and hasRole('ROLE_FORM_ADMIN')" />
		<intercept-url pattern="/access/**" access="isFullyAuthenticated() and hasRole('ROLE_ACCESS_ADMIN')" />
		<intercept-url pattern="/**" access="isAnonymous()" />
	</filter-security-metadata-source>
```

Refer to the Spring Security documentation for the meanings of these terms.  The `hasRole()` predicate enforces that the given authenticated party (who may be anonymous) has the given role.  The `isFullyAuthenticated()` predicate requires the authenticated party to not be anonymous (e.g., to have a successfully logged in via gmail or the locally-held credentials).

Note that anonymous users can have roles via the assignment of those roles to the `USER_IS_ANONYMOUS` group through the Aggregate UI.

The `/view/csvFragment` and `/admin/upload` pages allow anonymous logins so that the Applets can be debugged against a test server.

The `/submission` page allows anonymous logins so that older unauthenticated ODK Collect instances can submit to the server and so that the Applet can be debugged against a test server.