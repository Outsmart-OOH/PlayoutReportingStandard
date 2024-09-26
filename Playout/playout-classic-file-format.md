#Playout Classic File Format

This document defines the format for files for Classic frame records to be supplied by Media Owners to Playout. The first published version has been agreed
through consultation with the Playout Delivery Group and Playout Steering Committee.

# Version control

| **Version** | **Date**     | **Updated by**               | **Details**                                                                    |
|-------------|--------------|------------------------------|--------------------------------------------------------------------------------|
| 1.0         | 25 Sept 2024 | Adwanted UK (Susie Campbell) | First published specification.                                                 |
| 0.5         | 17 Sept 2024 | Adwanted UK (Susie Campbell) | Change to protocol to clarify up/down and rules, and updated SOT description.  |
| 0.4         | 6 Aug 2024   | Adwanted UK (Peter Howe)     | Change to protocol in which both provided records are retained in the database.|
| 0.3         | 20 Jun 2024  | Adwanted UK (Susie Campbell) | Amended the o/s note re shareoftime to clarify.                                |
| 0.2         | 6 Mar 2024   | Adwanted UK (Susie Campbell) | Amended following discussions.                                                 |
| 0.1         | 27 Feb 2024  | Adwanted UK (Susie Campbell) | Initial version.                                                               |

Classic Frames will be stored in the data warehouse in the same format to Digital Frames but in separate databases from Digital Frames. That is, there will be two databases per each environment (Dev/UAT/Production).

## Classic Frame File specification

As per Digital Frames the file format used by Media Owners for Classic Frames should adhere to following specification:

1. Files must be in CSV format.

2. For each Media Owner, their filenames must be unique. The file naming convention is up to each Media Owner to decide.

3. The files must be UTF-8 encoded.

4. There is no header record. The first record begins on the first line. No empty lines are permitted and there is no provision for 'comments' within the file.

5. Each record (including the final record in the file) must be terminated with a Unix-style line termination (LF 0x0A).

6. Each record must have a fixed number of fields, as defined later in this document. Optional fields can be left empty or else must adhere to the defined field format.

