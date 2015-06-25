# Introduction #
Google Maps Engine makes it easy for you to create beautiful maps, share them with others, and reach your audience no matter where they are. It's built on the same platform that provides Google services to millions of people worldwide, so your users have a consistent and familiar experience wherever they are.

Google Maps Engine (GME) requires a license, so you will need to purchase one before you can stream data from ODK Aggregate. You can purchase a license at [Google Maps Engine](http://www.google.com/enterprise/mapsearth/products/mapsengine.html). If you are an educational or non-profit institution, you can [apply for a GME grant for your Nonprofit](http://www.google.com/earth/outreach/grants/software/mapsengine.html) or [apply for a GME grant for education](http://maps.google.com/help/maps/education/grants.html).

For technical information about Google Maps Engine please visit the [Google Maps Engine developer site](https://developers.google.com/maps-engine).

# Instructions #

To publish data submissions from Aggregate to GME, you must create a table store in GME that matches the form you wish to publish submissions from.

### 1. Build the KML used to create data store ###
The KML will be used by GME to create a data store. The KML must include a Placemark with ExtendedData which will be used to make the data store. If you'd like the final map to zoom into a particular location (instead of 0,0,0), include a Polygon around your expected coverage area. See below for an example.

```
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2">
  <Document>
    <name>GME Geo Tagger</name>
    <Folder>
      <!-- Placemark is needed to build the data store -->
      <Placemark>
        <ExtendedData>
          <Data name="/data/meta/InstanceId"/>
          <Data name="/data/DeviceId"/>
          <Data name="/data/Image"/>
          <Data name="/data/Location"/>
          <Data name="/data/Description"/>
        </ExtendedData>
        <!-- Replace with a polygon to set the zoom level -->
        <Point>
          <coordinates>0,0,0</coordinates>
        </Point>
      </Placemark>
    </Folder>
  </Document>
</kml>
```

### 2. Upload KML to create data store and retrieve the asset ID ###
In Maps Engine, upload the KML source file. Make sure the data source type is vector table, not KML. Once the form is uploaded, look at the URL in your browser.

For example:
```
https://mapsengine.google.com/admin/?pli=1#RepositoryPlace:cid=1234567890&v=DETAIL_INFO&aid=18065949428396504284-21231512441249124018&qid=12345678901234567890...
```

Find the “aid=” part of the URL. That is your Asset ID. It will look like two very large numbers separated by a dash. Once the vector has finished processing, you may continue.

### 3. Verify Aggregate's Google Credentials has been setup ###
To setup Aggregate's Google Credentials go to "Site Admin" -> "Preferences".


### 4. Configure ODK Aggregate to publish submission to Google Maps Engine ###
Press "Publish" on "Submissions" -> "Filter Submissions". Alternatively, on "Form Management" -> "Forms List".

  1. Choose 'zALPHA Google Maps Engine'.
  1. Choose whether you would like to upload only, stream only, or both.
    * Upload only - This will take the current table and send it to the service. No new data will be sent.
    * Stream only - This will only send new data after the service is created. No old data will be sent.
    * Both - This will send both old and new data.
  1. Enter the Google Map Engine Asset ID (created in steps above). If the Asset ID was added to the form before it was uploaded to Aggregate the value will auto-populate from parsing the Xform.
  1. Using the "GeoPoint to use" drop down, select which of the XForm's geo point should be used to place the data point in Google Maps Engine.
  1. Select where you would like the photos to hosted:
    * Google Drive - Aggregate will put a copy of the photo in the users' Google Drive and will include a link to the Drive photo in the GME.
    * ODK Aggregate - In the GME submission, Aggregate will provide a link to Aggregate's copy of the photo (no extra copy of the photo is created).
  1. Press "Publish".
  1. You can view your published data on Google Map Engine.

### 5. Viewing data on GME ###
In your data source view in GME, click the link to view the vector table.

Pan and zoom to view the points.

Upon clicking a point, a popup will appear with the data from the form and a link to the image hosted on Aggregate or on Google Drive (depending on what was configured when the publisher was setup).


# Notes #
  * For a submission to be published to GME, the submission must have a prompt with a location. If the submission does not have a location, it will not be submitted. If a submission has more than one location, ODK Aggregate will use the first one. Submissions must not also include any repeats. If a submission has repeats, it will not be submitted.
  * If you want to have Aggregate pre-populate your form with the asset ID, edit the form XML to include a gme\_table\_id attribute in the first sub-node of the instance node (usually called data).
```
<instance>
 <!-- gme_table_id is the asset id of the vector table in gme -->
 <data id="gme-geo-tagger" gme_table_id="18065949428396504284-21231512441249124018" >
  <meta>
   <InstanceId/>
  </meta>
  <DeviceId/>
  <Image/>
  <Location/>
  <Description/>
 </data>
</instance>
```