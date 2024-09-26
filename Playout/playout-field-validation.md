# Playout Field Validation

This document describes the validation process undertaken when processing Playout records uploaded by Media Owners.

##Version Control

| **Version** | **Date**    | **Updated by**               | **Details**                                                                                           |
|-------------|-------------|------------------------------|-------------------------------------------------------------------------------------------------------|
| 2.0         | 18 Sep 2024 | Susie Campbell (Adwanted UK) | Updated with further agreed classic amends and cosmetic changes<br><br>First published specification. |
| 1.2         | 20 Nov 2023 | Peter Howe (Adwanted UK)     | Cosmetic changes.                                                                                     |
| 1.1         | 5 Jul 2023  | Peter Howe (Adwanted UK)     | player_mac_address replaced with more flexible player_ref and no longer needs bespoke validation.     |
| 1.0         | 19 Jun 2023 | Peter Howe (Adwanted UK)     | Published version with outstanding questions answered.                                                |
| 0.2         | 06 Apr 2023 | Peter Howe (Adwanted UK)     | Updated after review with Delivery Group and Steering Group.                                          |
| 0.1         | 29 Mar 2023 | Peter Howe (Adwanted UK)     | Initial version.                                                                                      |

**Record-level validation:** Validation is at a record level, not a file level. Successfully validated records are inserted to the warehouse. An exception report for Media Owners will detail what has failed and why.

**Ignore resubmitted successfully processed records:** Any records exactly matching already (successfully) processed playouts will be silently ignored by Playout and no exception produced in this scenario.

## Validations

1. **Frame type rules**

Playout uses the Frame ID to look up the Frame Type held in SPACE. The Frame Type determines whether the frame is Digital or Classic and therefore which validation rules to apply. See the [File Formats](https://github.com/Outsmart-OOH/PlayoutReportingStandard/tree/main/Playout) for valid fields and their mandatory/optional settings

Media Owners provide one record for a Digital playout. Media Owners may provide two records for a Classic posting (an ‘up’ record and a ‘down’ record, or just a ‘down’ record).

For Classic frames neither mandatory nor optional fields supplied in the ‘up’ record can be amended in the ‘down’, with the exception of specific ‘Brand not provided at point of trading’ brandid entries. Optional fields not populated in the ‘up’ record, but populated in the ‘down’ record, will be accepted. Once a record with an enddate has been submitted, it cannot be amended.

2. **Mandatory field rules:**

For fields marked with an asterisk (\*) in the File Format, a value must be populated.

3 **Date/time rules:**

When providing **_startdate_** and/or **_enddate_,** it is mandatory to provide the associated time zone (**_utcoffset_**) value.

**Digital** - it is mandatory to provide either:

- **_startdate_**/**_enddate_, or**
- **_startdate_**/**_spotlength_, or**
- **_enddate_**/**_spotlength_**

Playout will store all fields (startdate/enddate/spotlength) calculating the missing one as necessary. If all three fields are provided, enddate will be ignored and recalculated.

Records with a startdate (provided or calculated) older than 14 days will be rejected.

**Classic** – it is mandatory to provide:

- **_startdate_** in the ‘up’ record
- startdate and enddate in the ‘down’ record.

NB spotlength is not present in the classic file format.

‘Up’ records with a startdate older than 14 days will be rejected.

‘Down’ records with an enddate older than 14 days will be rejected.

‘Up’ records received for a Frame ID where no corresponding ‘down’ record, matched on the mediaownerplayoutref, has been received (i.e. there is no enddate for a posting) will flag a ‘Warning’ to the Media Owner. Classic frames identified as Rotating/Scrolling are exempt from this requirement.

4. **Field type rules:**

| **Field type** | **Rule** |
| --- | --- |
| INT4 | Must be a non-negative integer with a maximum value of 2147483647 |
| INT8 | Must be a non-negative integer with a maximum value of 9223372036854775807 |
| TIMESTAMP | Must adhere to this format (with hyphens, T, colons and full-stops as shown):<br><br>_yyyy_\-_mm_\-_dd_T_hh_:_mm_:_ss.sss_<br><br>_yyyy_, _mm_, _dd_, _hh_, _mm_, _ss_, _sss_ must adhere to permitted values as per ISO8601 |
| TZ_OFFSET | _Either:_<br><br>Must begin with a + or – followed by a time adhering to this format: _hh_:_mm_ (including the colon). _hh_ and _mm_ must adhere to permitted values as per ISO8601.<br><br>_or:_<br><br>Must be the single character Z |
| DECIMAL(5,2) | Must be a number with a maximum of two decimal places. If the decimal point is present, then at least one decimal place must be provided. The number must not exceed 100.00 |
| TEXT(_n_) | May contain zero or more UTF-8 characters. The value may optionally be enclosed in double-quotes (UTF-8 0x22) and, if so, the value may include escaped double-quote characters (two double-quotes).<br><br>The maximum length of the value, excluding enclosing double-quotes and excluding any double-quote escape characters, must not exceed _n_ |

5. **Field-specific rules:**

| **Field name** | **Rule** |
| --- | --- |
| frameid | Must represent a valid ID held in SPACE.<br><br>Must be exactly 10 digits to adhere with the SPACE standard.<br><br>The frame should belong the Media Owner (on the date of the playout – with a 7-day tolerance). |
| startdate/startutcoffset and enddate/endutcoffset | Must not be in the future.<br><br>End date/time must not be before start date/time. |
| shareoftime | Must not exceed 100.00 |
| spacebuyerid | Must be a current SPACE-defined ID for the buyer, either a SPACE Agency ID or Client ID. (Note that these IDs do not overlap in SPACE.) |
| spaceagencyid | Must be a current SPACE-defined ID for the agency. |
| spacebrandid | Must be a current SPACE-defined Brand ID |

6. **Overlapping play rule:**

For a given playout, the start/end period for the given Frame ID must not overlap with the period of an existing playout in the data warehouse for that frame.

A 1.5 second tolerance exists with this overlap checking.

It is assumed that the play which is already in the Playout database is correct (with subsequent overlapping plays being rejected.)