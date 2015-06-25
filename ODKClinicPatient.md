# Introduction #

The ODK Clinic Patient application allows for an Android phone to interface with an OpenMRS server and read and append patient data.

# Initial 10/22/09 #

**Requirements Doc ver. 0.1 up**

**Must meet up later to clarify requirements doc.**

**Get more clarification on OpenMRS data input/output**

**Get dev environments working (OpenMRS, Android, probably AppEngine for server backend/backup)**

**Must start Initial Design Specification Doc**

## Milestones ##

### After Demo ###
  * zoom in on graph
  * proper UI data backing classes (instead of massive switches)
  * lots more datatypes support (UI)
  * figure out why in the world the javascript is so slow
  * patient list -> show visited or not
  * most recent value patient summary (height)
  * not to display graph if no data
  * list view of historical data
  * Networking
  * Interconnection with the ODKClinic Xforms project.
  * Database Rewrite
  * Performance Testing and Tweaking
  * Tweak UI for better usability

# UI Design #
## Tasks ##
  1. Get a group of patients from openMRS and move them to andriod application
  1. Find a patient in andriod application and add new encounter
  1. Sync encounter with patient back to openMRS and remove unessesary patients

## Personas ##


# Versions #
## Version 0.2 ##
[paper design](http://odk-clinic.googlecode.com/files/ODKclinicUI-0.2.png) - completed as of (18 Nov '09)

### Feedback Summary ###

## Version 0.1 ##
paper design - completed as of (8 Nov '09)

### Feedback Summary ###

  * v0.1 designed for remote usage according to feedback/specs from Jul... (practitioner in the field)
  * Yaw... (project owner) has indicated this side of the project is more in-facility which needs to be reflected in v0.2

highlights of changes in store for v0.2:

  * add a separate interface for managing and synchronizing patients
  * stick with single click interaction instead of a click swipe
  * add historical display of 10 factors in patient summary
  * add more details in patient & cohort list