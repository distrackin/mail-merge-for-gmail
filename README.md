# Mail Merge for Gmail
Send personalized emails based on Gmail template to multiple recipients using Gmail, Google Spreadsheet, and Google Apps Script. An alternative for using BCC.

## Overview
Similar to [the mail merge feature available in Microsoft Word](https://support.office.com/en-us/article/use-mail-merge-for-bulk-email-letters-labels-and-envelopes-f488ed5b-b849-4c11-9cff-932c49474705), this Mail Merge for Gmail allows Gmail/G Suite users to send personalized emails to the recipients listed in the spreadsheet. Some notable features are:  
- Use Gmail drafts as template for mail merge. HTML styling is preserved in the personalized emails.
- The **Group Merge** feature available for combining two or more entries with the same recipient.

## How to Use
### 1. Prepare
Copy [the sample spreadsheet](https://docs.google.com/spreadsheets/d/1pVoKzoldYOaEXhbEmpsLJAZqmkB1IDncQ6rTXlbqETY/edit?usp=sharing) to your Google Drive by `File` -> `Make a copy`

### 2. Make your List
Edit your spreadsheet in any way you want to. If you want to change the sheet name `List`, make sure to change the value of `DATA_SHEET_NAME` in sheet `Config`. 

#### Notes
- Keep the first row of the spreadsheet as a header, i.e., the first row should be the name of each column, and nothing else.
- The default value for the field (column) name of recipient email address is set to `Email`; change the value of `RECIPIENT_COL_NAME` in sheet `Config` to suit your needs
- Changing sheet name of `Config` is not recommended unless you are familiar with Google Apps Script and can edit the relevant section of the script.
- The lower-case letter `i` is reserved as part of the group merge function, as described below, and cannot be used for a column name.
- Line breaks within a spreadsheet cell will be reflected in the plain text version of the merged mail, but not in the HTML version.

### 3. Create a template draft on Gmail
Create a Gmail draft to serve as the template. By default, the merge fields are specified by double curly brackets, i.e., `Dear {{Name}},... `. The field names should correspond with the column names of the spreadsheet (case-sensitive). If HTML mail is enabled, text styles of the draft template will be reflected on the personalized emails.

#### Group Merge
In a case where there are two or more entries in your list with the same recipient, you might want to group the entries into a single email rather than sending the recipient similar emails more than once. Group merge enables you to specify which field to list individually and which to combine in an email, as shown in the example below.

The group merge field is, by default, marked by double square brackets, i.e., `[[Meeting ID: {{Meeting ID}}]]`. The merge fields (the curly brackets) nested inside this group merge field will be merged reclusively if there are two or more rows for the same recipient. A special index field `{{i}}` can be used inside the group merge field to indicate the index number within the group merge. To enable the group merge function, change the value of `ENABLE_GROUP_MERGE` to `true`.

#### Notes
- The subject of the template Gmail draft must be unique. An error will be returned during the process of Step 4 below if there are two or more Gmail templates with the designated subject.
- You can use merge fields and group merge fields in the subject line, too.
- If an invalid field name (e.g., a field name that does not match the column names) is designated, the field is replace by `NA` or the text value you entered for `REPLACE_VALUE` in sheet `Config`.

### 4. Create Personalized Gmail Drafts or Send Merged Emails
From the spreadsheet menu `Mail Merge`, you can choose either to `Create Draft` or directly `Send Emails` based on the template. You will be prompted to enter the subject of the template mail that you created in the above Step 3.

#### Notes
- The boolean value `BCC_TO_MYSELF` in sheet `Config` determines whether or not to include the sender's email address as BCC in each personalized email. The default value is set to `true`; change to `false` if you do not want to set the BCC.

## Example of Group Merge
Given a list of email addresses below:
|Email|Name|Meeting ID|Date|Start Time|End Time|
| --- | --- | --- | --- | --- | --- |
|`john@example.com`|John|00001|May 7, 2020|13:00|14:00|
|`mary@sample.com`|Mary|00002|May 7, 2020|14:30|15:30|
|`john@example.com`|John|00003|May 8, 2020|9:00|10:00|

and a Gmail draft with the below text as its body:
```
Dear {{Name}},

Thank you for your application.
Details of your meeting are as below:

[[
Meeting No. {{i}}
Date: {{Date}}
Time Slot: {{Start Time}} – {{End Time}}
Meeting ID: {{Meeting ID}}

]]

We look forward to seeing you!
```

The personlized emails using group merge will look like this:  
**Email to John:**
```
Dear John,

Thank you for your application.
Details of your meeting are as below:

Meeting No. 1
Date: May 7, 2020
Time Slot: 13:00 – 14:00
Meeting ID: 00001

Meeting No. 2
Date: May 8, 2020
Time Slot: 9:00 – 10:00
Meeting ID: 00003

We look forward to seeing you!
```
**Email to Mary:**
```
Dear Mary,

Thank you for your application.
Details of your meeting are as below:

Meeting No. 1
Date: May 7, 2020
Time Slot: 14:30 – 15:30
Meeting ID: 00002

We look forward to seeing you!
```

## Advanced Settings
- The markers for merge fields and group merge fields can be adjusted via the values `MERGE_FIELD_MARKER` and `GROUP_FIELD_MARKER`, respectively, in the sheet `Config`. You will need to be familiar with [the regular expressions of JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions).
- The index field marker for group merge `{{i}}` can also be modified through the value `ROW_INDEX_MARKER` in sheet `Config`.
- If HTML is enabled in your Gmail, make sure that your modified markers can still be detected in the HTML string.

## Acknowledgements
This work was inspired by [Tutorial: Simple Mail Merge (Google Apps Script Tutorial)](https://developers.google.com/apps-script/articles/mail_merge).

