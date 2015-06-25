# Introduction #

I use "widget" to refer to the external app that would be providing a screen and data gathering capabilities. The BreathCounter app is an example of this.  You should familiarize yourself with that app ( [BreathCounter](http://code.google.com/p/opendatakit/source/browse?repo=androidextras) ) and the form that launches it [breathTest.xml](http://code.google.com/p/opendatakit/source/browse/BreathCounter/breathTest.xml?repo=androidextras) ).

Here is how I would add this functionality. It avoids creating a new datatype in Javarosa, or modifying Javarosa from the custom mods already available in ODK Collect 1.2.

(1) within the form definition `<instance>` block, you would have:

```
...
   <groupname>
       <pathLength/>
       <area/>
       <pathFile/>
   </groupname>
```

where groupname is arbitrary. But the other tags (pathLength, area, pathFile) are the values that your widget would be returning and you want inserted into ODK Collect's form.

Keep in mind that decimal values are limited to 10 digits (no scientific notation).  So you might need a scale factor or units tag.

(2) The bind section would be a normal bind section, defining all the data types of the return values from your widget:

```
<bind nodeset="/.../groupname" relevant="..." />
<bind nodeset="/.../groupname/pathLength" type="decimal"/>
<bind nodeset="/.../groupname/area" type="decimal"/>
<bind nodeset="/.../groupname/pathFile" type="binary"/>
```

(3) And the body would be as if you were entering this entirely manually using the field-list appearance, BUT WITH AN ADDITIONAL 'intent' ATTRIBUTE:

```
<group ref=".../groupname" appearance="field-list"
    intent="org.whatever.PerimeterWidget">
  <label>Enter the border path information</label>
  <input ref=".../groupname/pathLength">
    ...
   </input>
   <input ref=".../groupname/area">
   ...
   </input>
   <upload ref=".../groupname/pathFile" mediaType="text/kml">
    ...
    </upload>
</group>
```

The intent for your widget would be identified by this 'intent' attribute (`org.whatever.PerimeterWidget`).

The response Bundle returned from your widget would have keys with values for "pathLength", "area" and "pathFile"
I.e., the field names within the group identify the keys in the Bundle that hold the values that should be stored into the ODK Collect form.

# ODK Collect Changes #

## ODKView ##

Inside ODKView, you would do:

```
   // constructor for field-list views
   public ODKView(Context context, FormEntryPrompt[] questionPrompts,
            FormEntryCaption[] groups) {
        super(context);

        // TODO: save the list of prompts into ODKView.

        // get the group we are showing -- it will be the last of the groups in the groups list
        FormEntryCaption c = groups[groups.length-1];

        String intent = c.getFormElement().getAdditionalAttribute(null, "intent");
        if ( intent != null && intent.length() != 0 ) {

          // TODO: construct the Intent for the external widget
          // TODO: save values to record that we are expecting a response
          // see the ExString widget for example
          startActivityForResult( the intent we built );
          return;
       } else {
          // current code
       }
...
    // TODO: add handler for results from activity
    // that iterates over the question prompts and 
    // plucks data out of the response bundle, placing
    // the values in the form.
```

The handler for the result bundle would use the list of `questionPrompts[]` to extract the values from the bundle and stuff them into the form.

If you handle the `startActivityForResult()` errors and exceptions correctly, you could fall back to the normal processing / field-list rendering in the case where the external app was not installed on the phone.

See the `ExString` widget and familiarize yourself with the `BreathCounter` example to understand the external intent flow.

Furthermore, the intent fired to launch the external app could be populated with additional key-value pairs that are populated from the label, hint, etc. fields that are part of the regular presentation so that the UI prompts in the widget could be customized within this widget (and entirely supplied by the form definition). e.g., the intent sent would have key-values:
```
    intent['questionFontSize']= questionFontSize
    intent['language']=formLanguage

    intent['label']=whatever is in the <group> block of the body
    intent['hint']=whatever is in the <group> block of the body
    intent['area.label']= whatever is the <label> of the <area> prompt in the <group> block in the body
      etc.
```