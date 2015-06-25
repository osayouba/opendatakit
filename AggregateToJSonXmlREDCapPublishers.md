# Introduction #

ODK Aggregate is intended to act as a locus for aggregation of data from devices running ODK Collect.

Once collected, data can be published to 3rd party servers.  There are 5 publishers available:

  1. Google Spreadsheet
  1. Google Fusion Tables
  1. REDCap (Xml publishing)
  1. Simple JSON publisher
  1. Ohmage JSON publisher (with media attachments)

<font color='red'>The last 3 publishers are in ALPHA.</font>

# Known Issues #

  * Google Fusion Tables -- repeat groups are published to their own Fusion Tables, and users must create their own VIEW to join these repeat tables against the top-level fusion table that is accessible through the ODK Aggregate UI.
  * REDCap publisher fails when publishing media attachments.
  * REDCap publisher has naming conventions for fields which may change.
  * Ohmage JSON publisher has no verified accepting servers.

# Details #

Below are details of the submission protocols used for the REDCap, Simple JSON and Ohmage JSON publishers.

## REDCap (Xml) publisher ##

Each submission is published into REDCap using a separate http POST request, and each attachment is also published as a separate http POST request.

### Submission POST request ###

NOTE: The URL to which the REDCap data is POSTed must end in a slash.

URL-encoded UTF-8 form body with the following key-value pairs:

  * "token" : the api token specified when publisher is created
  * "content" : "record"
  * "format" : "xml"
  * "overwriteBehavior" : "overwrite"
  * "data" : submission-data (see below)
  * "returnContent" : "ids"
  * "returnFormat" : "xml"

The submission-data is structured as:

```
  <?xml version=\"1.0\" encoding=\"UTF-8\" ?>
  <records>
     <item><study_id>primaryKey</study_id>
       ...       
     </item>
  </records>
```

**Any repeat groups in the form are ignored.**

**The group structure of a form is entirely ignored.** So if you have a form like:
```
<form id="myform">
  <provider></name></provider>
  <patient></name></patient>
  ...
</form>
```
it will not be able to be published because the constructed XML will contain two `<name>` fields. You would need to redefine these as `<provider_name>` and `<patient_name>`.

Multiple-select columns are expanded out as separate columns:
```
fieldname___choiceValue : "1" or "0"
```

At the moment, boolean columns are expanded like multiple-choice columns (this will hopefully change).

Geopoint columns are expanded out with 'gps\_lat_', 'gps\_lon_', 'gps\_alt_', 'gps\_acc_' prefixed to the broken-out values.

The unique id for a data row is assumed to be in a string-valued form field called 'study\_id'.

Various prefix strings are eliminated. If the Geopoint column begins with 'gps_', that is stripped off.  If the multiple-choice or boolean column starts with 'slct-', that is stripped off._

The column renaming and the treatment of boolean columns will hopefully be removed.  The Geopoint prefix of gps_.... will hopefully be changed to adding a suffixes of_lat, _lon,_alt, _acc, and dropping all use of 'gps', as you can get geolocation information from wifi and cellular networks.  The unique id (study\_id) will hopefully be changed to the instanceID field used by the ODK infrastructure. Or, if it must be study\_id for REDCap, the processing should be changed to use the metadata instanceID field for this value, and not have it be a separate field in the form._


### Media attachments ###

<font color='red'>This apparently does not work with REDCap servers.</font> Hopefully someone in the REDCap consortium can raise this issue with the REDCap developers.  It appears that the form-data parser used in REDCap is non-standard and does not recognize semicolon-separated field headers, such as those produced by standard Apache HttpClient Java libraries.

Media attachments are published as multi-part form-data POST to the same URL as the submission data POST, with the following string-valued (text/plain) form elements.

  * "token" : the api token specified when publisher is created
  * "content" : "file"
  * "action" : "import"
  * "record" : study\_id value for corresponding submission data
  * "field" : field name of the field in the form under which this document is attached.

Plus the following binary form element:

  * "file" : a java `ByteArrayBody` of the binary data, the content type, and the filename, which is always the field name plus the file extension of the original file.

## Simple JSON Publisher ##

The simple JSON publisher constructs a JSON representation of the form, preserving all groups and nesting, including nested repeat groups. A

The data is published as POST with a content type of "application/json". The POST has the following string-valued elements:
  * "token" : the auth token specified when publisher is created
  * "content" : "record"
  * "formId" : the unique form id
  * "formVersion" : the form's version
  * "data" : submission-list

The "data" is a normal JSON serialization of an array of submissions following the general conventions specified on [json.org]. Multiple-choice values are represented as an array of values (or null if the array is empty).
The Media attachments are written strings using one of two options:

**Option A)** String URLs that can be used to fetch the binary contents of the attachment.

_"Image": {
  * "url": "https://opendatakit.appspot.com/view/binaryData?blobKey=gme-geo-tagger-b1%5B%40version%3Dnull+and+%40uiVersion%3Dnull%5D%2Fdata%5B%40key%3Duuid%3A57a5c38d-e903-429e-857d-985bb415b057%5D%2FImage",
  * "filename": "1377188872644.jpg",
  * "type": "image/jpeg"
}_

**Option B)** Binary strings.

_"Image": {
  * "bytes": Base64 encoded binary
  * "filename": "1377188872644.jpg",
  * "type": "image/jpeg"
}_

### Notes from a Django integration ###
  * The Django server must be visible to the Aggregate server, so it might not be able to receive the POST on localhost.
  * If the Aggregate server and receiving server are on different IPs, the receiver may think that it is a cross site request forgery and block it automatically. To get around this either use the token option in the publish feature, or allow cross site requests for this one function. In Django, this can be done by adding the @csrf\_exempt decorator before the view.
  * Because the JSON POST is not formatted in the same way as a form, a Django server might not recognize it as a POST request, and the request.POST field will be empty. Try looking in request.body, which contains the raw request received. This is particularly difficult to find because print(request) does not include request.body, and instead appears that no form data is in the request at all.
  * After receiving the POST request, send a 200 status code back to the Aggregate server, or else it will think there was an error and not send any further POSTs.
  * For a working example, see github.com/eschluntz/odk\_receiver

## Ohmage JSON Publisher ##

This publisher was contributed as example code by the Ohmage team, but has not be verified as working with their site http://ohmage.org/

The Ohmage JSON publisher publishes one submission record at a time, together with all the media attachments for that submission.

The data is published as multi-part form-data POST, with the following string-valued (text/plain) form elements.

  * "campaign\_urn" : the URN specified when publisher is created
  * "campaign\_creation\_timestamp" : the creation timestamp specified when publisher is created
  * "user" : the Ohmage username specified when publisher is created
  * "password" : the Ohmage hashed password specified when publisher is created
  * "client" : this ODK Aggregate server 'root' URL (everything before the Aggregate.html in the server url.
  * "survey" : the JSON survey data (see below)

Plus binary form elements for each media attachment:

  * uniqueKey : a java `ByteArrayBody` of the binary data, the content type, and the filename of the original file.

The JSON survey data is the JSON representation of a singleton list of one submission.

The submission has all binary (media) field values replaced with a UUID, which will correspond to the uniqueKey for the binary data for that field.

Otherwise the JSON submission should be much the same as that for the simple JSON publisher. (corrections are welcome).