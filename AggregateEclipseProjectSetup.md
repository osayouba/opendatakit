# Creating an Eclipse Project for ODK Aggregate #

<font color='red'>NOTE:</font> For Building the ODK Aggregate v1.0 source, download the Mercurial tree and update to the v1\_0 tip (or uiExperiment for the unstable tip).  Then follow the directions in the CONFIGURE.txt.

This page takes you through the recommended sequence for setting up ODK Aggregate in Eclipse. **Make sure you are using JRE 7.**

> (1) Go to the File menu and select New->Project.
http://opendatakit.org/images/DirectionPics/NewProject.JPG

> (2) A New Project wizard window should appear on the screen. From the New Project wizard options, select the Mercurial folder from the list and then select “Clone repository using Mercurial”. Once you have selected “Clone repository using Mercurial” click on the Next button.(NOTE: this step assumes that Eclipse has the Mercurial plugin installed)
http://opendatakit.org/images/DirectionPics/MercurialProject.JPG

> (3) The next screen should be titled “Select/Create Location”. In the Repository Location section of the window enter the URL  "https://code.google.com/p/opendatakit.aggregate/" into the text bar provided for a URL. After entering the URL click on the Next Button.

http://opendatakit.org/images/DirectionPics/CloneRepo.JPG

> (4) On the next screen it will display a number of projects. You should check the eclipse-gae-aggregate and odk-gae-settings projects.   Click on the Finish button.

http://opendatakit.org/images/DirectionPics/FinishImportProject.JPG

> (5) See the CONFIGURE.TXT in the root directory of the mercurial tree to complete the configuration.