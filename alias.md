---
layout: default
title: Aliases
---

# Aliases - Human-Friendly Guide

- [Introduction](#introduction)
- [API Documentation](#api-documentation)
- [Query Documentation](#query-documentation)
- [Simple Terminology](#simple-terminology)
- [Create an Alias](#create-an-alias-for-a-target)
- [Update an Alias](#update-an-alias-to-point-to-a-new-target)
- [Delete an Alias](#delete-an-alias-regardless-of-the-target)
- [Remove Aliases from a Target](#remove-aliases-from-a-specified-target)
- [Delete All User Aliases](#delete-all-of-a-users-aliases)
- [Display Aliases](#display-aliases)
- [Bulk Delete Aliases](#bulk-delete-aliases)
- [Bulk Reassign Aliases](#bulk-reassign-aliases)
- [Check Address Type](#determine-if-an-address-is-a-user-user-alias-group-or-group-alias)
- [Practical Examples](#practical-examples)

## Introduction

Aliases are alternative email addresses that point to a primary user or group. They allow people to receive emails sent to multiple addresses in a single inbox. This document explains how to manage aliases using GAM commands.

## API Documentation
* [Directory API - User Aliases](https://developers.google.com/admin-sdk/directory/reference/rest/v1/users.aliases)
* [Directory API - Group Aliases](https://developers.google.com/admin-sdk/directory/reference/rest/v1/groups.aliases)

## Query Documentation
* [Search Users](https://developers.google.com/admin-sdk/directory/v1/guides/search-users)

## Simple Terminology

Instead of formal syntax notation, here are simple explanations of terms used in GAM commands:

* **Domain Name**: A standard internet domain like "example.com" or "school.org"
* **Domain Name List**: Multiple domains separated by commas, like "example.com,school.org"
* **Email Address**: A standard email address format like "user@example.com"
* **Email Address List**: Multiple email addresses separated by commas
* **Unique ID**: An identifier prefixed with "id:", like "id:abc123"
* **Regular Expression**: A pattern for matching text (see [Python Regular Expressions](Python-Regular-Expressions))
* **Match Pattern**: A regular expression pattern used to match specific text
* **Search Pattern**: A regular expression pattern used to search for text
* **Substitution**: Text used to replace matched patterns

You can specify collections of items using files or CSV files. For more details, see [Collections of Items](Collections-of-Items).

## Create an Alias for a Target

```
gam create alias|aliases <EmailAddressEntity> user|group|target <UniqueID>|<EmailAddress> [verifynotinvitable]
```

This command creates one or more aliases that point to a specific user or group.

* **EmailAddressEntity**: The alias(es) you want to create
* **UniqueID or EmailAddress**: The target user or group that will receive emails sent to the alias
* **verifynotinvitable**: Optional check to ensure the alias isn't an unmanaged account

## Update an Alias to Point to a New Target

```
gam update alias|aliases <EmailAddressEntity> user|group|target <UniqueID>|<EmailAddress> [notargetverify] [waitafterdelete <Integer>]
```

This command changes where an existing alias points to. It works by deleting the old alias and creating a new one.

* **EmailAddressEntity**: The alias(es) you want to update
* **UniqueID or EmailAddress**: The new target user or group
* **notargetverify**: Skip checking if the target exists
* **waitafterdelete**: How many seconds to wait after deleting (max 10 seconds)

## Delete an Alias Regardless of the Target

```
gam delete alias|aliases [user|group|target] <EmailAddressEntity>
```

This command deletes one or more aliases completely, regardless of what they point to.

## Remove Aliases from a Specified Target

```
gam remove alias|aliases <EmailAddress> user|group <EmailAddressEntity>
```

This command removes specific aliases from a particular user or group.

* **EmailAddress**: The target user or group
* **EmailAddressEntity**: The alias(es) to remove

## Delete All of a User's Aliases

```
gam <UserTypeEntity> delete aliases
```

This command removes all aliases from specified users.

## Display Aliases

To display information about a specific alias:
```
gam info alias|aliases <EmailAddressEntity>
```

To display selected aliases with filtering options:
```
gam print aliases [todrive <ToDriveAttribute>*]
        ([domain|domains <DomainNameEntity>] [(query <QueryUser>)|(queries <QueryUserList>)]
         [limittoou <OrgUnitItem>])
        [user|users <EmailAddressList>] [group|groups <EmailAddressList>]
        [select <UserTypeEntity>]
        [aliasmatchpattern <REMatchPattern>]
        [shownoneditable] [nogroups] [nousers]
        [onerowpertarget] [delimiter <Character>]
        [suppressnoaliasrows]
        (addcsvdata <FieldName> <String>)*
```

This command lets you list aliases with various filtering options:

* Filter by domain(s)
* Filter by query
* Limit to specific organizational units
* Show only specific users or groups
* Match aliases by pattern
* Show only user aliases or only group aliases
* Format output in different ways

By default, the output shows three columns: Alias, Target, and TargetType. You can change this format with options like `onerowpertarget`.

## Bulk Delete Aliases

You can delete many aliases at once using this approach:

```
gam redirect csv ./OldDomainAliases.csv print aliases aliasmatchpattern ".*@olddomain.com" onerowpertarget suppressnoaliasrows
gam redirect stdout ./DeleteAliases.txt multiprocess redirect stderr stdout csv ./OldDomainAliases.csv gam remove aliases "~Target" "~TargetType" "~Aliases"
```

This first creates a CSV file of all aliases matching a pattern, then processes that file to delete them.

## Bulk Reassign Aliases

You can move many aliases from one target to another:

1. Create a CSV file with OldTarget,NewTarget columns
2. Generate a list of aliases to reassign
3. Process the list to update all aliases

## Determine if an Address is a User, User Alias, Group or Group Alias

```
gam whatis <EmailItem> [noinfo] [noinvitablecheck]
```

This command tells you whether an email address belongs to a user, is an alias for a user, belongs to a group, or is an alias for a group.

* **noinfo**: Don't show detailed information
* **noinvitablecheck**: Skip checking if it's a user invitation (saves API quota)

The command returns different exit codes depending on what type of address it is.

## Practical Examples

### Example 1: Create an Alias for a User
To allow Robert to receive mail sent to bob@yourdomain.com:

```
gam create alias bob@yourdomain.com user robert@yourdomain.com
```

### Example 2: Create Multiple Aliases for a User
To give a user several alternative email addresses:

```
gam create aliases "sales@yourdomain.com,support@yourdomain.com,info@yourdomain.com" user helpdesk@yourdomain.com
```

### Example 3: Update an Alias to Point to a Different User
When an employee changes roles and someone else takes over their responsibilities:

```
gam update alias finance@yourdomain.com user newfinanceperson@yourdomain.com
```

### Example 4: List All Aliases in a Domain
To see all aliases in your organization:

```
gam print aliases
```

### Example 5: Find All Aliases Matching a Pattern
To find all aliases containing "support":

```
gam print aliases aliasmatchpattern ".*support.*"
```

### Example 6: Delete an Alias
To remove an alias that's no longer needed:

```
gam delete alias oldname@yourdomain.com
```

### Example 7: Check What Type of Address Something Is
To determine if an address is a user, group, or alias:

```
gam whatis mysterious@yourdomain.com
```

### Example 8: Move All Aliases from One User to Another
When an employee leaves and another takes over their responsibilities:

```
gam print aliases user oldemployee@yourdomain.com > aliases.txt
# Edit aliases.txt as needed
gam csv aliases.txt gam update alias "~Alias" user newemployee@yourdomain.com
```

### Example 9: Delete All Aliases for a Specific User
To clean up when an employee leaves:

```
gam user oldemployee@yourdomain.com delete aliases
```

### Example 10: Export All Aliases to Google Sheets
To create a spreadsheet of all aliases for review:

```
gam print aliases todrive
```