# ODK Voice #
**Voice is in an early stage of development and has not been officially released.**

## Overview ##
ODK Voice is a system for delivering surveys using automated phone dialogs (IVR). Participants can call or be called by ODK Voice, and answer a series of questions using the phone keypad (DTMF) or by recording audio responses.

ODK Voice is based on [XForms](http://code.javarosa.org/wiki/buildxforms) and is powered by [JavaRosa](http://code.javarosa.org/). Data collected by ODK Voice is automatically aggregated by [ODK Aggregate](http://code.google.com/p/opendatakit/wiki/ODKAggregate), or can be hooked up to any backend with XForms support (e.g. OpenMRS). Ideally, this tool would be used to collect data from participants who don't have access to smartphones and specialized data collection software, or low-literacy participants.

## Features ##
  * Support for most JavaRosa XForms control types:
    * Audio recording
    * Integer
    * String
    * Select1
    * Select
    * Date
    * Info (Read-Only)
  * Multi-Language XForms Support
  * Multiple form support
  * Easy prompt recording over the phone
  * Integration with ODK Aggregate (and easy integration with other XForms backends)
  * Form and prompt management
  * Outgoing call management
  * OpenMRS integration (in development)
  * Speech Recognition (ASR) _not supported_

## Setup Instructions ##

ODK Voice can be run locally with a computer and GSM modem or through a hosted provider such as [Voxeo](http://www.voxeo.com).

The documentation on how to set up ODK Voice is available under the /doc folder of the ODKVoice server.  See http://voice.opendatakit.googlecode.com/hg/war/doc/help.html?r=2ebbf8264eaf3e78645ebaec82745cd30614733d

## Demo ##

The ODK Voice demo server has been taken down. Please volunteer to set one up for others! The old demo instructions are commented-out in the wiki markup for the next person who wants to set up a demo.

<a href='Hidden comment: 
We have set up a demo of ODK Voice for you to try out for yourself.

*Instructions*
# Call (857) 244-1900. This will connect you to the ODK Voice system, where you will be able to fill out the sample form.
# When you finish filling out the form, you can see your answers at http://odk-voice.appspot.com/formSubmissions?odkId=sample-2. You will need a gmail account to sign in and see the answers.
# You can view the ODK Voice admin console pages at http://alerer-asterisk.mit.edu:8080/odk-voice.
# Click on [http://alerer-asterisk.mit.edu:8080/odk-voice/upload.jsp Manage forms] to download/view the uploaded XForms and upload new XForms at. Please do not upload or delete forms from the demo server without speaking with alerer at mit dot edu.
# Click on [http://alerer-asterisk.mit.edu:8080/odk-voice/callcontrol.jsp Manage outbound calls] to schedule ODK Voice to call certain phone numbers and deliver the survey (alpha).
# Click on [http://alerer-asterisk.mit.edu:8080/odk-voice/logs View logs] to view the server logs. If there are errors after you upload a new form (i.e. you call in and it doesn"t work), you can view the logs to try to debug the problem.
# Click on [http://alerer-asterisk.mit.edu:8080/odk-voice/record.jsp Manage prompts] to go to the prompt management page. To record new prompts, you have to call into the system and press 7 in the "Welcome to the ... survey" dialog. If there are any unrecorded prompts (or prompts you"ve deleted in the Manage forms page) you will be asked to record them and they will appear in the red box on the page (this sounds weird but should make sense when you do it).
'></a>

### Having Problems? ###
Please direct questions to the [mailing list](http://groups.google.com/group/opendatakit).