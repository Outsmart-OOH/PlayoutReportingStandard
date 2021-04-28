![OutsmartLogo](Pictures/OutsmartLogo.png)

Out-Of-Home Playout Reporting Standard v2.0 - April 2021

## Version Control

| **Version** | **Author** | **Date** | **Description** |
| --- | --- | --- | ---  |
| 1.0| Anon | July 2018 | Out-Of-Home Playout Reporting Standard |
| 2.0 | Tim Harvey | April 2021 | Out-Of-Home Playout Reporting Standard + JSON Implementation |

## Contributors

**Clear Channel** Ben Price, Jamie Mills, Victor Porter, Karen Fornos Klein

**Global** Dan Sharp, Luke Howard, Matt Allard, Rob Brayshaw

**JCDecaux** Dom Kozak, Philippa Kings, Rebecca Lee

**Key Systems** Jon Axworthy, Ross Lafar

**Kinetic** Alex Berry, Georgina Monet, Prasaant Patel

**Mediacom** Nate Barker

**Ocean Outdoor** Angela Green, Doug Swan, Luka Djukic

**Posterscope** Daniel Conway, Steve Pavett, Gavin Lee

**Rapport** Paul Sambrook, Ross Wilson, Gail Williams

**Talon Outdoor** Anant East

**Knitting Media** Tim Harvey


# Contents

