# Tasks: Document Upload and Management

**Input**: Design documents from `/specs/001-document-management/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Align the current Blazor server app with the document feature structure and local storage needs.

- [ ] T001 Create feature implementation structure for document models, services, and UI extensions under ContosoDashboard/Models/, ContosoDashboard/Services/, and ContosoDashboard/Pages/
- [ ] T002 [P] Add local document storage configuration, application settings, and the file-system path strategy in ContosoDashboard/Program.cs and ContosoDashboard/appsettings.json
- [ ] T003 [P] Define document-related enums and validation constants that match the approved categories and file requirements in ContosoDashboard/Models/

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Establish the data model, storage abstraction, and authorization primitives before any user story work begins.

- [ ] T004 Extend ContosoDashboard/Data/ApplicationDbContext.cs to register Document, DocumentAccessGrant, and DocumentActivity entities and relationships
- [ ] T005 [P] Create the document storage abstraction and local file persistence service in ContosoDashboard/Services/IFileStorageService.cs and ContosoDashboard/Services/LocalFileStorageService.cs
- [ ] T006 [P] Create the core document service contract and authorization helpers in ContosoDashboard/Services/IDocumentService.cs and ContosoDashboard/Services/DocumentService.cs
- [ ] T007 Implement validation of title, category, file size, supported type, and pending safety status in ContosoDashboard/Services/DocumentService.cs
- [ ] T008 Add audit-event recording and permission recheck support for document actions in ContosoDashboard/Services/DocumentService.cs and ContosoDashboard/Models/DocumentActivity.cs
- [ ] T009 [P] Wire document dependencies into dependency injection and app startup in ContosoDashboard/Program.cs

**Checkpoint**: Foundation ready - user story implementation can begin.

---

## Phase 3: User Story 1 - Upload and organize documents (Priority: P1) 🎯 MVP

**Goal**: Allow employees to upload valid documents with metadata, retain file safety status, and keep each upload result independent.

**Independent Test**: Upload a valid supported file with required metadata, then verify it appears in the uploader's documents with its metadata and is available to authorized viewers.

### Implementation for User Story 1

- [ ] T010 [P] [US1] Create `Document` model in ContosoDashboard/Models/Document.cs with required fields for title, category, tags, uploader, project/task association, file size, type, and safety status
- [ ] T011 [P] [US1] Create `DocumentAccessGrant` model in ContosoDashboard/Models/DocumentAccessGrant.cs for explicit user/team sharing relationships
- [ ] T012 [US1] Implement multi-file upload validation and per-file success/rejection/pending outcome handling in ContosoDashboard/Services/DocumentService.cs
- [ ] T013 [US1] Add document creation, persistence, and upload-record generation for accepted files in ContosoDashboard/Services/DocumentService.cs
- [ ] T014 [US1] Add upload UI and single-page document form in ContosoDashboard/Pages/Documents.razor or the relevant existing page that supports project/task uploads
- [ ] T015 [US1] Implement unauthorized project upload rejection and project-association validation in ContosoDashboard/Services/DocumentService.cs
- [ ] T016 [US1] Ensure rejected or pending files remain inaccessible until inspection passes and inform the uploader with a clear status in ContosoDashboard/Services/DocumentService.cs

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently.

---

## Phase 4: User Story 2 - Find and access authorized documents (Priority: P1)

**Goal**: Provide search, sorting, filtering, and access-controlled preview/download for permitted documents.

**Independent Test**: Populate documents with varied metadata and access scopes, then verify that a user can find and open permitted documents while documents outside their access remain undiscoverable.

### Implementation for User Story 2

- [ ] T017 [P] [US2] Create document query and summary logic for user-owned, project, and shared views in ContosoDashboard/Services/DocumentService.cs
- [ ] T018 [US2] Implement search, sorting, and filtering by title, category, project, uploader, tags, and date range in ContosoDashboard/Services/DocumentService.cs
- [ ] T019 [P] [US2] Add the document browser and shared-with-me UI in ContosoDashboard/Pages/Documents.razor and the relevant project/task views
- [ ] T020 [US2] Enforce access checks for document lists, search results, preview, and download paths in ContosoDashboard/Services/DocumentService.cs
- [ ] T021 [US2] Implement browser preview for PDF and image documents and download support for authorized files in ContosoDashboard/Services/DocumentService.cs and ContosoDashboard/Pages/
- [ ] T022 [US2] Add the empty-state and access-denied handling for searches with no valid matches in ContosoDashboard/Pages/Documents.razor

**Checkpoint**: At this point, User Stories 1 and 2 should both work independently.

---

## Phase 5: User Story 3 - Maintain and share documents (Priority: P2)

**Goal**: Allow document owners and authorized managers to manage metadata, replace files, delete documents, and share access with recipients.

**Independent Test**: As an owner, edit metadata, replace a file, share it with a recipient, verify the recipient's notification and access, then delete the document after confirmation. Separately verify manager and team-lead permissions are limited to their authorized scope.

### Implementation for User Story 3

- [ ] T023 [P] [US3] Add metadata edit and file-replacement workflow with validation and retained associations in ContosoDashboard/Services/DocumentService.cs
- [ ] T024 [US3] Implement document deletion confirmation and role-scoped management checks in ContosoDashboard/Services/DocumentService.cs
- [ ] T025 [P] [US3] Add share-grant logic and recipient access creation in ContosoDashboard/Services/DocumentService.cs and ContosoDashboard/Models/DocumentAccessGrant.cs
- [ ] T026 [US3] Trigger in-app notifications for shared documents and record DocumentActivity events in ContosoDashboard/Services/NotificationService.cs and ContosoDashboard/Services/DocumentService.cs
- [ ] T027 [P] [US3] Add management controls in the document UI for edit, replace, share, and delete in ContosoDashboard/Pages/Documents.razor and ContosoDashboard/Pages/ProjectDetails.razor
- [ ] T028 [US3] Re-check authorization before every document mutation and deny actions when permission is removed in ContosoDashboard/Services/DocumentService.cs

**Checkpoint**: At this point, User Stories 1, 2, and 3 should all be independently functional.

---

## Phase 6: User Story 4 - Use documents in project work and oversight (Priority: P3)

**Goal**: Connect documents to project workflows, dashboard recency, notifications, and reporting without breaking access boundaries.

**Independent Test**: Add a document to a project task, verify its project association and visibility to authorized project members, then verify dashboard recency/counts, notifications, and administrator report data.

### Implementation for User Story 4

- [ ] T029 [P] [US4] Add task-attachment and upload-from-task association logic in ContosoDashboard/Services/DocumentService.cs and ContosoDashboard/Pages/Tasks.razor
- [ ] T030 [US4] Implement dashboard recency and document-count calculations in ContosoDashboard/Services/DashboardService.cs
- [ ] T031 [P] [US4] Add project-membership notification triggers for newly uploaded project documents in ContosoDashboard/Services/NotificationService.cs
- [ ] T032 [US4] Add admin activity reporting for uploads, downloads, deletions, and sharing in ContosoDashboard/Services/DashboardService.cs or ContosoDashboard/Services/DocumentService.cs
- [ ] T033 [P] [US4] Expose the dashboard and reporting views in ContosoDashboard/Pages/Index.razor and the relevant project/task screens
- [ ] T034 [US4] Ensure activity records capture actor, document, action type, and event time for all document-related operations in ContosoDashboard/Models/DocumentActivity.cs and ContosoDashboard/Services/DocumentService.cs

**Checkpoint**: All user stories should now be independently functional.

---

## Phase 7: Polish & Cross-Cutting Concerns

**Purpose**: Final validation, security audit, and documentation around the document feature.

- [ ] T035 [P] Update the project documentation and quickstart guidance in README.md and specs/001-document-management/quickstart.md
- [ ] T036 [P] Review all document service code paths for authorization, validation, and offline-first constraints in ContosoDashboard/Services/
- [ ] T037 Run the end-to-end validation checklist from specs/001-document-management/quickstart.md and confirm behavior matches the feature requirements
- [ ] T038 [P] Clean up duplicate logic and align UI patterns with the existing Blazor server structure in ContosoDashboard/Pages/ and ContosoDashboard/Services/

---

## Dependencies & Execution Order

### Phase Dependencies

- Setup (Phase 1): No dependencies - can start immediately
- Foundational (Phase 2): Depends on Setup completion and blocks all user stories
- User Story 1 (Phase 3): Depends on Foundational completion
- User Story 2 (Phase 4): Depends on Foundational completion and can run alongside UI work from US1
- User Story 3 (Phase 5): Depends on Foundational completion and uses the same service boundary
- User Story 4 (Phase 6): Depends on the document service foundation and may integrate with previous stories
- Polish (Phase 7): Depends on all desired user stories being complete

### Parallel Opportunities

- T002 and T003 can run in parallel during setup
- T005, T006, and T009 can run in parallel after the schema and app startup are ready
- US1 tasks T010, T011, T014, and T015 can be scheduled in parallel when the service boundary is established
- US2 tasks T017, T019, and T021 can be developed in parallel once document query logic exists
- US3 tasks T023, T025, and T027 can be parallelized by different contributors once the document service contract is stable
- US4 tasks T029, T030, and T031 can be developed in parallel after the document permissions and notification flow exist

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational
3. Complete Phase 3: User Story 1
4. Validate independently and stop before expanding scope

### Incremental Delivery

1. Setup + Foundational
2. User Story 1: upload and organize documents
3. User Story 2: access and discovery
4. User Story 3: maintenance and sharing
5. User Story 4: project and oversight integration
6. Polish and validation

### Parallel Team Strategy

With multiple developers:

1. One developer handles the document data model and local storage infrastructure
2. One developer implements the document service and authorization checks
3. One developer builds the UI surfaces and project/task integration
4. One developer focuses on notification, dashboard, and reporting integration

This keeps story work independent while preserving a shared foundational contract.
