# Introduction #

XLSForms are xls or xlsx workbooks that can be used to generate forms for ODK Scan and other ODK tools. Below the XLSForm format for ODK Scan forms is described:

[An example form is available here.](https://docs.google.com/spreadsheet/ccc?key=0Ak1svGcuI9iqdEFiRFpEZ0NTNUMzb0VaUm5sby1pbUE)

Follow these instructions for setting up your own ODK Scan XLSXGenerator and TemplateMaker: ScanWebApps

The XLSXGenerator will create printable form images as well as a template needed by Scan to process the generated form.

# Worksheets #

A workbook is composed of one or more worksheets. Most spreadsheet programs have controls in the bottom-left corner for adding and removing worksheets. Some worksheet names have special meanings in XLSForms, they are [survey](#survey.md), [choices](#choices.md), and [settings](#settings.md).

## survey ##

All XLSForms require a survey sheet. The survey worksheet contains the structure and most of the content of the form. It contains the full list of questions and information about how they should be presented. Most rows represent a question; the rest of the rows specify control structures such as groups.

Example survey sheet:

| **type** | **name**   | **label**            |
|:---------|:-----------|:---------------------|
| text     | user\_name | What is your name?   |
| integer  | user\_age  | How old are you?     |

### survey sheet columns: ###

  * #### type ####

| text   | Write-in text input. |
|:-------|:---------------------|
| integer| Integer (i.e. whole number) input.|
| decimal| Decimal input.       |
| note   | Displays a label with no input. |
| select\_one `choice_list` | Multiple choice question; only one answer can be selected. |
| select\_multiple `choice_list` | Multiple choice question; multiple answers can be selected.|

Scan specific types:
| tally `number` | Print the specified number of bubbles, and count up how many are filled in. |
|:---------------|:----------------------------------------------------------------------------|
| markup         | A label that only appears on the printed form.                              |

Groups:
Groups are drawn as columns. Nested groups are drawn as rows within a column. For example:

| **type** | **name**   | **label**            |
|:---------|:-----------|:---------------------|
| begin group|            |                      |
| note     |            | This is in the left column |
| begin group|            |                      |
| note     |            | These are            |
| note     |            | in rows in           |
| note     |            | the right column     |
| end group|            |                      |
| end group|            |                      |

  * #### name ####

The name sets the variable the answer is stored under.

  * #### label ####

The label is typically the question you want to ask. Labels can contain line-breaks (most spreadsheet software allows you to create line breaks using ctrl or alt + enter).

  * #### min\_height ####

Form segments are not automatically re-sized to fit text. If the text content is overflowing you can set the min\_height to some number of pixels to make the segment larger.

  * #### default/constraint ####

When scanned forms are exported to Collect the default and constraint columns can be used to set default values to be entered as answers, as well as xpath constraints on the answers. [For more information see the XLSForm documentation.](http://opendatakit.org/help/form-design/xlsform/)

  * #### font ####

Using the font column it is possible to customize the font of individual labels.

  * #### itemProperties.y\_offset ####

If a question's label overlaps its bubbles, it may be necessary to manually set the pixel distance from the top of the segment where the bubbles begin using this property.

## choices ##

The choices sheet allows you to specify choices for use with `select_` type questions.

Example choices sheet:

| **list\_name** | **name**  | **label** |
|:---------------|:----------|:----------|
| cities         | seattle   | Seattle   |
| cities         | paris     | Paris     |
| cities         | new\_york | New York  |
| cities         | mumbai    | Mumbai    |

### choices sheet columns: ###

  * #### list\_name ####

The list\_name is used to group choices into lists that can be referenced from the survey sheet. The example above could be referenced as follows:

| **type**           | **name**       | **label**                       |
|:-------------------|:---------------|:--------------------------------|
| select\_one cities |cities\_visited | Which cities have you visited?  |

  * #### name ####

The name sets the value that the choice is stored as. This is useful in constraint formulas, and for formatting exports.

  * #### label ####

Label sets the text that is drawn next to each choice bubble. There is a limited amount of space for them.

## settings ##

Example settings sheet:

| **form\_title** | **font**                   |
|:----------------|:---------------------------|
| Example Form    | 12pt Verdana, sans-serif   |

### settings sheet columns: ###

  * #### form\_title ####

This sets the form title displayed at the top of the form.

  * #### title\_font ####

This sets the form title's font.

  * #### font ####

This sets the font of all text used in the form.