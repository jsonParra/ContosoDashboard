# Data Model: Document Upload and Management

## Core Entities

### Document

| Field | Type | Constraints / Notes |
|---|---|---|
| DocumentId | int | Primary key |
| Title | string | Required, trimmed, non-empty |
| Description | string? | Optional free text |
| Category | enum/string | Must be one of Project Documents, Team Resources, Personal Files, Reports, Presentations, Other |
| Tags | string? | Optional comma-delimited or JSON metadata |
| OriginalFileName | string | Required for reportability and user display |
| StoredFilePath | string | Local storage path or generated file key |
| FileSizeBytes | long | Must be > 0 and <= 25 MB |
| MimeType | string | Derived from upload or safe fallback |
| SafetyStatus | enum/string | Pending, Approved, Rejected |
| UploadDateUtc | DateTime | Set on initial acceptance |
| UpdatedAtUtc | DateTime | Updated on edits or replacement |
| UploaderId | int | FK to `User` |
| ProjectId | int? | Optional project association |
| TaskId | int? | Optional task association |
| IsDeleted | bool | Soft delete or permanent delete at the service layer |

**Relationships**
- Many `Document` rows belong to one `User` (uploader)
- Many `Document` rows may belong to one `Project` or one `Task` when relevant
- One `Document` can have many `DocumentAccessGrant` rows
- One `Document` can have many `DocumentActivity` rows

### DocumentAccessGrant

| Field | Type | Constraints / Notes |
|---|---|---|
| DocumentAccessGrantId | int | Primary key |
| DocumentId | int | FK to `Document` |
| GrantedToUserId | int? | Optional recipient user |
| GrantedToTeamId | int? | Optional team or role-scoped recipient |
| CreatedByUserId | int | Owner or authorized grant creator |
| CreatedAtUtc | DateTime | When the grant was created |
| IsActive | bool | True until revoked or the document is removed |

**Relationships**
- Each grant is attached to exactly one `Document`
- Recipient is one user or one team scope, depending on the implementation model

### DocumentActivity

| Field | Type | Constraints / Notes |
|---|---|---|
| DocumentActivityId | int | Primary key |
| ActionType | enum/string | Upload, Download, Delete, Share |
| ActorUserId | int | FK to `User` |
| DocumentId | int | FK to `Document` |
| OccurredAtUtc | DateTime | Event timestamp |
| Details | string? | Optional summary or metadata |

**Relationships**
- One actor user may create many activity records
- One document may have many activity records

## Existing Entities Reused

### User
- Existing entity already stores identity, role, team, and project context for authorization checks.
- Used as the source of ownership and permission decisions.

### Project
- Existing entity already represents project membership and permissions.
- Used to scope project documents and project-visible activity.

### Task
- Existing task entity is used to associate the document to a project task when uploaded from a task details screen.

## Validation Rules

- Title must not be null, empty, or whitespace-only.
- Category must be one of the approved categories in `FR-005`.
- File type must be in the supported set: PDF, Word, Excel, PowerPoint, plaintext, JPEG, or PNG.
- File size must be <= 25 MB.
- Document must remain `Pending` until inspection passes; `Rejected` if inspection fails or the engine is unavailable and the file remains blocked.
- Search and list operations must never include documents that the current user cannot access.
- A document can be replaced only if the current user still owns the document or otherwise retains management authority.

## State Transitions

```text
Pending -> Approved
Pending -> Rejected
Approved -> Deleted
Approved -> Replaced (new file stored; metadata retained)
Rejected -> Deleted or left blocked
```

**Important**: A rejected or pending document is not visible for preview, download, or sharing, and the uploader must receive a clear status explanation.
