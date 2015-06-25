# ODK Core #
## Unit Testing ##
Build unit tests for ODK applications.  This would be a great way to familiarize yourself with the ODK code.
<br><i>Languages:</i>  Java<br>
<br>
<h2>Internationalization</h2>
We have an active community that wants to contribute translated strings for ODK. Currently, we rely on email and spreadsheets to accomplish this. This project would build an online application that would automate this process.  Note:  this application isn't specific to ODK and could benefit any open Android project.<br>
<br><i>Languages:</i>  Likely Java, but open for discussion.<br>
<br>
<h2>Grouped Questions</h2>
Currently, Collect uses a question-at-a-time interaction.  The goal of this project would be able to let form designers specify groups of questions that they'd prefer to be displayed on a single screen.<br>
<br><i>Languages:</i>  Java<br>
<br>
<br>
<h1>External Support</h1>
<h2>OpenRosa Server Support</h2>
There are several storage tools that support the JavaRosa subset of XForms.  This project would work with these groups to better integrate form download and instance submission into these tools.  Specifically we're targeting OpenXData, GATHER and Sahana.<br>
<br><i>Languages:</i>  Java<br>
<br>
<h2>OpenMRS Support</h2>
There are a number of projects that integrate OpenMRS into ODK. These projects include form filling and patient record syncing. You will work with the community to make these projects deployment ready.<br>
<br><i>Languages:</i>  Java<br>
<br>
<h2>Simple Server Support</h2>
One of the most requested features from the community is the ability to run a local server that can accept data from Collect and store it on a local machine.  Simplicity is key here.<br>
<br><i>Languages:</i>  Up for discussion.<br>
<br>
<h2>XML Form Support</h2>
Nokia Data Gathering and CyberTracker use XML form definition and data submission.  Work with the Nokia and CyberTracker communities to build appropriate translators for ODK.<br>
<br><i>Languages:</i>  Up for discussion.<br>
<br>
<h2>ODK Route</h2>
To simplify data submission, we envision an application running in the cloud that can accept JavaRosa XForm submissions, and then stream them to the datastore of the user's choice in the format of the user's choice.  ODK Aggregate has much of this functionality, so this project would be splitting out that functionality into a stand-alone application.<br>
<br><i>Languages:</i>  Java<br>
<br>
<br>
<h1>Form Designers</h1>
<h2>ODK Build</h2>
Extend ODK's web-based drag-and-drop XForm designer to support complex logic.  The code for this project is here: <a href='http://github.com/clint-tseng/odkmaker'>http://github.com/clint-tseng/odkmaker</a>
<br><i>Languages:</i>  Ruby<br>
<br>
<h2>PurcForms</h2>
PurcForms designer is an XForm designer built by the community to run on a local machine.  However, it's too complex for many implementers to use.  The task here is to work with the community to simplify the UI.<br>
<br><i>Languages:</i>  Java<br>
<br>
<br>
<h1>JavaRosa Extensions</h1>
<h2>Unit Testing</h2>
Build unit tests for the JavaRosa core project.<br>
<br><i>Languages:</i>  Java<br>
<br>
<h2>Media as Questions</h2>
Currently, questions and answers can only be specified as text.  We have the framework in place to enable embedding images, video and sound into or as a replacement for question text.  Now we need someone to implement it.  This will require minor modification to Collect's UI elements, and some additions to the JavaRosa core.<br>
<br><i>Languages:</i>  Java<br>
<br>
<h2>ItemSets</h2>
Support for dynamic item sets of multi-select and select-one questions.<br>
<br><i>Languages:</i>  Java<br>
<br>
<h2>XML Attributes</h2>
Support for moving all attributes from the raw XML up to the form entry API.<br>
<br><i>Languages:</i>  Java