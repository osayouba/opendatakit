**ODK Clinic v1.0, the app described below, has been deprecated, but the code is available [here](https://code.google.com/p/opendatakit/source/browse?repo=clinic). Its replacement, ODK Clinic v2.0, was developed by the ODK team in collaboration with [AMPATH](http://www.ampathkenya.org/). A demo video can be seen [here](http://www.youtube.com/watch?v=skV25YchXlE). The code for v2.0 was created for a research project and is not suitable for public release. If you are interested in helping with a public release of ODK Clinic v2.0, contact [Nafundi](http://nafundi.com).**

[OpenMRS](http://openmrs.org) is an open source medical record system used in many developing countries. In most deployments of OpenMRS, clinicians fill out a paper form with patient data. They place that form into the patient's folder and every few days, data clerks enter that information into the OpenMRS server. Once in the server, the data is available for clinical studies, administrative reports and sometimes printed patient summaries. While this is an improvement over a purely paper-based system useful patient data (clinical alerts, lab tests, drug interactions) are not readily available to clinicians for decision making at the point of care.

To address this issue, the [Open Data Kit](http://opendatakit.org) (ODK) team has been working to bring mobile phone connectivity using ODK to OpenMRS. Our goal is to bring a simple and elegant OpenMRS experience to the phone. To this end, Clinic provides patient demographic and encounter history download.

We have much more functionality to add. If you are interested in helping with this ongoing effort, should join the [ODK mailing list](http://groups.google.com/group/opendatakit) and help us bring mobile to OpenMRS. You should also check out other projects in this space!
  * https://wiki.openmrs.org/display/docs/Mobile - List of all the OpenMRS mobile projects
  * https://bitbucket.org/androidopenmrs/androidopenmrs/overview - Running the OpenMRS platform on an Android phone
  * https://wiki.openmrs.org/display/docs/OpenMRS-jr - Download patients and complete forms on J2ME phones
  * http://medic.frontlinesms.com/ - Connecting OpenMRS to SMS tools

## Installing ODK Clinic ##
  * Please read all the instructions and notes before beginning.
  * You will need an Android phone (like the [T-Mobile G1](http://en.wikipedia.org/wiki/T-Mobile_G1),  [T-Mobile myTouch 3G](http://en.wikipedia.org/wiki/MyTouch), [Verizon Droid](http://en.wikipedia.org/wiki/Motorola_Droid), [Google Nexus One](http://en.wikipedia.org/wiki/Nexus_One)) to install ODK Clinic. If you don't have an Android phone and you have some technical expertise, you can follow our instructions for [installing the emulator](http://code.google.com/p/opendatakit/wiki/DevelopmentEnvironment).
  * If you have installed a previous version of ODK Clinic, please uninstall it first. Go into Settings, Applications, Manage Applications, and find the old version and uninstall it. You can then proceed.

### Downloading from Web ###
  1. From your phone's application drawer, choose Settings, then Applications. Make sure Unknown sources is checked.
  1. Return to the application drawer and choose Browser. Navigate to http://code.google.com/p/opendatakit/downloads/list and select ODK Clinic vN.N.apk
  1. In the download window, you will see ODK\_Clinic\_vN.N.apk. Select it to install after you approve the security settings.

## Using ODK Clinic ##
  * When ODK Clinic is run for the first time, you will be prompted to enter the connection details of your OpenMRS server. Once you've correctly entered in the server details, press the back button on your Android phone. The default rwinkwavu.cs.washington.edu server will work well for demos.
> > ![http://wiki.opendatakit.googlecode.com/hg/resources/clinic-settings.png](http://wiki.opendatakit.googlecode.com/hg/resources/clinic-settings.png)
  * If you currently don't have any patients downloaded, ODK Clinic will automatically display the "Download Patients" window. Select a cohort from the list in the "Select Cohort" dialog box and download. If the list of cohorts is not up to date, press "Refresh" to update the list. Once ODK Clinic has downloaded all the patients from the cohort, you will be returned to the main screen.
> > ![http://wiki.opendatakit.googlecode.com/hg/resources/clinic-cohort.png](http://wiki.opendatakit.googlecode.com/hg/resources/clinic-cohort.png)
  * Downloaded patients can be searched for in the top search box. Patients can also be optionally found by barcode by clicking the barcode icon next to the search box and scanning the patient's barcode.
> > ![http://wiki.opendatakit.googlecode.com/hg/resources/clinic-search.png](http://wiki.opendatakit.googlecode.com/hg/resources/clinic-search.png)
  * Selecting patient will bring up a patient information screen, with a list of available patient observations. Clicking on a patient observation will bring up a screen providing a detailed view of the latest patient observations.
> > ![http://wiki.opendatakit.googlecode.com/hg/resources/clinic-patient.png](http://wiki.opendatakit.googlecode.com/hg/resources/clinic-patient.png)    ![http://wiki.opendatakit.googlecode.com/hg/resources/clinic-observation.png](http://wiki.opendatakit.googlecode.com/hg/resources/clinic-observation.png)


## Setting Up OpenMRS for ODK Clinic ##
### Adding a module ###
If you have not installed OpenMRS before, the [OpenMRS Appliance](http://wiki.openmrs.org/display/RES/OpenMRS+Appliance) is a great way to started. If you already have OpenMRS installed, you will need the  [Reporting Compatibility](http://modules.openmrs.org/modules/view.jsp?module=reportingcompatibility) and [XForms](http://modules.openmrs.org/modules/view.jsp?module=xforms) modules.
  1. Once you have downloaded the .omod files for a module, login to OpenMRS as an admin and click on the "Administration" tab.
  1. Under the "Modules" heading, click on the "Manage Modules" link.
  1. Click the "Browse..." button under the "Add Module" section, and browse to the .omod file you've downloaded. click the "Upload" button, and wait for OpenMRS to restart and load the module. If OpenMRS has successfully installed the module, then the module's name should be displayed under the "Manage Modules" section.

### Creating cohorts for download ###
Patients are downloaded into ODK Clinic as part of a "cohort". To create a cohort, you will need the [Reporting Compatibility](http://modules.openmrs.org/modules/view.jsp?module=reportingcompatibility) module.
  1. Log into OpenMRS, click on the "Cohort Builder" tab in the main horizontal navigation bar (next to "Administration" tab).
  1. Under the "Search" heading, search for the patients which match the criteria for the cohort that you are creating. The various tabs allow you to search based on their respective properties, eg. you can search for a "Male" between the ages of "5" and "10" under the "Patient Attributes" tab.
  1. Once you've found a suitable cohort, you can save it by clicking on the save icon at the bottom of the list (next to the "Actions:" heading). A popup will appear. Enter the name of your cohort and a short description of it, and click the "Save" button.

### Choosing medical history for download ###
Patient history is downloaded in ODK Clinic using the Medical History functionality in the [XForms](http://modules.openmrs.org/modules/view.jsp?module=xforms) module.
  1. Log into OpenMRS, click on the "Administration" tab in the main horizontal navigation bar.
  1. Under the "XForms" heading, click on "Medical HIstory Fields".
  1. Search for the concepts you would like to appear on the phone. Drag them from the search result on the right, into the list on the left.

## Notes ##
  * Clinic has only been tested extensively with OpenMRS 1.6.
  * Be careful when using Clinic with real patient data. Clinic does not currently encrypt the transfer or storage of data.