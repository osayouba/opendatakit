# Introduction #


<font color='red'>
This documentation is out-of-date w.r.t. the BETA release.<br>
<br>
While the general outline is the same, much more processing is being done to generate the formDef.json, and it is much more highly structured.<br>
<br>
<b>This is only in ALPHA. Everything may change.</b>

<b>This is only in ALPHA. Everything may change.</b>

<b>This is only in ALPHA. Everything may change.</b>

<b>This is only in ALPHA. Everything may change.</b>

Everything may change at a moments notice, and nothing should be taken as a hard-and-fast standard. <b>PLEASE NOTE:</b> As we integrate and merge ODK Survey and ODK Tables functionality to create and manage the data store, we expect further changes to the formats discussed in this document.</font>

# ODK Survey Operation #

The only file needed for ODK Survey javascript on the phone is the formDef.json.  The xlsx does not need to be on the phone (we just ship it along with the formDef.json, as it is a simple way to know which formDef.json file matches which XLS source file).

The xml file is used for the backwards-compatibility interaction with ODK Aggregate -- both (1) so that ODK Aggregate knows how to store data collected using the formDef.json, and (2) as a 'form' that ODK Aggregate can interpret and report as downloadable by an ODK Survey client using the legacy ODK Collect APIs.  When such an XML 'form' is uploaded to ODK Aggregate, it is used by aggregate to create the data tables to store the submissions, but ODK Survey is expecting that a formDef.json file is present as a media file associated with the form. From ODK Survey's perspective, the xml form definition file is just boilerplate needed to gain access to the formDef.json.  Once the new interfaces to ODK Aggregate become available (the ones that allow bidirectional updates), there won't be any need for the xml file.

If you copy any valid formDef.json file into a directory underneath the forms directory on the phone, ODK Survey will pick up that form definition and present it as an option for the user.  That is the intended behavior -- i.e., no xml or xls is required once the formDef.json gets onto the phone. If you see a different behavior, please open an issue.

