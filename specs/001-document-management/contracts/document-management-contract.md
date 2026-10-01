# Document Management Contract

This document defines the service-level contract for document uploads, access checks, and sharing within the Blazor Server application. It is intentionally implementation-oriented but stays at the contract boundary instead of embedding UI code.

## UploadDocumentRequest

```json
{
  "title": "Quarterly roadmap",
  "category": "Reports",
  "description": "Updated project summary for Q4",
  "tags": ["roadmap", "q4"],
  "projectId": 12,
  "taskId": 7,
  "files": [
    {
      "fileName": "roadmap.pdf",
      "contentType": "application/pdf",
      "sizeBytes": 245760,
      "content": "base64-encoded-bytes"
    }
  ]
}
```

### Rules
- `title` is required and must not be blank after trimming.
- `category` must be one of the approved enum values.
- Each file is validated independently.
- `projectId` and `taskId` are optional but must remain consistent with role-based access checks.

## UploadDocumentResult

```json
{
  "fileResults": [
    {
      "fileName": "roadmap.pdf",
      "status": "Accepted",
      "documentId": 123,
      "message": "Upload completed successfully"
    },
    {
      "fileName": "archive.exe",
      "status": "Rejected",
      "message": "Unsupported file type"
    }
  ]
}
```

`status` should be one of `Accepted`, `Rejected`, or `Pending`.

## DocumentSummary

```json
{
  "id": 123,
  "title": "Quarterly roadmap",
  "category": "Reports",
  "description": "Updated project summary for Q4",
  "tags": ["roadmap", "q4"],
  "uploaderId": 4,
  "projectId": 12,
  "taskId": 7,
  "fileSizeBytes": 245760,
  "fileType": "application/pdf",
  "safetyStatus": "Approved",
  "uploadedAtUtc": "2026-09-30T16:00:00Z"
}
```

## ShareDocumentRequest

```json
{
  "documentId": 123,
  "recipientUserIds": [5, 8],
  "recipientTeamIds": [2]
}
```

### Rules
- Only the owner or an authorized manager may share the document.
- Recipients receive access only to the shared document and its permitted actions.
- Sharing produces an in-app notification and a `DocumentActivity` record.

## Access Validation Contract

The document service must return a deny-safe result for any action against a document when the current user lacks permission.

```json
{
  "isAuthorized": false,
  "reason": "User is not a project member or explicit recipient",
  "documentId": 123
}
```

This contract is used for list filtering, search, preview, download, replacement, deletion, and shared-document access.

## Service responsibilities

- `UploadAsync` validates file type and size, writes metadata, and performs the safety gate before allowing access.
- `SearchAsync` returns only authorized document results matching the active user’s scope.
- `PreviewAsync` and `DownloadAsync` enforce access checks before exposing the file.
- `ShareAsync` verifies ownership or management rights and records the event.
- `DeleteAsync` and `ReplaceAsync` re-check authorization before committing. 

These service boundaries are the contract that the UI pages will call, keeping the document logic explicit and consistent with the repo’s existing layered architecture.
