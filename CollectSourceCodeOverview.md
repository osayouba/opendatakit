The main ODK Collect source is on the default branch.

Beginning with ODK Collect 1.4 (i.e., to support the new Maps 2.0 API), you will also need the playservices project: http://code.google.com/p/opendatakit/source/checkout?repo=playservices

We tag each release on that branch.

All packages are under `org.odk.collect.android`  The key files are:

  * activities
    * FormEntryActivity -- responsible for displaying a new (blank) form or editing a saved form.
    * other activities are generally self-explanatory.

  * logic
    * FormController -- encapsulates JavaRosa's FormController functionality -- so that we know what interfaces we actually use into JR.

  * tasks
    * the longer-running actions like downloadng forms, uploading forms, deleting forms and instances, saving forms, scanning the forms directory for changes, and loading a form or instance for processing.

  * views
    * support classes to make constructing widgets and activities easier.

  * widgets
    * the various widgets (UI constructs) used to render individual questions.
    * WidgetFactory -- this decides which widget to use based upon appearance or other fields in the XForms XML.


ODK Collect uses its own branch of the JavaRosa project https://bitbucket.org/m.sundt/javarosa/src/