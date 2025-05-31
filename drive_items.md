---
layout: default
title: Drive Items
---

# Drive Items - Human-Friendly Guide

This document explains how to reference Google Drive items in GAMADV-XTD3 commands.

## Related Documentation
- [Basic Items](Basic-Items)
- [List Items](List-Items)

## Drive File References

### Drive File ID
A Drive File ID is simply a string of characters that uniquely identifies a file or folder in Google Drive.

### Drive File URLs
You can reference Drive files using any of these URL formats:
- `https://drive.google.com/open?id=FILE_ID`
- `https://drive.google.com/drive/files/FILE_ID`
- `https://drive.google.com/drive/folders/FILE_ID`
- `https://drive.google.com/drive/folders/FILE_ID?resourcekey=RESOURCE_KEY`
- `https://drive.google.com/file/d/FILE_ID/ADDITIONAL_INFO`
- `https://docs.google.com/document/d/FILE_ID/ADDITIONAL_INFO`
- `https://docs.google.com/drawings/d/FILE_ID/ADDITIONAL_INFO`
- `https://docs.google.com/forms/d/FILE_ID/ADDITIONAL_INFO`
- `https://docs.google.com/presentation/d/FILE_ID/ADDITIONAL_INFO`
- `https://docs.google.com/spreadsheets/d/FILE_ID/ADDITIONAL_INFO`

### Drive File Item
A Drive File Item can be either:
- A Drive File ID
- A Drive File URL (any of the formats above)

### Drive File Name
A Drive File Name is simply the name of a file or folder in Google Drive.

## Ways to Reference Drive Files in Commands

### By ID
You can reference a Drive file by its ID in these ways:
- Just the ID or URL by itself
- `(id FILE_ID_OR_URL)` or `(id:FILE_ID_OR_URL)`
- `(ids LIST_OF_FILE_IDS)` or `(ids:LIST_OF_FILE_IDS)` for multiple files

### By Name
You can reference a Drive file by its name in these ways:
- `(name FILE_NAME)` or `(name:FILE_NAME)`
- `(drivefilename FILE_NAME)` or `(drivefilename:FILE_NAME)`
- `(anyname FILE_NAME)` or `(anyname:FILE_NAME)`
- `(anydrivefilename FILE_NAME)` or `(anydrivefilename:FILE_NAME)`

## Shared Drive References

### By ID
You can reference a Shared Drive by its ID in these ways:
- Just the ID or URL by itself
- `(teamdriveid DRIVE_ID_OR_URL)` or `(teamdriveid:DRIVE_ID_OR_URL)`

### By Name
You can reference a Shared Drive by its name using:
- `(teamdrive DRIVE_NAME)` or `(teamdrive:DRIVE_NAME)`

### Combined References
A Shared Drive reference can be either an ID reference or a name reference.

### Files in Shared Drives
You can reference a file in a Shared Drive by its name using:
- `(teamdrivefilename FILE_NAME)` or `(teamdrivefilename:FILE_NAME)`

## Practical Examples with GAM

Here are some examples of how to use these references in GAM commands:

### Working with Files by ID

```bash
# Show file information using a file ID
gam user user@example.com show fileinfo 1a2b3c4d5e6f7g8h9i0j

# Show file information using a file URL
gam user user@example.com show fileinfo https://docs.google.com/document/d/1a2b3c4d5e6f7g8h9i0j/edit

# Download a file using ID syntax
gam user user@example.com get drivefile (id:1a2b3c4d5e6f7g8h9i0j) format docx targetfolder ~/Downloads

# Share a file with another user
gam user user@example.com add drivefileacl 1a2b3c4d5e6f7g8h9i0j user colleague@example.com role writer
```

### Working with Files by Name

```bash
# Show file information using a file name
gam user user@example.com show fileinfo (name:"Quarterly Report")

# Find files with a specific name pattern
gam user user@example.com print filelist query "name contains 'Budget'" fields id,name,owners

# Download a file using name syntax
gam user user@example.com get drivefile (name:"Project Plan.docx") targetfolder ~/Downloads
```

### Working with Shared Drives

```bash
# List all Shared Drives
gam print teamdrives

# Show information about a specific Shared Drive by name
gam show teamdrive (teamdrive:"Marketing Department")

# Show information about a Shared Drive by ID
gam show teamdrive 0Ab1CdEf2GhIjKlMnOpQ

# List files in a Shared Drive
gam print filelist teamdriveid 0Ab1CdEf2GhIjKlMnOpQ

# Find a specific file in a Shared Drive
gam print filelist teamdriveid 0Ab1CdEf2GhIjKlMnOpQ query "name = 'Annual Report.pdf'"

# Create a folder in a Shared Drive
gam user user@example.com create drivefile drivefilename "New Project" mimetype gfolder teamdriveid 0Ab1CdEf2GhIjKlMnOpQ
```

### Working with Multiple Files

```bash
# Copy multiple files to a folder
gam user user@example.com copy drivefile (ids:1a2b3c,4d5e6f,7g8h9i) parentid 0Ab1CdEf2GhIjKlMnOpQ

# Change ownership of multiple files
gam user current@example.com transfer ownership (ids:1a2b3c,4d5e6f,7g8h9i) user new@example.com
```