[1. About Outsmart](#1-about-outsmart)

[2. About OOH Industry Standards Committee](#2-about-ooh-industry-standards-committee)

[3. Introduction](#3-introduction)

[4. Asset Defintitions](#4-asset-definitions)

[5. Reporting Levels](#5-reporting-levels)

[6. The "Summary Report" Data Standard](#6-the-summary-report-data-standard)

[7. The “Playout Report” Data Standard](#7-the-playout-report-data-standard)

[8. Example Reports](#8-Example-Reports)

[9. JSON Schema](#9-JSON-Schema)

[10. JSON Filename](#10-JSON-Filename)

[11. Report Publishing](#11-Report-Publishing)


# 1. About Outsmart

Outsmart is the UK trade body for the Out of Home advertising industry. Our members include the biggest
sellers of OOH advertising in the UK including Clear Channel, Global, JCDecaux and Ocean Outdoor.

# 2. About OOH Industry Standards Committee

The OOH Standards Committee has representation from both Outsmart and IPAO. The IPAO represents the
biggest buyers of Out of Home advertising. The committee consults, develops and agrees upon standards which
are required to improve the efficiency and effectiveness of the operation of Out of Home Buying and Selling

# 3. Introduction

The agreed Reporting standard impacts any business that sends, receives or processes OOH reports. This
includes Media Owners, Specialists, Agencies and technology providers.

The document refers to a set of 6 _Reporting Levels_ that share the granularity and accountability of the type of
reporting that can be produced by a company. – A further update to this document is expected to cover Level 6
in more detail in the future.

The final part of this document sets out the expected _Data Standards_ of how this data should be provided, for
those companies sending or receiving this data between them.

The following document is a guideline that covers the minimum detail required and future revisions to this
document may add additional requirements.


# 4. Asset Definitions

SPACE is the industry database of OOH advertising assets.

The structure and scope of the database is defined by the OOH Standards Committee.

Every OOH asset held within the database is ascribed a unique reference number (Frame ID).

The characteristics of each asset are held within the database and subject to the agreed definitions.


# 5. Reporting Levels

This table sets out the attributes that differentiate the types of standard report.

It is reccomended that Media Owners and Publishers implement Level 6 to standardise the transmission of the most granular and frequent as detailed in the [JSON Schema](#9-JSON-Schema)

![ReportingLevels](Pictures/Levels.png)


# 6. The “Summary Report” Data Standard

This is the standard for day/hour specific reporting that can be shared with customers to report on order-
specific campaign delivery.

The attached examples assume that they are accompanied by an Order ID that references that specific
campaign.

![SummaryReport](Pictures/Summary.png)

# 7. The “Playout Report” Data Standard

This is the standard for reporting on the display of campaigns that can be shared with customers if more
granular detail is required.

![PlayoutReport](Pictures/Standard.png)

# 8. Example Reports

## Level 1 – Summary Report Example (Classic OOH)

![Level1Classic](Pictures/Level1Classic.png)

## Level 1 – Summary Report Example (DOOH)

![Level1DOOH](Pictures/Level1DOOH.png)

## Level 2 - Summary Report Example (Classic)

![Level2Classic](Pictures/Level2Classic.png)

## Level 2 - Summary Report Example (DOOH)

![Level2DOOH](Pictures/Level2DOOH.png)

## Level 3/4 - Summary Report Example (Classic)

![Level3Classic](Pictures/Level3Classic.png)

## Level 3/4 - Summary Report Example (DOOH)

![Level3DOOH](Pictures/Level3DOOH.png)

## Level 5/6 Playout Example (Classic)

![Level6Classic](Pictures/Level6Classic.png)

## Level 5/6 Playout Example (DOOH)

![Level6DOOH](Pictures/Level6DOOH.png)

# 9. JSON Schema
The JSON schema is as detailed in Stats OOH Reporting object in [OpenDirect OOH 1.5.1](https://github.com/Outsmart-OOH/ooh_open_direct/blob/master/docs/v1-1/OpenDirect_OOH_1-5-1_v1-1.md)

## Granularity
The report is delivered at the Spot level of granularity (Outsmart Level 5/6 Reporting)

## Classic Reporting Example
```json
{
    "AccountId": "23873345",
    "OrderId": "3479",
    "LineId": "7",
    "ReportPublishTime": "2020-12-20T00:00:00.000Z",
    "ReportStartTime": "2020-12-09T00:00:00.000Z",
    "ReportEndTime": "2020-12-10T01:00:00.000Z",
    "Report": [
        {
            "StartTime": "2020-12-09T00:00:00Z",
            "EndTime":"2020-12-10T00:00:00Z",
            "FrameID":"1234159856",
            "CreativeID":"adfgj123",
            "ShareOfTime":"100%"
        },
        {
            "StartTime": "2020-12-09T00:00:00Z",
            "EndTime":"2020-12-10T00:00:00Z",
            "FrameID":"1234159857",
            "CreativeID":"adfgj123",
            "ShareOfTime":"100%"
        }
    ]
}
```
## Digital Reporting Example
```json
{
    "AccountId": "23873345",
    "OrderId": "3479",
    "LineId": "8",
    "ReportPublishTime": "2020-12-20T00:00:00.000Z",
    "ReportStartTime": "2020-12-09T00:00:00.000Z",
    "ReportEndTime": "2020-12-10T01:00:00.000Z",
    "Report": [ 
        {
            "StartTime": "2020-12-09T00:00:00Z",
            "EndTime":"2020-12-09T00:00:10Z",
            "FrameID":"1234158956",
            "CreativeID":"capad653",
            "ShareOfTime":"16.66%"
        },
        {
            "StartTime": "2020-12-09T00:02:00Z",
            "EndTime":"2020-12-09T00:02:10Z",
            "FrameID":"1234158956",
            "CreativeID":"capad653",
            "ShareOfTime":"16.66%"
        },
        {
            "StartTime": "2020-12-09T00:03:00Z",
            "EndTime":"2020-12-09T00:03:10Z",
            "FrameID":"1234158956",
            "CreativeID":"capad653",
            "ShareOfTime":"16.66%"
        },
        {
            "StartTime": "2020-12-09T00:04:00Z",
            "EndTime":"2020-12-09T00:04:10Z",
            "FrameID":"1234158956",
            "CreativeID":"capad653",
            "ShareOfTime":"16.66%"
        },
        {
            "StartTime": "etc",
            "EndTime":"etc",
            "FrameID":"etc",
            "CreativeID":"etc",
            "ShareOfTime":"etc"
        }
    ]
}
```

# 10. JSON Filename

The structure of the name of the JSON reporting file is as follows:

*AccountId_OrderId_LineId_StartTime_EndTime_PublishTime.json*
  
Where:
* AccountId = The Account ID of the campaign buyer
* OrderId = The Order ID of the campaign order
* LineId = The Order Line ID of the playout report 
* StartTime = The start time of the data written in the playout report
* EndTime = The end time of the data writted in the playout report 
* PublishTime = The time when the playout report file was written 

All time fields must be recorded in ISO-8601 format to Coordinated Universal Time (UTC)
The ':' is ommitted in the hour, minutes and seconds description as it is not recognised in filenames on common computer operating systems.
e.g. yyyy-mm-ddThhmmssZ

Example Filename:

*ACCT1234_OD54321_LN54321-1_2021-11-01T000000Z_2021-11-08T173000Z_2021-11-09T030000Z.json*

# 11. Report Publishing
The inital reccomendation is that the playout reports are written to a secure shared folder, ftp location or bucket that is only made available to the relevant client account user(s)
The frequency of writing the playout reporting files is at the Media Owner / Publisher's discretion.




