# Quickstart: Document Upload and Management Validation

## Prerequisites

- .NET 8 SDK installed
- SQL Server LocalDB available to the training app
- The ContosoDashboard project restored and ready to run
- An authenticated dashboard user available via the mock login flow

## Run the application

```powershell
cd "ContosoDashboard"
dotnet run
```

Then sign in with one of the seeded users (for example, `ni.kang@contoso.com` or `camille.nicole@contoso.com`).

## Validation scenarios

### 1. Upload a valid document

1. Open the dashboard or a project/task view.
2. Choose the document upload action.
3. Select a valid PDF or image under 25 MB.
4. Provide a title and category.
5. Submit the upload.

**Expected result**: the file is accepted, displays upload progress, and appears in the user’s document list with the correct metadata.

### 2. Test a rejected upload

1. Upload a file larger than 25 MB or a disallowed extension.
2. Submit the form.

**Expected result**: the file is rejected with a clear explanation; it is not added to the document list and no user can access it.

### 3. Verify authorization behavior

1. Upload a document as one user and then sign out.
2. Sign in as another user without project access.
3. Attempt to search or view the document.

**Expected result**: unauthorized users do not see the document in results or lists and cannot preview or download it.

### 4. Verify project/task association and sharing

1. Attach or upload a document in a project or task context.
2. Share it with another user or team.
3. Sign in as the recipient.

**Expected result**: the recipient sees the shared document in the shared-with-me view and can access it only within the granted scope.

### 5. Verify audit trail and dashboard reporting

1. Upload, download, share, or delete a document.
2. Open the reporting or dashboard view if available in the app.

**Expected result**: an activity record exists for the event and the dashboard reflects the current document count and recency correctly.

## Reference artifacts

- See [spec.md](spec.md) for the full feature requirements and acceptance criteria.
- See [data-model.md](data-model.md) for the document and access model.
- See [contracts/document-management-contract.md](contracts/document-management-contract.md) for the service and DTO contract definitions.