7. Each field of type "TEXT" may optionally be delimited by double-quotes (0x22) -- necessary if the value includes a comma. When delimited like this and a double-quote exists within the value of the text, the double-quote must be escaped with an additional double-quote. For example: \`"some text ""in quotes"" more text"\`

8. Each field must not include space padding at the beginning or end.

## Protocol

Media Owners will upload two records to Playout: one when the creative is posted (an ‘up’ record), and a further one when the creative is removed (a ‘down’ record), to provide ‘enddate’. Both records are identified by the same ‘mediaownerplayoutref’.

The Playout classic database will keep both records in the database (one without ‘enddate’, one with) so there is an audit trail of the data supply. Data will be validated between the first and the second record. Media Owners will be able to improve data quality with rules as follows:

- Neither mandatory nor optional fields supplied in the ‘up’ record can be amended in the ‘down’, with the exception of the specific ‘Brand not provided at point of trade’ brandids
- Optional fields not populated in the ‘up’ record, but populated in the ‘down’ record, will be accepted.
- Once a record with an ‘enddate’ has been submitted, it cannot be amended.

Media Owners may also submit a single record with a unique ‘mediaownerplayoutref’ to Playout containing both a ‘startdate’ and an ‘enddate’ once a creative is removed.

For scrollers, Media Owners may post several 'up'/'down' records e.g. should a campaign go on for more than 2 weeks there may be several ‘up’ and ‘down’ records for a campaign duration as the number of creatives (and therefore ‘shareoftime’) may change over that period.

N.B. An 'up' record will be a row, a 'down' will be a further row and both will be exported via S3. There is also the possibility that just a 'down' is received/reported.

## Record definition

Each record (each line of the file) reflects all the data associated with a single playout on a single frame. All reporting is at the frame level (e.g. linked assets are not represented).

Mandatory fields are marked with an asterisk ‘\*’ in the following specification.

Some fields \*must\* be populated under certain circumstances, as described in the \*Description\* column below. Such fields are marked with a ‘?’ below.

| **Field name** | **Type** | **Format** | **Description** |
| --- | --- | --- | --- |
| \*frameid | INT4 | SPACE Frame ID | The Frame ID held in SPACE. |
| **\*startdate** | TIMESTAMP | _yyyy_\-_mm_\-_dd_T_hh_:_mm_:_ss_._sss_ | The UTC start time of the display. |
| **\*startutcoffset** | TZ_OFFSET | ±hh:mm Z | Time zone offset for the display start time (e.g. +01:00 for BST; Z or +00:00 can be used for GMT. |
| **?enddate** | TIMESTAMP | _yyyy_\-_mm_\-_dd_T_hh_:_mm_:_ss_._sss_ | The UTC end time of the display. This may not be in the future.<br><br>Media Owners must leave this field blank when the creative is posted and not yet removed. |
| ?endutcoffset | TZ_OFFSET | ±hh:mm Z | Time zone offset for the display end time (e.g. +01:00 for BST; Z or :00:00 can be used for GMT.)<br><br>Media Owners must leave this field blank when the creative is posted and not yet removed. |
| shareoftime | DECIMAL (5,2) | Percentage to max 2 decimal places (no % sign). | The SOT a creative had, or is known will have |
| \*orderid | TEXT | Max length 36 characters. | Media Owner-defined order identifier. |
| ?lineid | TEXT | Max length 36 characters. | The identifier for the order line that this playout belongs to. This is mandatory for OpenDirect trades, but its presence is not policed by Playout. Media Owners must determine when this must be supplied. |
| \*spacebuyerid | INT4 | Max value 2147483647 | SPACE-defined ID for the buyer (often a Specialist). This can be a SPACE Agency ID or Client ID. (Note that these IDs do not overlap in SPACE.) |
| spaceagencyid | INT4 | Max value 2147483647 | SPACE-defined ID for the agency. If the Agency is the same as the Buyer, this field should still be populated to aid reporting. |
| \*spacebrandid | INT4 | Max value 2147483647 | SPACE-defined Brand ID. |
| buyercampaignref | INT4 | INT4 | Max length 64 characters \| Buyer-defined campaign reference (could be an Order ID, for example). |
| \*creativeid | TEXT | Max length 64 characters | Media Owner-defined creative identifier. |
| \*creativename | TEXT | Max length 128 characters | Creative title, to assist with reporting. Usually a filename. |
| thirdpartycreativeref | TEXT | Max length 128 characters | Creative reference provided by a third-party such as the creative agency. This might be a URL or a GUID, for example. |
| \*mediaownerplayoutref | TEXT | Max length 48 characters | Media Owner-defined reference representing this record. For the Media Owner, this should uniquely identify this record across all time. (Note that records supplied by other Media Owners may happen to use the same ID however.) |

NB this differs from Digital as ‘enddate’, ‘endutcoffset’ and ‘shareoftime’ are optional, and ‘spotlength’, ‘creativetriggerevent’ and ‘playerref’ are not present.

## Supply

Note that the existing S3 bucket and folder already used for Digital play data will also be used for supplying Classic data files.

## Record augmentation

Note that Playout will record the SPACE Media Owner ID against each ingested playout. It will be determined from the S3 folder into which the file is dropped.

The date/time of ingesting a record (‘inserttime’), and an ID unique to each file processed (‘jobid’) will be included in the data stored in the data warehouse.

## Second record: provision of enddate/endutctime

Once creative is removed from a classic frame, the Media Owner must provide the record again, but this time with the ‘enddate’. This second record must use the same ‘mediaownerplayoutref’ as the initial record.

This will be added to the Playout classic database as a second record, with its own ‘inserttime’ and ‘jobid’ (but the same ‘mediaownerplayoutref’).