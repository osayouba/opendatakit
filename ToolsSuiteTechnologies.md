# Introduction #

This document lists the libraries we use in the ODK 2.0 tools.

# Javascript #

The active list of 3rd party libraries can be found in the `libs` directory.

For rev 126/7 and earlier, this is under:
```
framework/libs
```
For later releases, this is under:
```
system/libs
```

At this time, these are:
  * chai -- testing framework for Tables Javascript
  * d3 -- graphics library
  * jquery-mobile -- widgets and layout designed for mobile devices (replaced by bootstrap after rev 126/7)
  * jquery-csv -- for reading CSV files for external datasets
  * jquery-qrcode -- for reading and generating QRCode barcodes
  * mobiscroll -- for date and time widgets (removed after rev 126/7)
  * mocha -- testing framework for Tables Javascript
  * backbone -- for class inheritance; we have our own custom persistence layer
  * d3\_chart\_box -- extension to d3 for charting
  * handlebars -- templating framework for all HTML rendering
  * jquery -- utility library (may be replaces with zeptos or other lighter frameworks in the future)
  * require -- module dependency and loading framework
  * text -- extension to require for loading plain text files (vs. javascript). Used to dynamically load handlebars template files.
  * underscore -- utility library
  * XRegExp -- utility library for extended regular expressions supporting UTF-8 patterns.

Some of our Tables demonstration forms have been using `ratchet`

# Android Java #

On the devices, we make use of a wide variety of libraries. These will be in the `libs` directories of the various projects.

The key technologies that drive the inclusion of many of these libraries are:
  * Jackson2 JSON and XML serialization library. This is used in our REST synchronization interface with the server. It is also used in most places internally, though there are some GSON and JSON library usages.
  * Wink REST client. This is a stripped-down version of Wink, a REST interface library. We use Wink on the server and client, with **only** Javascript transport to the client (while the server can speak XML, the client only speaks Javascript). This client uses URLConnection internally.
  * Apache Http Client 4.2 custom build. This (httpclientandroidlib) library is used only by the legacy 1.x pipeline.
  * KXML. We are using the Android-supplied version of this after rev 126/7. For rev 126/7 and earlier, this is a hold-over from a 1.x Javarosa library dependency.

# Server Java #

On the server, we are using a considerable number of libraries. You can see the list by browsing to
```
eclipse-aggregate-gae/war/WEB-INF/lib
```

The significant ones are:
  * Spring Framework and Spring Security for configuration and security.
  * we use our own database abstraction layer.
  * Wink 1.4 for REST api (full library)
  * Jackson2 library for JSON and XML serialization.
  * Selenium for web UI and integration testing
  * prior to 1.4.6, we used BoneCP for SQL database connections
  * beginning with 1.4.6, we use c3p0 for SQL database connections
  * various libraries for interfacing with Google services






