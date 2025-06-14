# Users - Chat - Human-Friendly Guide

- [Introduction](#introduction)
- [Initial Setup Required](#initial-setup-required)
- [API Documentation](#api-documentation)
- [Simple Terminology](#simple-terminology)
- [Understanding Chat Permissions](#understanding-chat-permissions)
- [Create and Manage Chat Spaces](#create-and-manage-chat-spaces)
- [View Chat Space Information](#view-chat-space-information)
- [Manage Chat Members](#manage-chat-members)
- [View Chat Member Information](#view-chat-member-information)
- [Send and Manage Messages](#send-and-manage-messages)
- [View Messages and Events](#view-messages-and-events)
- [Bulk Operations for Large Organizations](#bulk-operations-for-large-organizations)
- [Practical Examples](#practical-examples)

## Introduction

Google Chat is Google Workspace's team messaging platform that allows users to communicate in spaces (chat rooms), group chats, and direct messages. These GAM commands let administrators manage Chat spaces, members, and messages programmatically.

**Important:** These features require GAMADV-XTD3 version 6.60.00 or later and special setup (see next section).

## Initial Setup Required

### Step 1: Update Service Account Permissions
Before using Chat commands, you must update your service account with Chat API permissions:

```bash
gam user user@domain.com update serviceaccount
```

Select these Chat API options when prompted:
- Chat API - Memberships (supports readonly)
- Chat API - Memberships Admin (supports readonly)
- Chat API - Messages (supports readonly)
- Chat API - Spaces (supports readonly)
- Chat API - Spaces Admin (supports readonly)
- Chat API - Spaces Delete
- Chat API - Spaces Delete Admin

### Step 2: Set Up a Chat Bot
Google requires a Chat Bot to use the Chat API. Run this command and follow the setup wizard:

```bash
gam setup chat
```

**Bot Setup Instructions:**
1. Enter an App name and Description of your choice
2. For Avatar URL, use: `https://dummyimage.com/384x256/4d4d4d/0011ff.png&text=+GAM`
3. In Functionality: **Uncheck** both "Receive 1:1 messages" and "Join spaces and group conversations"
4. In Connection settings: Choose "Cloud Pub/Sub" and enter "no-topic" for topic name
5. In Visibility: **Uncheck** "Make this Chat app available to specific people and groups"
6. Click Save

### Step 3: Configure Admin Access (Optional)
Add this to your `gam.cfg` file to control admin access behavior:

```
use_chat_admin_access = False
```

- **False** (default): Uses user access for all calls, can override with `asadmin` option
- **True**: Automatically uses admin access when available

## API Documentation
* [Google Chat Overview](https://developers.google.com/workspace/chat/overview)
* [Chat API Reference](https://developers.google.com/workspace/chat/api/reference/rest)
* [Chat Apps Support](https://support.google.com/chat/answer/7655820)
* [Admin Console Chat Management](https://support.google.com/a/answer/13369245)

## Simple Terminology

Instead of formal syntax notation, here are simple explanations of terms used in GAM Chat commands:

### Basic Terms
* **Chat Space**: A chat room where multiple people can communicate (like "spaces/ABC123")
* **Space Name**: The display name of a chat space (like "Marketing Team")
* **Chat Member**: A person or bot that belongs to a chat space
* **Direct Message**: A private conversation between two people
* **Group Chat**: A small group conversation (2-20 people)
* **Thread**: A conversation within a chat space organized by topic

### Chat Space Types
* **Space**: A named chat room that can have many members and advanced features
* **Group Chat**: A simple group conversation with 2-20 people
* **Direct Message**: Private conversation between exactly 2 people

### Member Roles
* **Member**: Regular participant who can read and write messages
* **Manager**: Advanced participant who can manage space settings and members

### Message Content Options
You can send messages using any of these methods:
* **Text**: Direct text in the command
* **Text File**: Read message from a local file
* **Google Doc**: Read message from a Google Document
* **Cloud Storage**: Read message from Google Cloud Storage

For more details on specifying collections of users, see [Collections of Users](Collections-of-Users).

## Understanding Chat Permissions

Google Chat has two types of space permissions that affect what members can do:

### Announcement Spaces
Best for one-way communication where only managers post important updates:

- **Post Messages**: Only managers can post new messages
- **Reply to Messages**: Members can reply to existing messages
- **Manage Members**: Only managers can add/remove people
- **Modify Space Details**: Only managers can change space name/description
- **Use @all Mentions**: Only managers can notify everyone

### Collaboration Spaces (Default)
Best for team discussions where everyone participates equally:

- **Post Messages**: All members can post new messages
- **Reply to Messages**: All members can reply
- **Manage Members**: All members can add/remove people
- **Modify Space Details**: All members can change space settings
- **Use @all Mentions**: All members can notify everyone

## Create and Manage Chat Spaces

### Create a New Chat Space
```bash
gam <Users> create chatspace [options]
```

**For Named Spaces (Team Rooms):**
```bash
# Create a collaboration space (default)
gam user manager@example.com create chatspace type space displayname "Marketing Team" description "Marketing team discussions" members "user1@example.com,user2@example.com"

# Create an announcement space
gam user manager@example.com create chatspace type space displayname "Company Announcements" description "Important company updates" announcement members "all-staff@example.com"
```

**For Group Chats:**
```bash
# Create a small group chat (2-20 people required)
gam user manager@example.com create chatspace type groupchat members "user1@example.com,user2@example.com,user3@example.com"
```

**For Direct Messages:**
```bash
# Create a direct message conversation
gam user manager@example.com create chatspace type directmessage members "colleague@example.com"
```

**Available Options:**
- `displayname`: Name for the space (required for spaces)
- `description`: Description of the space's purpose
- `guidelines`: Rules or guidelines for the space
- `history`: Whether to save message history (true/false)
- `members`: List of people to add initially
- `announcement|collaboration`: Permission model (spaces only)

### Update an Existing Chat Space
```bash
# Update space details
gam user manager@example.com update chatspace spaces/ABC123 displayname "New Marketing Team" description "Updated description" history true

# Convert group chat to named space
gam user manager@example.com update chatspace spaces/XYZ789 type space displayname "Project Alpha Team"

# Update space permissions (Developer Preview)
gam user manager@example.com update chatspace spaces/ABC123 managemembersandgroups managers modifyspacedetails members
```

### Delete a Chat Space
```bash
# Delete as user
gam user manager@example.com delete chatspace spaces/ABC123

# Delete as admin
gam user admin@example.com delete chatspace asadmin spaces/ABC123
```

## View Chat Space Information

### View Details of a Specific Space
```bash
# Show all information about a space
gam user manager@example.com info chatspace spaces/ABC123

# Show only specific details
gam user manager@example.com info chatspace spaces/ABC123 fields displayname,membershipcount,spacetype

# Show information about a direct message space
gam user manager@example.com info chatspacedm colleague@example.com
```

### List All Spaces for a User
```bash
# Show all spaces
gam user manager@example.com show chatspaces

# Show only specific types of spaces
gam user manager@example.com show chatspaces types space,groupchat

# Export to CSV for analysis
gam user manager@example.com print chatspaces todrive
```

### Admin View of All Organization Spaces
```bash
# Show all spaces in organization (admin only)
gam user admin@example.com show chatspaces asadmin

# Filter spaces by criteria
gam user admin@example.com show chatspaces asadmin query "displayName:Marketing"

# Export organization spaces to CSV
gam user admin@example.com print chatspaces asadmin todrive
```

## Manage Chat Members

### Add Members to a Space
```bash
# Add individual users
gam user manager@example.com create chatmember spaces/ABC123 user user1@example.com user user2@example.com

# Add multiple users from a list
gam user manager@example.com create chatmember spaces/ABC123 members "user1@example.com,user2@example.com,user3@example.com"

# Add a Google Group
gam user manager@example.com create chatmember spaces/ABC123 group marketing-team@example.com

# Add users as managers
gam user manager@example.com create chatmember spaces/ABC123 role manager members "team-lead@example.com"

# Admin add members (for external users)
gam user admin@example.com create chatmember asadmin spaces/ABC123 members "external@partner.com"
```

### Remove Members from a Space
```bash
# Remove specific users
gam user manager@example.com delete chatmember spaces/ABC123 user user1@example.com user user2@example.com

# Remove a group
gam user manager@example.com delete chatmember spaces/ABC123 group old-team@example.com

# Remove using member IDs
gam user manager@example.com remove chatmember members "spaces/ABC123/members/DEF456,spaces/ABC123/members/GHI789"

# Admin remove members
gam user admin@example.com delete chatmember asadmin spaces/ABC123 user user@example.com
```

### Change Member Roles
```bash
# Promote users to managers
gam user manager@example.com update chatmember spaces/ABC123 role manager user team-lead@example.com

# Demote managers to regular members
gam user manager@example.com update chatmember spaces/ABC123 role member user former-lead@example.com

# Update using member IDs
gam user manager@example.com modify chatmember role manager members "spaces/ABC123/members/DEF456"

# Admin update roles
gam user admin@example.com update chatmember asadmin spaces/ABC123 role manager user new-manager@example.com
```

## View Chat Member Information

### View Members of a Space
```bash
# Show all members of a space
gam user manager@example.com show chatmembers spaces/ABC123

# Show members of multiple spaces
gam user manager@example.com show chatmembers spaces/ABC123 spaces/XYZ789

# Show only managers
gam user manager@example.com show chatmembers spaces/ABC123 filter 'role = "ROLE_MANAGER"'

# Show only human members (no bots)
gam user manager@example.com show chatmembers spaces/ABC123 filter 'member.type = "HUMAN"'

# Include invited but not yet joined members
gam user manager@example.com show chatmembers spaces/ABC123 showinvited true

# Show group memberships
gam user manager@example.com show chatmembers spaces/ABC123 showgroups true

# Export member list to CSV
gam user manager@example.com print chatmembers spaces/ABC123 todrive
```

### View Specific Member Details
```bash
# Show details of specific members
gam user manager@example.com info chatmember members "spaces/ABC123/members/DEF456,spaces/ABC123/members/GHI789"

# Show with specific fields only
gam user manager@example.com info chatmember members "spaces/ABC123/members/DEF456" fields member,role,createtime
```

### Admin View of All Organization Members
```bash
# Show members across all organization spaces (admin)
gam user admin@example.com show chatmembers asadmin query "displayName:Marketing"

# Export all organization chat members
gam user admin@example.com print chatmembers asadmin todrive
```

## Send and Manage Messages

### Send Messages to a Space
```bash
# Send a simple text message
gam user manager@example.com create chatmessage spaces/ABC123 text "Hello team! Meeting at 2 PM today."

# Send a formatted message
gam user manager@example.com create chatmessage spaces/ABC123 text "*Important:* Please review the _quarterly report_ by ~Friday~"

# Send message from a file
gam user manager@example.com create chatmessage spaces/ABC123 textfile ./announcement.txt

# Send message from a Google Doc
gam user manager@example.com create chatmessage spaces/ABC123 gdoc announcements@example.com name "Weekly Update"

# Reply to an existing thread
gam user manager@example.com create chatmessage spaces/ABC123 text "Thanks for the update!" thread spaces/ABC123/threads/THREAD123

# Create a threaded conversation with a custom key
gam user manager@example.com create chatmessage spaces/ABC123 text "Starting new project discussion" threadkey "project-alpha-discussion"

# Continue the thread using the key
gam user manager@example.com create chatmessage spaces/ABC123 text "Here are the requirements" threadkey "project-alpha-discussion"
```

### Update Existing Messages
```bash
# Edit a message (shows as "edited")
gam user manager@example.com update chatmessage name spaces/ABC123/messages/MSG456 text "Updated: Meeting moved to 3 PM today."

# Update message from file
gam user manager@example.com update chatmessage name spaces/ABC123/messages/MSG456 textfile ./corrected-announcement.txt
```

### Delete Messages
```bash
# Delete a message
gam user manager@example.com delete chatmessage name spaces/ABC123/messages/MSG456
```

## View Messages and Events

### View Specific Messages
```bash
# Show details of a specific message
gam user manager@example.com info chatmessage name spaces/ABC123/messages/MSG456

# Show with specific fields only
gam user manager@example.com info chatmessage name spaces/ABC123/messages/MSG456 fields text,sender,createtime
```

### View All Messages in a Space
```bash
# Show all messages in a space
gam user manager@example.com show chatmessages spaces/ABC123

# Include deleted messages
gam user manager@example.com show chatmessages spaces/ABC123 showdeleted true

# Filter messages by date
gam user manager@example.com show chatmessages spaces/ABC123 filter 'createTime > "2024-01-01T00:00:00-00:00"'

# Filter messages in a specific thread
gam user manager@example.com show chatmessages spaces/ABC123 filter 'thread.name = spaces/ABC123/threads/THREAD123'

# Filter by date range and thread
gam user manager@example.com show chatmessages spaces/ABC123 filter 'createTime > "2024-01-01T00:00:00-00:00" AND createTime < "2024-02-01T00:00:00-00:00" AND thread.name = spaces/ABC123/threads/THREAD123'

# Export messages to CSV
gam user manager@example.com print chatmessages spaces/ABC123 todrive
```

### View Chat Events
```bash
# Show specific event
gam user manager@example.com info chatevent name spaces/ABC123/spaceEvents/EVENT456

# Show all events in a space with filter
gam user manager@example.com show chatevents spaces/ABC123 filter 'start_time="2024-03-15T11:30:00-04:00" AND event_types:"google.workspace.chat.message.v1.created"'

# Export events to CSV
gam user manager@example.com print chatevents spaces/ABC123 filter 'start_time="2024-01-01T00:00:00-00:00" AND event_types:"google.workspace.chat.message.v1.created"' todrive
```

## Bulk Operations for Large Organizations

### Get All Chat Spaces for Multiple Users
```bash
# Export all users' chat spaces to CSV
gam config auto_batch_min 1 redirect csv ./AllUserChatSpaces.csv multiprocess redirect stdout - multiprocess redirect stderr stdout all users print chatspaces

# Export specific types of spaces only
gam config auto_batch_min 1 redirect csv ./UserSpaces.csv multiprocess redirect stdout - multiprocess redirect stderr stdout all users print chatspaces types space,groupchat
```

### Get All Members Across Organization
```bash
# First get all spaces
gam config auto_batch_min 1 redirect csv ./ChatSpaces.csv multiprocess redirect stdout - multiprocess redirect stderr stdout all users print chatspaces

# Then get all members of those spaces
gam redirect csv ./AllChatMembers.csv multiprocess redirect stdout - multiprocess redirect stderr stdout csv ./ChatSpaces.csv gam user "~User" print chatmembers "~name"
```

### Remove User from All Their Spaces
```bash
# Get user's spaces
gam redirect csv ./UserSpaces.csv user departing@example.com print chatspaces types space,groupchat

# Remove user from all spaces (as admin)
gam redirect stdout ./RemoveUserFromSpaces.txt multiprocess redirect stderr stdout csv ./UserSpaces.csv gam user admin@example.com delete chatmember asadmin "~name" user departing@example.com
```

## Practical Examples

### Example 1: Set Up a New Team Space
Create a collaboration space for a project team:

```bash
# Create the space with initial members
gam user project-manager@example.com create chatspace type space displayname "Project Alpha Team" description "Collaboration space for Project Alpha development" guidelines "Please keep discussions on-topic and professional" history true members "dev1@example.com,dev2@example.com,designer@example.com,qa@example.com"

# Send welcome message with project info
gam user project-manager@example.com create chatmessage spaces/NEW_SPACE_ID text "*Welcome to Project Alpha!*\n\nThis space is for all project-related discussions.\n\n*Next Steps:*\n- Review project requirements document\n- Join daily standup at 9 AM\n- Questions? Tag @project-manager"
```

### Example 2: Create Department Announcement Space
Set up a one-way communication channel for leadership:

```bash
# Create announcement-style space
gam user dept-head@example.com create chatspace type space displayname "Engineering Announcements" description "Important updates for the engineering team" announcement members "engineering-team@example.com"

# Send first announcement
gam user dept-head@example.com create chatmessage spaces/ANNOUNCEMENT_SPACE_ID text "*Welcome to Engineering Announcements*\n\nThis space is for important team updates and announcements.\n\nManagerial team can post, everyone can reply to discuss."
```

### Example 3: Manage Team Reorganization
Handle member changes during team restructuring:

```bash
# Remove departing team members
gam user manager@example.com delete chatmember spaces/TEAM_SPACE_ID user leaving1@example.com user leaving2@example.com

# Add new team members
gam user manager@example.com create chatmember spaces/TEAM_SPACE_ID members "new1@example.com,new2@example.com"

# Promote new team lead to manager
gam user manager@example.com update chatmember spaces/TEAM_SPACE_ID role manager user new-lead@example.com

# Update space details
gam user manager@example.com update chatspace spaces/TEAM_SPACE_ID displayname "Restructured Team Alpha" description "Updated team structure as of March 2024"

# Send transition message
gam user manager@example.com create chatmessage spaces/TEAM_SPACE_ID text "*Team Update*\n\nWelcome to our new team members and congratulations to our new team lead!\n\nLet's continue our great work together."
```

### Example 4: Automated Daily Announcements
Send daily updates from a file:

```bash
# Create daily announcement (could be automated with cron/task scheduler)
gam user comms@example.com create chatmessage spaces/DAILY_UPDATES_SPACE textfile ./daily-update-$(date +%Y-%m-%d).txt

# Or send from a constantly updated Google Doc
gam user comms@example.com create chatmessage spaces/DAILY_UPDATES_SPACE gdoc communications@example.com name "Daily Standup Notes"
```

### Example 5: Audit and Compliance Reporting
Generate reports for compliance or analysis:

```bash
# Export all organization spaces for audit
gam user admin@example.com print chatspaces asadmin todrive

# Export all messages from specific spaces for compliance
gam user compliance@example.com print chatmessages spaces/LEGAL_SPACE_ID spaces/HR_SPACE_ID filter 'createTime > "2024-01-01T00:00:00-00:00"' todrive

# Get membership reports
gam user admin@example.com print chatmembers asadmin todrive
```

### Example 6: Bulk Space Creation for Multiple Teams
Create similar spaces for different departments:

```bash
# Create marketing team space
gam user admin@example.com create chatspace type space displayname "Marketing Team" description "Marketing team collaboration" members "marketing@example.com"

# Create sales team space  
gam user admin@example.com create chatspace type space displayname "Sales Team" description "Sales team collaboration" members "sales@example.com"

# Create support team space
gam user admin@example.com create chatspace type space displayname "Support Team" description "Customer support collaboration" members "support@example.com"
```

### Example 7: Emergency Communication Setup
Quickly set up emergency communication channels:

```bash
# Create emergency announcement space
gam user crisis-manager@example.com create chatspace type space displayname "Emergency Communications" description "Critical updates during emergency situations" announcement members "all-staff@example.com"

# Send immediate alert
gam user crisis-manager@example.com create chatmessage spaces/EMERGENCY_SPACE_ID text "*EMERGENCY ALERT*\n\nPlease check your email for important safety instructions.\n\nDo not reply here - follow instructions in email.\n\nStay safe!"
```

### Example 8: Project Handover Process
Transfer project ownership and update permissions:

```bash
# Update space ownership
gam user old-manager@example.com update chatmember spaces/PROJECT_SPACE_ID role manager user new-manager@example.com

# Remove old manager's admin rights
gam user new-manager@example.com update chatmember spaces/PROJECT_SPACE_ID role member user old-manager@example.com

# Update space details
gam user new-manager@example.com update chatspace spaces/PROJECT_SPACE_ID description "Project now managed by New Manager - updated March 2024"

# Send handover message
gam user new-manager@example.com create chatmessage spaces/PROJECT_SPACE_ID text "*Project Handover*\n\nI'm now managing this project. Please direct all questions to me.\n\nThanks to Old Manager for their excellent work getting us this far!"
```

## Tips and Best Practices

1. **Space Organization**: Use clear, descriptive names and include guidelines for proper usage

2. **Permission Management**: Choose announcement vs collaboration spaces based on communication needs

3. **Bulk Operations**: Use CSV exports and imports for managing large numbers of spaces or members

4. **Message Formatting**: Use Chat's simple formatting (*bold*, _italic_, ~strikethrough~) for emphasis

5. **Thread Management**: Use thread keys for ongoing conversations to keep messages organized

6. **Admin Access**: Use `asadmin` commands for organization-wide management and external user access

7. **Compliance**: Regularly export messages and events for audit and compliance requirements

8. **Automation**: Combine GAM commands with scripts for routine tasks like daily announcements

9. **Member Management**: Regularly audit space memberships to ensure appropriate access

10. **Testing**: Test commands with small groups before applying to large user sets 