To generate the XML, use the Windows-based HTA script, as described here: [backward-compatibility](http://code.google.com/p/opendatakit/wiki/SurveyReleaseNotes#Backward-compatible_operations_with_ODK_Aggregate)

# formDef.json file structure #

Here is the current design for the file structure.

## Overall Goals ##

The overall goals for the XLS2Form and JSON form specifications were to:

  1. Keep XLS2Form transformation as simple and direct as possible. Do very very little semantic processing in this transformation.
  1. Easily allow extensions to form behaviors via overriding or adding functionality with spreadsheet columns.
  1. JSON representation should leverage any JS standard that would aid interoperability.
  1. Must immediately support i18n

The overall goals for the system were:

  1. Move as much logic out of the Java code, and into the Javascript. The assumption is that there are far more people that can hack Javascript than are competent Java coders.
  1. Survey definition should be done with a high level tool -- e.g., XLS2Form. And survey customization should be allowed at a very low level -- e.g., Javascript, CSS.
  1. Move away from XForms- and XPath- based intermediate-level survey descriptions, and toward a more natural flow-chart-style description of the forms in XLS2Form, and then directly to low-level Javascript and JSON in the form definition. (cut out the XForms/XPath middle-man).
  1. No repeat groups. Introduce concept of ‘subforms’ (linked forms).
  1. Separate survey form from data model (introduce notion of ‘table\_id’).

## XLSForm Conversion ##

The JSON is produced by the XLS2Form processor by assembling JSON objects using the column headings. So if you had two column headings, foo.a, foo.b, with values “1” and “2”, you would end up with this object for the spreadsheet row:
```
{ …,
   “foo” : { “a”: “1”,
               “b”: “2” }
…
}
```
The processing is therefore very syntactical. Only minimal validation and reordering is done in XLS2Form, and the bulk of the processing is done during the `buildSurvey` step in the Javascript.

See `default/js/builder.js`

<font color='red'>Where this processing is done is up for debate. In particular, some processing, such as the consolidation of with-next (background) actions into the following rendering action may change in both their representation within the JSON and the stage at which they are processed.</font>

## I18N Support ##

Any displayable text strings may be internationalized by replacing them in the JSON definition with an object of the form:
```
{ “languageTag” : “displayStringForThisLanguage”, 
  …
} 
```
Forms generally have a “default” language in addition to declared languages. The framework should apply i18n translations of strings transparently whenever the passed-in value is an object of this form.

## JSON Format ##

The overall structure of the formDef.json file is:
```
{ “model”: …,
  “settings”: …,
  “survey”: …,
  “calculates”: …,
  “choices”: …
}
```
Roughly speaking, each of these elements is a separate sheet in the spreadsheet. The ‘model’ is synthesized from the ‘survey’ if it is missing in the XLS.

Where:

  * model - defines the data model of the backing store of the survey. The intent is to persist all of this information so that surveys are restartable with no loss of state.  If you have 2 or more forms sharing the same backing store, they would have the same data model, though they might each only reference a subset of the data values defined in that model.
  * settings - defines things like ‘form\_id’, ‘form\_version’, ‘table\_id’ etc. global settings. We haven’t split this into model-specific and form-specific values, as it is just info from the ‘settings’ tab in the XLS.
  * survey - defines the survey processing steps and questions.
  * calculates - user-defined Javascript functions that may reference data values (function arguments may be supported as key-value pairs in a passed-in ‘context’ object -- untested).
  * choices - for defining choice lists.

<font color='red'>Where certain values reside is up for debate. In particular, we likely need a way to include or import a model definition so it can be share across multiple forms.  Whether this is as a separate json file or done upstream by XLSForm is unclear. What resides in the settings and what resides in the model are therefore also unclear.</font>

<font color='blue'>TODO:</font>

  * Defining how choices can draw from dynamic data sets. e.g., database tables or Ajax calls.

  * Defining how complex forms could be split across multiple sheets in a workbook, or how XLS2Form would reference two or more XLS files and combine them (e.g., to have one workbook define the ‘model’ and share that across several form definitions).

## Model ##

The model is an object with property names (field names in the survey) and an ODK-extended / ODK-subsetted version of the JSON Schema ( http://json-schema.org/ ) to define its data type.
```
{ “surveyFieldName” : shemaObj, … }
```
ODK has extensively extended the JSON schema to reflect fine-level control over how the data is mapped into the backing store.

For example, the shemaObj (JSON schema) for a geopoint (‘northernPt’) where the data is specifically declared to be stored in ‘northernLat’, ‘northernLong’, ‘northernAlt’ and ‘northernAcc’ would look something like:

```
“northernPt” : { type: “object”,
  		  elementType: “geopoint”,
		 properties: {
			“latitude” : { type: “number”,
                                                       elementKey: “northernLat” },
			“longitude: {type: “number”,
				       elementKey: “northernLong”},
			“altitude”: {type: “number”,
				       elementKey: “northernAlt”},
			“accuracy”: {type: “number”,
				      elementKey: “northernAcc”}
		}
	}
```

An example of a ‘clientPhone’ survey field that captures a ‘phoneNumber’ type that is represented as a string and has no referenceable properties is shown below:
```
“clientPhone” : { type: “string”,
		  elementType: “phoneNumber” }
```
There are some use cases in ODK Tables where we can make use of phoneNumber elements for, e.g., SMS messaging. We need to know the datum is a phoneNumber for this to be effective.

An example of a multiple-choice list of strings that is persisted as a JSON serialization into the data store is:
```
“members” : { type: “array”,
                       items: {
		  type: “string”
			}
		}
```
An example of a file attachment (‘myImage’), represented as an ODK ‘mimeUri’ is:
```
“myImage” : { type: “object”,
                      elementType: “mimeUri”,
                      “properties” : {
                              “uri” : { type: “string” },
		      “contentType” : { type: “string” }
		},
		isPersisted: true
	}
```
The ODK extensions in these examples are:
  * elementType -- for naming a type, such as a geopoint, dateTime, date, etc. that is not one of the Javascript types.  If not specified, it is assumed to be identical to the JSON schema ‘type’ field.
  * elementKey -- the specific name used in the backing store for this element. This is synthesized from the base propertyName if not explicitly specified.
  * isPersisted -- a flag that is true if this array/object is persisted as a JSON string representation, rather than the leaf elements of the object being persisted. ‘array’ types are implicitly persisted (to avoid association tables in the backing store), whereas ‘object’ types are not -- their individual properties are.  An exception to that rule is the ‘mimeUri’ type -- it is an example of an ‘object’ type that is serialized into a single column as a JSON string.

In the Javascript, you can reference:  `data(‘northernPt.latitude’)`  to access the latitude value of the geopoint, or `data(‘myImage.uri’)` to access the URI of the image file.  (Some of this is not quite ready in the alpha).

ODK2 intends to support the following primitive elementType values:
  * string  (stored as TEXT)
  * integer (stored as INTEGER)
  * number (stored as REAL)
  * date (stored as iso8601 string with time zone, and 00:00:00.000 time portion)
  * dateTime (stored as iso8601 string with time zone)
  * time (stored as iso8601 time string with timezone hh:mm:ss.sssZ)
  * boolean (stored as INTEGER with 0 = false, 1 = true)
  * mimeUri (stored as JSON string in TEXT field)

and the ‘array’ and ‘object’ compositional types.  Note that dates and times and the mimeUri are stored as JSON ‘string’ types, but have a complex interpretation in ODK2.

Timezone treatments are problematic in javascript. The built-in javascript functions generally do not preserve timezones (they convert it to whatever local time your device has). So you will lose the original time zone unless special processing is done. It is entirely unclear how we should handle this.

`isPersisted` may be only partially supported.

ODK Tables may also define and use ‘phoneNumber’, ‘collectForm’ and one or two other elementType values.

## Settings ##

A simple list of key-value pairs:
```
[  { “setting”: …, “value”: …}, …]
```
“rowNum” is added by the XLS2Form for traceability back to the row in the XLS file.

## Survey ##

A list of the survey prompts:
```
[ { … }, … ]
```
If the prompt references a datastore element, the ‘name’ field identifies that field.

“rowNum” is added by the XLS2Form for traceability back to the row in the XLS file.

<font color='blue'>TODO:</font>

Describe prompts that update multiple fields at once.

The prompt types are defined in the `default/js/prompts.js` file.  This is an evolving list.

## Calculates ##

A list of calculation formulas:
```
[
  { name: “formulaName”, 
    calculation: “javascript formula”
  },
    …
]
```

“rowNum” is added by the XLS2Form for traceability back to the row in the XLS file.

## Choices ##

An object holding the data from the choice lists tab in the spreadsheet.  The property names are the choice list names, and these map to an array of choice values. The choice values have a ‘name’ which is the value of the choice selection, and a ‘label’ which is the display name for the choice.
```
{
        "yesno": [
            {
                "rowNum": 2, 
                "name": "yes", 
                "label": { “default”: "yes", “french”: “oui” }
            }, 
            {
                "rowNum": 3, 
                "name": "no", 
                "label": { “default”: "no", “french”: “non” }
            }
        ], 
```