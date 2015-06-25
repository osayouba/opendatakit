# ODK Management and Authentication Services #

To hit an early deadline, I'm assuming you can help with the authorization service and an authentication tie-in to LDAP; these would be servlets separate from Aggregate, so they should be easy enough for you to develop independently.

  * Assume a MySQL implementation for those two services (coded however you like).  I can fold it into our persistence structure later.
  * be sure to URLDecode all query parameters, as they may contain special characters.

# ODK Management Service #

The minimal Management functionality:

1) implement a new /formList servlet that returns XML identical to Aggregate's /formList servlet with authentication required on this request.  The new servlet will fetch the information directly from a FORMS table that it maintains (the url and title are in that table -- see below).  The change in functionality is that it will only return the list of forms that the authenticated user has the right to download or submit.

2) implement an /authorize?odkId=KKKK&userId=NNN&realm=RRRR&groups=G1,G2,G3&right=XXX servlet that returns success (200) if the user (NNN) in realm (RRRR) with membership
in groups (G1, G2, G3) is authorized to exercise the right (XXX) on the form (KKK).  Otherwise return access denied.  This request does not require authentication.

When the system is deployed, access to this servlet should be restricted to just the set of Aggregate instances that are using this authorization service.

The Manage database structures should be, at a minimum:

```
create table RIGHTS (
  PK_RIGHT   VARCHAR(80) NOT NULL,  /* requested in APIs as the access right (right) XXX */
  NAME          VARCHAR(40) NOT NULL,  /* displayed in managment displays (potentially localized) */
  DESCRIPTION VARCHAR(255) NULL );  /* verbose explanation */

create table FORMS (
  PK_FORM          VARCHAR(80) NOT NULL,     /* this field is the form identity (odkId) KKKK */
  FORM_TITLE     VARCHAR(255) NOT NULL,    /* the text name returned in formList calls */
  FORM_URL        VARCHAR(255) NOT NULL );  /* the url to be returned in formList calls */

create table FORM_USER_RIGHTS(
   PK_FORM_RIGHTS  VARCHAR(80) NOT NULL, /* unique key formed as: "uuid:" + UUID.toString() */
   FK_RIGHT  VARCHAR(80) NOT NULL,  /* FK to RIGHTS table */
   FK_FORM   VARCHAR(80) NOT NULL, /* FK to FORMS table */
   FK_USER VARCHAR(80) NOT NULL );  /* FK of USER_ID in authentication service (strings of the form: "mailto:xxxx@yyy") */

create table FORM_GROUP_RIGHTS(
   PK_FORM_RIGHTS  VARCHAR(80) NOT NULL, /* unique key formed as: "uuid:" + UUID.toString() */
   FK_RIGHT  VARCHAR(80) NOT NULL,  /* FK to RIGHTS table */
   FK_FORM   VARCHAR(80) NOT NULL, /* FK to FORMS table */
   FK_GROUP VARCHAR(80) NOT NULL );  /* FK of GROUP_ID in authentication service (strings of the form: "opendatakit.org:zzzzz") */
```

plus any management web pages you want to manage the authorizations.

The RIGHTS should be:

  * download  -- right to download a form (form displayed on /formList)
  * submit -- right to upload a completed form (form displayed on /formList)
  * retrieve -- right to retrieve completed form data

The granting of rights is initially DENY unless explicitly ALLOW.  So a right is granted if the FK\_USER has the right granted to them individually in the FORM\_USER\_RIGHTS table or if any of the FK\_GROUP to which the user belongs has the right granted to it in the FORM\_GROUP\_RIGHTS table.

The FK\_USER is a VARCHAR(80) of the form "[mailto:uuu@ddd](mailto:uuu@ddd)" (i.e., an e-mail address).

The FK\_GROUP is a VARCHAR(80) of the form "domainname:group" (i.e., whatever your domain name is + colon + group name).  See the Authenticate service, below.

# Authenticate service #

This is a very thin wrapper over LDAP or another authentication service.  The bare minimum api is:

1) /realm servlet that returns XML:
```
<realms>
  <realm>
    <name>realm-name</name>
    <mailto-domain>organization</mailto-domain>
    <root-domain>registeredName</root-domain>
    <domain>url1</domain>
    ...
    <domain>urln</domain>
  </realm>
</realms>
```
For now, only one realm is returned.

The name and other values should be configurable.  The values are as follows:
  * realm-name -- passed to client to identify who is requesting authentication
  * organization -- used for constructing userId e-mail addresses ([mailto:user@organization](mailto:user@organization))
  * root-domain -- the registered domain name for the organization.
  * url1..n -- passed to client for identifying which URL prefixes recognize this login.

Should always return 200.

2) /userInfo?userId=uuuuu servlet that returns XML:
```
<userIdentity>
  <userId>uuuuu</userId>
  <realm>rrrr</realm>
  <group>g1</group>
  ...
  <group>gn</group>
</userIdentity>
```
This does no authentication, but simply accesses the user and group information from the underlying data store (e.g., LDAP).  Returns 200 on success, 404 (not found) if user does not exist.

The user id (uuuuu) is a string of the form of "[mailto:user@organization](mailto:user@organization)" -- i.e., a fully qualified email address.  The implementation for LDAP would need to parse the userId, verify the organization matches what it can handle, and convert it to whatever underlying authentication domains it has.

The returned group ids (g1..gn) are of the form "root-domain:internalgroup"  where "root-domain" is the registered domain name for the organization (see the return values for the first servlet (1) above)).  The internalgroup portion can be anything.

3) an /authCheck?userId=uuuuu&realm=rrrr&postfix=pppp&md5=fffff servlet that returns the same XML as (2) above except that the xml is returned (200) if and only if:
```
 MD5( concat( MD5( concat( "uuuuu", ":", "rrrr", ":", password ) ),
              ":" , "pppp" ) )
```
is equal to the md5 argument ("fffff") on the request.  Note that "rrrr" should be the same as the realm name returned by the first servlet (1) above.

Otherwise a (404) not found is returned.

Note that this is the same return structure as in (2), but it performs a password verification before returning the data.