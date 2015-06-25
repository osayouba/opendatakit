Start by reading every wiki containing 'Aggregate' here: http://code.google.com/p/opendatakit/w/list

To understand the database, I recommend starting with an empty server, looking at its database structures, then uploading a simple full-featured form (e.g., GeoTagger v2), looking at what gets created, and then uploading a data instance, and seeing what changes.

w.r.t. the code repository, the 3 branches of importance are:

  * default -- current stable tip of whatever we have released
  * v1\_4 -- the 1.4.x product releases. These are stable except immediately before releases. Use the tags to ensure stability.
  * ui\_experiment -- unstable actively developed code

You should generally use the 'default' or 'v1\_4' branches.

The code uses GWT for the user interface.

  * In general, classes within a 'client' package are compiled into Javascript by GWT and run within the browser.
  * In general, classes within a 'common' package are shared between the Javascript (browser) and Java (server) code.
  * All other classes are server-side Java code.

w.r.t. the layout of the source code, we have tried to separate the 'framework' code from the 'application' code.

org.opendatakit.aggregate -- code specific to ODK Aggregate

  * The Submission class is the data structure holding a submitted filled-in form
  * The Form, FormInfo, etc. classes persist and model different aspects of a Form definition.
  * The 'parser' package isolates all the code specific to JavaRosa (this code is shared with ODK Briefcase).
  * The 'server' package contains the GWT server endpoints
  * The 'servlet' package holds the servlets (raw HTTP servlets) used by ODK Aggregate.
  * All ODK Tables server functionality is isolated to the 'odktables' package.
  * Other packages refer to different functional aspects of the software (e.g., formatters, background tasks, publishers to external services)


org.opendatakit.common -- framework code that should be reusable in other web applications

  * code that handles user authentication, security grants / permissions.
  * data modeling and persistence interfaces for interacting with MySQL, PostgreSQL, BigTables