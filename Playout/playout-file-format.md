# Playout File Format

This document defines the format for files to be supplied by Media Owners to Playout. The first published version has been agreed
through consultation with the Playout Delivery Group and Playout Steering Committee.

## Version control

| **Version** | **Date** | **Updated by** | **Details** |
| ------------|----------|----------------|-------------|
| 1.0 | 12 Apr 2023 | Adwanted UK (Peter Howe) | First published specification. |
| 0.2 | 06 Mar 2023 | Adwanted UK (Peter Howe) | Amended following discussions. Used for  final review before publishing. |
| 0.1 | 21 Feb 2023 | Adwanted UK (Peter Howe) | Initial version used for discussion with Delivery Group and Steering Group. |


## File specification

1.  Files must be in CSV format.

2.  For each Media Owner, their filenames must be unique. The file
    naming convention is up to each Media Owner to decide.

3.  The files must be UTF-8 encoded.

4.  There is no header record. The first record begins on the first
    line. No empty lines are permitted and there is no provision for
    'comments' within the file.

5.  Each record (including the final record in the file) must be
    terminated with a Unix-style line termination (LF 0x0A).

6.  Each record must have a fixed number of fields, as defined later in
    this document. Optional fields can be left empty or else must adhere
    to the defined field format.

7.  Each field of type "TEXT" may optionally be delimited by
    double-quotes (0x22) -- necessary if the value includes a comma.
    When delimited like this and a double-quote exists within the value
    of the text, the double-quote must be escaped with an additional
    double-quote. For example: `"some text ""in quotes"" more text"`

8.  Each field must not include space padding at the beginning or end.


## Record definition

Each record (each line of the file) reflects all the data associated
with a single playout on a single frame. All reporting is at the frame
level (e.g. linked assets are not represented, and players which play to
multiple screens are reported at frame level).

Mandatory fields are marked with an asterisk `*` in the following specification.
Some fields *must* be populated under certain circumstances, as described in the *Description* column below.
Such fields are marked with a `?` below.

| Field name | Type | Format | Description |
|---|---|---|---|
| **`* `frame_id**	| INT4 | SPACE Frame ID | The Frame ID held in SPACE. |
| **player_mac_address** | TEXT	| Length: 12 characters (i.e. with conventional colons/hyphens omitted) | The MAC Address of the network interface used by the player. |
| *See notes opposite<br>relating to the time<br>fields below* | | | *It is mandatory to provide either **spot_start**/**spot_end**, **spot_start**/**spot_length** or **spot_end**/**spot_length**.<br>If all three fields are provided, **spot_end** will be ignored.<br>When providing **spot_start** or **spot_end**, it is mandatory to provide the associated time zone (**_tz**) value.* |
| **`? `spot_start_utc** | TIMESTAMP | *yyyy*-*mm*-*dd*T*hh*:*mm*:*ss*.*sss* | The UTC start time of the spot. |
| **`? `spot_start_tz** | TZ_OFFSET | ±*hh*:*mm*<br>Z | Time zone offset for the spot start time (e.g. +01:00 for BST; Z or :00:00 can be used for GMT.) |
| **`? `spot_end_utc** | TIMESTAMP | *yyyy*-*mm*-*dd*T*hh*:*mm*:*ss*.*sss* | The UTC end time of the spot. |
| **`? `spot_end_tz** | TZ_OFFSET | ±*hh*:*mm*<br>Z | Time zone offset for the spot end time. (e.g. +01:00 for BST; Z or :00:00 can be used for GMT.) |
| **`? `spot_length** | INT8 | Max value 9223372036854775807 | Spot Length in milliseconds. |
| **`? `share_of_time** | DECIMAL(5,2) | Percentage to max 2 decimal places (no % sign). | Share of time. This must be provided for scrollers, but its presence is not policed by Playout. Media Owners must determine when this must be supplied. |
| **`* `order_id**	| TEXT | Max length 36 characters. | Media Owner-defined order identifier. |
| **`? `line_id** | TEXT | Max length 36 characters. | The identifier for the order line that this playout belongs to.<br>This is mandatory for OpenDirect trades but its presence is not policed by Playout. Media Owners must determine when this must be supplied. |
| **`* `space_buyer_id** | INT4 | Max value 2147483647 | SPACE-defined ID for the buyer (often a Specialist). This can be a SPACE agency_id or client_id. (Note that these IDs do not overlap in SPACE.) |
| **space_agency_id** | INT4 | Max value 2147483647	| SPACE-defined ID for the agency. If the Agency is the same as the Buyer, this can field should still be populated to aid reporting. |
| **`* `space_brand_id** | INT4 | Max value 2147483647 | SPACE-defined Brand ID. |
| **buyer_campaign_ref** | TEXT | Max length 64 characters | Buyer-defined campaign reference (could be an Order ID, for example). |
| **`* `creative_id** | TEXT | Max length 64 characters | Media Owner-defined creative identifier. |
| **`* `creative_name** | TEXT | Max length 128 characters | Creative title, to assist with reporting. Usually a filename. |
| **third_party_creative_ref** | TEXT | Max length 128 characters | Creative reference provided by a third-party such as the creative agency. This might be a URL or a GUID, for example. |
| **creative_trigger_event** | TEXT | Max length 64 characters | The creative trigger event, indicating what prompted the given creative to be used. |
| **media_owner_playout_ref** | TEXT | Max length 36 characters | Media Owner-defined reference representing this record. For the Media Owner, this should uniquely identify this record. (Note that records supplied by other Media Owners may happen to use the same ID however.) |

### Record augmentation

Note that Playout will record the SPACE Media Owner ID against each
ingested playout. It will be determined from the S3 folder into which
the file is dropped.

The date/time of processing a record will be included in the data stored
in the data warehouse.
