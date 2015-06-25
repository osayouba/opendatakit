# Debugging Suggestions #

The Google App Engine Plugin for Eclipse is buggy. You will most likely experience problems with the DataNucleus Enhancer. The first step to deal with non syntax issues should be to perform a clean on the project.

http://opendatakit.org/images/DirectionPics/ProjectClean.JPG


Another way to force Eclipse to rebuild everything is to switch the App Engine SDK as Eclipse will rebuild everything from scratch. To do this Right-click on ODK Aggregate project -> Properties -> Google -> App Engine, and toggle between "Use default SDK" and "Use specific SDK"(selecting the same SDK for both). This causes Eclipse to rebuild the whole project.

http://opendatakit.org/images/DirectionPics/ToggleForDebug.JPG