# SBT-DF205 Case 2 – iOS Forensics Investigation

## Overview

This repository contains my forensic investigation for **SBT-DF205 Mobile and IoT Forensics – Case 2**, focused on an **iPhone SE running iOS 13.4.1**.

The purpose of the investigation was to examine the supplied iOS evidence, identify the device and associated accounts, recover communication records, review browsing and calendar activity, analyse media and location artefacts, examine KnowledgeC and Health data, and build a supported timeline of relevant activity.

All findings were based on the supplied evidence package and were validated using forensic tools on Kali Linux.

---

## Case Reference

**Case ID:** SBT-DF205-C2  
**Module:** SBT-DF205 Mobile and IoT Forensics  
**Case:** iOS Forensics – iPhone SE iOS 13.4.1  
**Student:** Athanasius Alekwe  
**Student ID:** 2025/FWSD/11230  

---

## Investigation Objectives

The main objectives of this investigation were to:

- Preserve and verify the integrity of the source evidence.
- Identify the iPhone model, iOS version, and build information.
- Examine stored account information and relevant device settings.
- Recover SMS and iMessage records and reconstruct communication activity.
- Associate message and call identifiers with stored contacts where supported.
- Analyse call history records.
- Review Safari browsing and calendar artefacts.
- Examine photo and media records and validate database entries against actual files.
- Analyse KnowledgeC activity records.
- Review Health data sources and activity samples.
- Examine location-related artefacts without overstating what the records prove.
- Create a UTC-normalised forensic timeline using multiple artefact families.

---

## Laboratory Environment

The investigation was carried out in a Kali Linux virtual machine using VMware Workstation.

### Tools Used

- Kali Linux
- SQLite3
- Python 3
- plistlib
- SHA-256 hashing tools
- GNU tar
- unzip
- ExifTool
- `file`
- `stat`
- `find`
- `grep`
- standard Linux command-line utilities

---

## Evidence Preparation

The supplied `ios_13_4_1.zip` archive was preserved and hashed before analysis.

The required iOS filesystem archive:

`Extraction/13-4-1.tar`

was extracted into a dedicated working directory.

The original source evidence was kept separate from the working copy, and database sidecar files such as:

- `-wal`
- `-shm`

were preserved where present.

An evidence register and handling log were maintained throughout the investigation.

---

## Investigation Summary

### Task 1 – Evidence Preservation

The original ZIP and TAR archives were identified, preserved, and hashed using SHA-256.

A structured investigation workspace was created with separate directories for:

- source evidence
- working copies
- evidence
- exports
- screenshots
- logs

Extraction warnings and permission-related issues were recorded in the handling log rather than ignored.

---

### Task 2 – Device, Accounts and Settings

System artefacts were examined to identify the device configuration.

The investigation confirmed an iPhone running **iOS 13.4.1**.

`Accounts3.sqlite` and related Apple account artefacts were examined to identify stored account associations.

Location Services and backup-related configuration artefacts were also reviewed.

These records were treated as stored configuration and not automatically as proof of user activity.

---

### Task 3 – Messages, Contacts and Calls

The following SMS database was examined:

`private/var/mobile/Library/SMS/sms.db`

The database contained **48 message records**.

Messages were analysed for:

- participant
- content
- direction
- service type
- raw timestamp
- UTC timestamp

A two-way conversation was reconstructed from the recovered message records.

The investigation also traced a message attachment to its local file:

`iOS_Bug_Reporting_for_Forensic_Purposes_1.2.pdf`

The attachment was validated using file metadata and SHA-256 hashing.

`AddressBook.sqlitedb` associated the communication identifiers with the stored contact:

**Josh Hickman**

including:

- `joshua.hickman1@me.com`
- `(919) 579-0479`

Call records were recovered from:

`CallHistory.storedata`

and examined for direction, timestamp, duration, location information, and service.

---

### Task 4 – Safari and Calendar

Safari browsing history was recovered from:

`History.db`

Multiple recorded visits were identified, including Google searches and MLB-related browsing activity.

Raw Apple timestamps were converted to UTC.

Calendar artefacts were examined from:

`Calendar.sqlitedb`

The database contained records in tables including:

- `Calendar`
- `CalendarItem`
- `Location`
- `Recurrence`
- `OccurrenceCache`

Calendar records were reported only where supported by the database.

---

### Task 5 – Photos and Media

`Photos.sqlite` was examined, including the `ZGENERICASSET` table.

