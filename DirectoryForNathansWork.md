The following projects are located in the [github UW-ICTD org](https://github.com/UW-ICTD):

| ODKScan-core | The image processing back-end for ODKScan |
|:-------------|:------------------------------------------|
| ODKScan\_webapp | Django app for scanning, OMRing and transcribing forms |
| pyxform      | A Python package to create XForms for ODK Collect.|
| XLSXGenerator | A Javascript app for generating paper forms from XLSX spreadsheets. |
| XLSXConverter | Webpage that converter XLSX files into ODK Survey json form definitions |
| FreeSpeech   | Cordova app for guiding, recording and annotating interviews. |
| pyxform\_interface | A Django web interface for pyxform        |
| PythonXLSConverter | A Python script that converts XLS(X) files into JSON form defs for ODK Survey. |
| text\_renderer | Web-service that renders text as pngs using Pango |
| TemplateMaker | Web app for creating ODKScan form definitions |
| xlsform.exe  | A standalone windows executable XLSForm converter |

The ODKScan Android app is located here:
https://code.google.com/p/opendatakit/source/browse?repo=scan


The G2L (global2local) Interpreter Connect server component is located here:
https://github.com/UW-ICTD/G2L-Interpreter-Connect-Server
(Rather than trying to reuse the interpreter connect server I would suggest making a Django app. I didn't know about Django's built-in admin interface at the time I built the PHP admin interface. It would probably be easier to make something new with Django than to work with what I made and your code will be easier to work with in the future.)

The G2L Interpreter Connect Android app is located here:
https://code.google.com/p/g2l-interpreter-connect/