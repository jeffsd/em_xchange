# Copyright Notice

[MIT License](https://choosealicense.com/licenses/mit/)

Copyright (c) 2021 [need to determine a citation for the working group].

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

# Introduction

Extensive datasets containing video and telemetry are collected onboard commercial fishing vessels. Recording typically occurs while the vessel is at-sea, with later transfer to a regulator and/or authorized third parties for review and analysis.

This document defines a data interchange format suitable for: (a) primary collection of such datasets; and, (b) translation of manufacturer specific formats into a standard DATASET format to facilitate interoperability with fisheries information systems, support academic research, and provide a suitable format for long-term archival storage.

Several widely used conventions are adopted, such that the translation process:
* Does not require detailed processing of the majority of data (video and images);
* Sensor observations are retained in their original units-of-measurement; and,
* All data is represented in files having well understood data formats.

_NOTE: This interchange format DOES NOT recommend any particular form of data transmission, security, integrity or privacy mechanisms. Such decisions are left to the parties exchanging the DATASET._


## Interpretation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [[RFC2119]](https://tools.ietf.org/html/rfc2119) [[RFC8174]](https://tools.ietf.org/html/rfc8174) when, and only when, they appear in all capitals, as shown here.

## Country Codes

Country codes SHALL use the [ISO 3166-1](https://www.iso.org/obp/ui/#search) Alpha-3 format.

## File Types

All file formats used SHALL have their MIME Types described within:

* [[RFC2046]](https://tools.ietf.org/html/rfc2046) MIME Part Two: Media Types
* [[RFC6838]](https://tools.ietf.org/html/rfc6838) Media Type Specifications
* [List of Registered Media Types](https://www.iana.org/assignments/media-types/media-types.xhtml)
* [Common MIME Types & Extensions](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)

## Date & Time

With the exception of relative time intervals within MP4 containers, date and time representations SHALL follow:

* [ISO 8601-1:2019 Date & Time](https://www.iso.org/standard/70907.html)

In addition, Timestamps:
* SHALL use compact representation (no <code>-</code> or <code>:</code> separators)
* SHALL be expressed in the UTC timezone ("Z" suffix)
* SHALL contain four-digit year, month, day, hour, minute, and second
* MAY include precision to the millisecond level, where appropriate

In addition, Timeranges:
* SHALL NOT use the duration specifier option (no "P" notation)
* SHALL use the alternate interval designator "--" (for filename compatibility)
* SHALL be **inclusive** of the start timestamp
* SHALL be **exclusive** of the end timestamp

Example Timestamp:
* <code>20210203T040506Z</code> as 3 February, 2021 at 04:05:06 UTC.

Example Timerange:
* <code>20210201T000000Z--20210202T000000Z</code> representing a one-day duration.

## Accuracy of Timestamps

This interchange format distinguishes between two realtime timestamp references:

| Source | Definition |
| ------ | ---------- |
| <code>System</code> | The recording system's real-time-clock.<br>SHOULD be accurate to within: <code>&pm;1000 milliseconds</code>. <br> SHOULD be synchronized to a reliable time source: i.e. GNSS or [RFC5905](https://tools.ietf.org/html/rfc5905).
| <code>GNSS</code> | Timestamp derived from a Global Navigation Satellite Service (GNSS).<br>Note that this may not be continuously available due to gaps in satellite visibility. |

# Dataset

When in capitals, the word "DATASET" SHALL mean:
* A set of files formatted according to this data interchange format; and,
* Pertaining to a chronologically contiguous timerange.

### Example

A minimal example is presented for a two-hour recording of:
* Two video streams (hourly video segments)
* Two sensor streams (hourly sensor segments)
* Two image capture sequences (hourly images)


      20200101T000000Z--20200101T000000Z_image_1.jpeg
      20200101T000000Z--20200101T000000Z_image_2.jpeg
      20200101T000000Z--20200201T010000Z_log.csv
      20200101T000000Z--20200201T010000Z_sensors_1.csv  
      20200101T000000Z--20200201T010000Z_sensors_2.csv
      20200101T000000Z--20200201T010000Z_video_1.mp4  
      20200101T000000Z--20200201T010000Z_video_2.mp4
      20200101T010000Z--20200101T010000Z_image_1.jpeg  
      20200101T010000Z--20200101T010000Z_image_2.jpeg      
      20200101T010000Z--20200201T020000Z_log.csv  
      20200101T010000Z--20200201T020000Z_sensors_1.csv  
      20200101T010000Z--20200201T020000Z_sensors_2.csv
      20200101T010000Z--20200201T020000Z_video_1.mp4  
      20200101T010000Z--20200201T020000Z_video_2.mp4
      dataset.json

## Filename Convention

Each file SHALL be named according to the following rules:

For the DATASET configuration file:

> <code>\<type\>.\<extension\></code>

For all other file types:

> <code>\<timerange\>\_\<type\>\[_\<stream\>].\<extension\></code>

Where:
* <code>timerange</code> specifies the timerange
* <code>type</code> is a recognized file type
* <code>stream</code> is a sensor- or video- stream number (where applicable)
* <code>extension</code> is the file extension corresponding to the MIME Type

The stream designates a specific sub-source (such as a specific video camera):
* <code>dataset</code>: Not applicable
* <code>log</code>: A stream number SHALL NOT be present
* <code>video</code>: Camera number SHALL be present
* <code>image</code>: Camera number SHALL be present
* <code>sensor</code>: Sensor stream number SHALL be present

Valid Stream numbers are <code>1</code> through <code>16</code>.

Non-consecutive (sparse) allocation of stream identifiers SHALL be allowed.

### File Timeranges

The filename timerange SHALL reflect when observations could have been recorded to the file (e.g. telemetry observations or frames of video). This is true whether or not observations were actually recorded: that is, a video file with no frames of video, a sensor file with no telemetry observations, or a logfile with no entries are all valid files.

For example:

<code>20200101T000000Z--20200101T010000Z</code>

Represents a time range for all observations recorded during the period:
* <code>\>=</code> 1 January 2020 at 00:00:00 UTC (start inclusive)
* <code>\<&nbsp;</code>  1 January 2020 at 01:00:00 UTC (end exclusive)

Additional clarifications:

* This is referred to as "half-open", "[start, end)" or "java.time" convention.
* For contiguous recordings there MUST be an exact match between the end timestamp of the current segment and the start timestamp of the subsequent segment.
* By definition, timeranges MUST NOT overlap for a given <code>\<type\>[_\<stream\>]</code>.
* For <code>image</code> files the timerange SHALL represent a point-in-time. For consistency in the interpretation of filenames, this is indicated by setting the start and end timestamps identically.
* This interchange format does not specify any limits on the duration of a timerange. In common practice, files have been observed segmented from 1 to 1440 minutes.

### File Types

Each file type conveys a specific category of information:

| Type   | Extension | MIME Type | Presence | Purpose |
| -------| --------- | --------- | -------- | ------- |
| <code>dataset</code> | <code>.json</code> | application/json | REQUIRED | System, Vessel & Sensor Configuration Metadata |
| <code>log</code> | <code>.csv</code> | text/csv | REQUIRED | System Event Logs |
| <code>image</code> | <code>.jpeg</code> | image/jpeg | OPTIONAL | Still Images |
| <code>video</code> | <code>.mp4</code> | video/mp4 | OPTIONAL | Video Segments |
| <code>sensors</code> | <code>.csv</code> | text/csv | OPTIONAL | GNSS, Analog & Digital Sensor Observations|

_NOTE: A valid DATASET contains zero or more image, video and/or sensor files. This is to clarify that a DATASET may be exchanged purely to confirm the absence of certain types of information; i.e. that the system that was operational but not required to record due to speed thresholds, gear sensor status, or for any other reason(s)._

# File Formats

## Image files

Image files SHALL be valid [JPEG - ISO/IEC 10918:2103](https://www.iso.org/standard/75845.html) format.

Viewing software SHOULD support the following options and MAY support additional options:

| Category | Description |
| ---- | ---- |
| Formats | 8-bit (grayscale) or 24-bit (YCbCr) |
| Chroma Sampling | 4:2:0 |
| Color Space | sRGB |

## Video files

Video files SHALL be valid [MP4 - ISO/IEC 14496-14:2003](https://www.iso.org/obp/ui/#iso:std:iso-iec:14496:-14:en) format containers.

Each file SHALL contain one video stream. For clarity, multi-channel video or audio MUST NOT be present.

Viewing software SHOULD support the following options and MAY support additional options:

| Category | Description |
| ---- | ---- |
| Video Codecs |[H.264 - ISO/IEC 14496-10:2020](https://www.iso.org/standard/75400.html) <br>[H.265 - ISO/IEC 23008-2:2020](https://www.iso.org/standard/35424.html)<br>[H.266 - ISO/IEC 23090-3:2021](https://www.iso.org/standard/73022.html)
| H.264 Profiles | High Profile (8 bits, YCbCr, 4:2:0) |
| H.265 Profiles | Main Profile (8 bits, YCbCr, 4:2:0) |
| H.266 Profiles | Main Profile (8 bits, YCbCr, 4:2:0) |
| Chroma Sampling | 4:2:0 |
| Color Space | [Rec. 709](https://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.709-6-201506-I!!PDF-E.pdf) |

_NOTE: We draw attention to the possibility that the practice or implementation of these codecs may involve the use of a claimed Intellectual Property Right. The authors take no position concerning the evidence, validity or applicability of claimed Intellectual Property Rights. This statement does not constitute legal advice, and potential implementers are advised to obtain independent legal advice._

### Log files

System events SHALL be stored in valid [CSV - \[RFC4180\]](https://tools.ietf.org/html/rfc4180) format files.

The extended MIME Type is "<code>text/csv; charset=utf-8, header</code>", denoting:
* Use of UTF-8 character encoding per [[RFC3629]](https://tools.ietf.org/html/rfc3629)
* A Header Row is present: defining the name of each Column
* The Row Separator is: <code>\<CR\>\<LF\></code>
* The Column separator is: <code>,</code> (comma)

The Header Row SHALL define the following columns and MAY define additional columns:

| Column | Description |
| ------ | ----------- |
| <code>systemTimestamp</code> | The timestamp at which the event occurred |
| <code>event</code> | The type of event |
| <code>stream</code> | The stream number, where applicable (e.g. camera number) |
| <code>details</code> | Further details of the event |

* All <code>systemTimestamp</code> entries must be within the timerange indicated by the filename
* Expected <code>event</code> types are defined within the <code>dataset.json</code> metadata

### Sensors

Sensor observations SHALL be stored in valid [CSV - \[RFC4180\]](https://tools.ietf.org/html/rfc4180) format files.

The extended MIME Type is "<code>text/csv; charset=utf-8, header</code>", denoting:
* Use of UTF-8 character encoding per [[RFC3629]](https://tools.ietf.org/html/rfc3629)
* A Header Row is present: defining the name of each Column
* The Row Separator is: <code>\<CR\>\<LF\></code>
* The Column separator is: <code>,</code> (comma)

#### Header Row

The Header Row SHALL define one reserved column and MAY define additional  columns:

| Column | Description |
| ------ | ----------- |
| <code>systemTimestamp</code> | The timestamp at which the observation(s) occurred |
| <code>\<sensorColumn1\></code> | The first sensor value column name |
| ... | |
| <code>\<sensorColumnN\></code> | The last sensor value column name |

* All <code>systemTimestamp</code> entries must be within the timerange indicated by the filename
* The <code>sensorColumnX</code> interpretations are defined within the <code>dataset.json</code> metadata.

#### Data Rows

Each of the zero or more Data Rows SHALL contain observations of the group of sensor values at the specified observation time:
* The <code>systemTimestamp</code> SHALL be present to timestamp the Row.
* Any combination of sensor values MAY be present or absent in the Row.
* Absent (null) values SHALL be indicated by an empty Column within the Row.

#### Interpolation

There MAY be multiple sensor observation files recording in parallel (distinguished by stream suffix) having different observation timestamps. To relate sensor observations across streams, or to other timestamped sources such as video or images, the following method is defined:
* The <code>systemTimestamp</code> MUST be used to establish the temporal relationship, except in the case of GNSS location reports where the <code>gnssTimeOfFix</code> MUST be used instead.
* Linear interpolation SHALL be used to estimate the value of sensors between observations (i.e. a frame of video to the estimated GNSS location at that exact instant).

## Dataset Configuration File

The <code>dataset.json</code> file SHALL be valid [JSON - RFC8259](https://tools.ietf.org/html/rfc8259) (also: [ISO/IEC 21778](https://www.iso.org/standard/71616.html)).

_NOTE: One (and only one) dataset configuration file SHALL be present. If any content changes, e.g. a system replacement or update to the vessel name, this requires a new DATASET. This is by design, to make clear which data elements SHALL remain static for the entire duration of a DATASET._

The DATASET configuration file SHALL contain the following JSON values and objects:

```json
{
  "start": "<Start ISO-8601 timestamp>",
  "end": "<End ISO-8601 timestamp>",
  "system": {},
  "vessel": {},
  "events": {},
  "sensors": [],
}
```

* The DATASET timerange SHALL be from <code>start</code> (inclusive) to <code>end</code> (exclusive)
* All file timestamps SHALL be entirely contained within the DATASET timerange

### System

The following OPTIONAL values are defined for the <code>system</code> object to identify the recording system:

```json
{
  "manufacturer": "<System manufacturer>",
  "model": "<Model designation by manufacturer>",
  "serial": "<Serial number assigned by manufacturer>"
}
```

Additional manufacturer specific values MAY be present to further detail the recording system configuration.

### Vessel

The following OPTIONAL values are defined for the <code>vessel</code> object to identify the vessel:

```json
{
  "imo": "<IMO number>",
  "name": "<Vessel name as registered with competent authority>",
  "flag": "<Flag state of registration>",
  "ircs": "<Radio callsign assigned by flag state>",
  "uvi": "<Fisheries identifier assigned by competent fisheries authority>",
}
```

Additional values MAY be present to further detail the vessel and other relevant data elements, such as relevant fishing licenses or permits.

### Events

The <code>events</code> object assists in interpreting logs. The following <code>events</code> SHALL be present and used for the purpose shown:

```json
{
  "systemStartup": "Generated when the recording system completes startup",
  "systemShutdownPlanned": "Generated on planned shutdown",
  "systemShutdownUnplanned": "Generated on abnormal shutdown (e.g. power failure)",
  "videoRecordingStart": "Generated when recording starts (per video-stream)",
  "videoRecordingStop": "Generated on planned recording stop (per video-stream)",
  "videoRecordingError": "Generated on recording error (per video-stream)",
  "iamgeRecordingStart": "Generated when recording starts (per image-stream)",
  "imageRecordingStop": "Generated on planned recording stop (per image-stream)",
  "imageRecordingError": "Generated on recording error (per image-stream)",
  "sensorRecordingStart": "Generated when recording starts (per sensor-stream)",
  "sensorRecordingStop": "Generated on planned recording stop (per sensor-stream)",
  "sensorRecordingError": "Generated on recording error (per sensor-stream)"
}
```

Additional manufacturer defined events MAY also be present.

### Sensors

The <code>sensors</code> array contains a definition for each sensor:

```json
{
  "name": "<sensorName>",
  "stream": 1 ... 16,
  "columns": ["<sensorColumnPrimary>", "<sensorColumnBackup1>", ...],
  "uom": "<See Units-of-measurement table>",
  "purpose": "<Human readable description of sensor's purpose>",
  "range" : {
    "minimum": <number>,
    "maximum": <number>
  }
}
```

* The <code>name</code>, <code>stream</code>, and <code>columns</code> are REQUIRED
* It is RECOMMENDED that a <code>uom</code> and <code>purpose</code> be provided
* The OPTIONAL <code>range</code> provides the expected range of sensor observations; e.g.  -90 to +90 for <code>gnssLatitude</code>
* One value is REQUIRED in the columns array, referencing a CSV Column. If the sensor also has backup(s), then the primary SHALL be listed first, followed by backup(s) in order of priority.

The following sensor names SHALL be reserved and used only for the purpose shown:

| Name | Format | Range | Purpose |
| ---- | ------ | ----- | ------- |
| <code>gnssTimeOfFix</code> | <code>YYYYMMDDTHHMMSS[.sss]Z</code> | Per ISO-8601 | Timestamp of GNSS location fix|
| <code>gnssLatitude</code> | <code>Decimal</code> | -90.0 to 90.0 |  WGS84 Latitude (-S, +N) |
| <code>gnssLongitude</code> | <code>Decimal</code> | -180.0 to 180.0 |  WGS84 Longitude (-E, +W) |
| <code>gnssAltitude</code> | <code>Decimal</code> | |  WGS84 Altitude above sea-level |
| <code>gnssSOG</code> | <code>Decimal</code> | |  Speed-over-Ground |
| <code>gnssCOG</code> | <code>Decimal</code> | 0.0 to 360.0|  Course-over-Ground |
| <code>gnssHDOP</code> | <code>Decimal</code> | | Horizontal Dilution of Position |
| <code>gnssAccuracy</code> | <code>Decimal</code> | | Estimated accuracy of 2D location |
| <code>gnssSVs</code> | <code>Integer</code> | | Number of satellites used in fix |
| <code>gnssFixType</code> | <code>Integer</code> | 0 to 2 | 0=None, 1=Autonomous, 2=Differential |
| <code>gearWinchRotation</code> | <code>Decimal</code> | | -Deploying, +Retracting |
| <code>gearPowerHydraulic</code> | <code>Decimal</code> | | Hydraulic pressure to fishing gear |
| <code>gearPowerElectric</code> | <code>Decimal</code> | | Electrical current to fishing gear |

Additional sensors MAY be defined by the system manufacturer:

### Units of Measurement

It is RECOMMENDED that each sensor define a unit-of-measurement (UoM) from this list:
* For dimensionless items, we define generic pseudo-units
* These options were selected based on observed use by current systems
* These options are not intended to be exhaustive of valid SI or customary units

| Unit | Sensor UoM | S.I. UoM |
| ---- | ---------- | -------- |
| Dimensionless | <code>boolean</code> | n/a |
| Dimensionless | <code>integer</code> | n/a |
| Dimensionless | <code>number</code> | n/a |
| Dimensionless | <code>string</code> | n/a |
| Length | <code>metre</code> | metre |
| Length | <code>kilometre</code> | metre |
| Length | <code>feet</code> | metre |
| Length | <code>mile</code> | metre |
| Length | <code>nmi</code> | metre |
| Mass | <code>kilogram</code> | kilogram |
| Mass | <code>gram</code> | kilogram |
| Mass | <code>pound</code> | kilogram |
| Angle | <code>radian</code> | radian |
| Angle | <code>degree</code> | radian |
| Angle | <code>revolution</code> | radian |
| Temperature | <code>kelvin</code> | kelvin |
| Temperature | <code>celsius</code> | kelvin |
| Temperature | <code>fahrenheit</code> | kelvin |
| Current | <code>ampere</code> | ampere |
| Voltage | <code>volt</code> | volt |
| Power | <code>watt</code> | watt |
| Power | <code>kilowatt</code> | watt |
| Pressure | <code>pascal</code> | pascal |
| Pressure | <code>millibar</code> | pascal |
| Pressure | <code>kilopascal</code> | pascal |
| Pressure | <code>bar</code> | pascal |
| Pressure | <code>psi</code> | pascal |
| Speed | <code>m/s</code> | metres per second |
| Speed | <code>km/h</code> | metres per second |
| Speed | <code>mph</code> | metres per second |
| Speed | <code>knots</code> | metres per second |