Media records such as:

- `IMG_0001.JPG`
- `IMG_0002.JPG`
- `IMG_0003.JPG`

were identified.

One database record was validated against its actual media file:

`IMG_0001.JPG`

The file was confirmed using:

- `file`
- `stat`
- SHA-256
- ExifTool

The photo metadata did not provide reliable GPS evidence for the selected records. Coordinates such as `-180.0` were treated as invalid rather than as real locations.

---

### Task 6 – KnowledgeC Activity

The database:

`knowledgeC.db`

was examined through the `ZOBJECT` table.

Relevant records included streams related to:

- Safari web usage
- application focus
- battery percentage
- device charging state
- display state
- activity level

A strong temporal correlation was identified between Safari activity in KnowledgeC and Safari history records on **28 March 2020**.

This supported that Safari was active during the recorded browsing period.

---

### Task 7 – Health and Location Evidence

Health artefacts were examined from:

- `healthdb.sqlite`
- `healthdb_secure.sqlite`

Recorded sources included an iPhone and Apple Watch-related entries.

These source records were not treated as proof that the phone holder personally performed every activity recorded by a wearable.

Location artefacts were examined from:

- `cache_encryptedB.db`
- `Local.sqlite`

`CellLocation` returned no useful entries during the examination.

Some Wi-Fi reference records contained `0.0, 0.0` coordinates and were not treated as evidence of physical presence.

`Local.sqlite` contained learned locations of interest with valid coordinates.

These were reported as inferred locations rather than confirmed visits because the learned-visit table did not provide corresponding visit records.

---

### Task 8 – Timeline and Correlation

A final UTC timeline was created using more than twelve events from multiple artefact families, including:

- Health
- call history
- Safari
- KnowledgeC
- SMS/iMessage

Cross-artifact correlations were used to compare records from different databases.

Examples included:

- Safari browsing activity matching KnowledgeC Safari activity.
- Stored contact information matching identifiers recovered from messages.
- Media database records matching actual files in the DCIM directory.

The investigation also considered alternative interpretations where evidence was incomplete or ambiguous.

---

## Key Findings

The investigation established that:

- The supplied filesystem contained artefacts consistent with an iPhone SE running iOS 13.4.1.
- Communication records linked stored identifiers with Josh Hickman.
- SMS and iMessage activity was recoverable from `sms.db`.
- A message attachment was successfully traced to its local PDF file.
- Call history records were recovered and normalised to UTC.
- Safari history showed recorded browsing activity.
- KnowledgeC independently supported Safari application activity during part of the same browsing period.
- Media records could be linked to corresponding files.
- Health and location databases contained useful evidence but required careful interpretation.

---

## Limitations

Several limitations were encountered during the investigation.

Some extracted filesystem directories retained restrictive permissions and required elevated access.

Some Apple artefacts contained values that could not safely be interpreted without additional context.

Certain location databases contained empty tables or invalid placeholder coordinates.

The presence of a stored account, Health source, learned location, or device configuration does not automatically prove who was using the device at a particular time.

Where evidence did not support a definite conclusion, it was reported as uncertain rather than inferred.

---

## Conclusion

The forensic examination successfully recovered and correlated evidence from several independent iOS artefact families.

Messages, contacts, calls, Safari history, media records, KnowledgeC activity, Health sources, and location-related records contributed to a consistent reconstruction of device activity.

The strongest findings were supported by more than one artefact source, particularly the correlation between Safari browsing records and KnowledgeC activity.

Where the evidence was incomplete, such as learned locations without matching visit records or Health data associated with synced devices, the findings were kept appropriately limited.

Overall, the evidence supports a structured and reproducible reconstruction of relevant activity while clearly separating verified findings from interpretation.

---

## Repository Contents

Typical supporting files include:

```text
Report.pdf
Evidence_Register.csv
Timeline.csv
Queries.sql
Handling_Log.csv
README.md
```

The original evidence archives and full extracted filesystem are not included in this repository.

---

## Evidence Integrity

Source evidence was handled separately from working copies.

SHA-256 values were calculated for key evidence files and selected extracted artefacts.

All analysis was performed on copied or extracted evidence rather than modifying the original source archive.

---

## Notes

This repository is for academic digital forensics work.

Sensitive credentials, passwords, authentication tokens, and unrelated personal information are intentionally excluded.

Only selected evidence necessary to support the forensic findings should be published.
