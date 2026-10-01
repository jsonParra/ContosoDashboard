# Research: Document Upload and Management

## Decision: Store document metadata in EF Core and files on the local filesystem

**Decision**: The application will keep document metadata in the existing EF Core database and store each accepted file on the local filesystem under a secure per-document path (for example, a dedicated uploads folder in the app’s local data area).

**Rationale**: This matches the training requirement to remain fully offline and avoids introducing cloud dependencies. Storing metadata in the relational model allows the app to query access, project membership, and audit data consistently while preserving a simple file-storage abstraction for future Azure migration.

**Alternatives considered**:
- Storing file bytes directly in the database: rejected because it adds complexity and is harder to reason about in a teaching application.
- Using a remote cloud service: rejected because the feature is explicitly offline-first and must operate without external dependencies.

## Decision: Enforce access in the service layer and re-check before every operation

**Decision**: All document reads, updates, deletions, and sharing operations will be gated by a service-level authorization routine that validates the current user’s role, ownership, project membership, and explicit share grants before returning results.

**Rationale**: The app already follows mock-authentication and service-level security patterns. The document feature must preserve the same deny-by-default model and must not leak unauthorized records through search results, task views, dashboard cards, or shared lists.

**Alternatives considered**:
- UI-only hiding: rejected because it is insufficient; a user could still access data by URL or service call.
- Broad project visibility: rejected because it would violate least-privilege and the explicit sharing requirements.

## Decision: Use per-file results and a document status workflow for safety inspection

**Decision**: Multi-file uploads will be processed independently, with each file reporting success, rejection, or pending status. A `DocumentStatus` value will distinguish `Pending`, `Approved`, and `Rejected` for safety inspection outcomes.

**Rationale**: This matches the feature spec’s edge cases and ensures one invalid file does not block valid ones. It also preserves offline-safe behavior when the inspection engine is unavailable or still running.

**Alternatives considered**:
- Single transaction for all files: rejected because it is too coarse and conflicts with the requirement that each file reports independently.
- Making files visible before inspection: rejected because it violates the safety requirement and could expose unverified content.

## Decision: Model document activity as an auditable event stream

**Decision**: Upload, download, deletion, and share actions will each write a `DocumentActivity` record capturing the actor, target document, timestamp, and event type.

**Rationale**: The spec requires administrator reporting and auditable actions. An event model is easy to query and aligns with the existing notification and project activity patterns.

**Alternatives considered**:
- Only logging to UI notifications: rejected because it is not durable or queryable enough for admin reporting.
- Mixing activity into the document record itself: rejected because it makes reporting and history harder to maintain.

## Decision: Keep the design focused on the current app architecture and training constraints

**Decision**: The document feature will use the existing `Models`, `Data`, `Services`, and `Pages` structure without introducing new infrastructure frameworks or cloud concepts.

**Rationale**: This matches the repo’s “simple and teachable” design principle and keeps the feature approachable for the training environment. The abstraction can be upgraded later if the app moves to Azure without changing the document business rules.

**Alternatives considered**:
- Full domain-driven design or event-sourcing architecture: rejected because it exceeds the scope of this feature and the project’s educational focus.
- Introducing a separate microservice boundary: rejected because the app is a single-process Blazor Server application for training.
