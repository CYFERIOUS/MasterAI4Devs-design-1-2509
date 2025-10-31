# ATS Backend User Stories

This document lists the key user stories for the development of the Applicant Tracking System (ATS) backend, based on the architecture defined in `Technical_Architecture.md`. These stories focus on the server-side logic required to support the LTI integration with the external Assessment Tool and to provide the necessary data for the ATS frontend.

---

### Story 1: LTI Tool Configuration

*   **As a** System Administrator,
*   **I want to** securely configure and store the connection details for an external LTI Assessment Tool (e.g., client ID, issuer URL, deployment ID),
*   **so that** the ATS can establish a trusted relationship and initiate secure communication with the tool.

**Backend Implementation Notes:**
*   Create a database table (e.g., `lti_tool_configurations`) to store LTI 1.3 platform and deployment details.
*   Develop internal API endpoints (e.g., `POST /api/admin/tools`, `GET /api/admin/tools/{tool_id}`) for creating, viewing, and managing these configurations.
*   Ensure sensitive details are encrypted at rest.
*   The system should be able to manage configurations for multiple external tools.

---

### Story 2: Secure LTI Assessment Launch

*   **As a** Recruiter,
*   **I want** the system to generate a secure, one-time launch link when I decide to send an assessment to a candidate,
*   **so that** the candidate can be seamlessly and securely authenticated into the Assessment Tool with the correct context.

**Backend Implementation Notes:**
*   Create a backend endpoint (e.g., `POST /api/candidates/{candidate_id}/assessment/launch`).
*   This endpoint will use the `pylti1.3` library to generate an OIDC login initiation request.
*   It must construct a JWT containing the necessary LTI claims, including:
    *   `sub`: User ID (recruiter who initiated).
    *   `https://purl.imsglobal.org/spec/lti/claim/deployment_id`: The specific tool deployment ID.
    *   `https://purl.imsglobal.org/spec/lti/claim/context`: Contextual information (e.g., job opening ID).
    *   `https://purl.imsglobal.org/spec/lti/claim/resource_link`: Information about the specific assessment to be launched.
    *   Custom claims for passing the unique `candidate_id`.
*   The endpoint will return a redirect URL to the frontend, which will then direct the user (or open a new window) to the Assessment Tool.

---

### Story 3: Receive Assessment Scores

*   **As a** System,
*   **I need to** expose a secure endpoint to receive assessment scores and completion data from the external Assessment Tool,
*   **so that** a candidate's profile can be automatically updated with their results in real-time.

**Backend Implementation Notes:**
*   Implement the LTI Advantage Assignment and Grade Services (AGS) endpoint. This is typically a `POST` endpoint like `/api/lti/ags/lineitems/{lineitem_id}/scores`.
*   This endpoint must be protected by OAuth 2.0, validating the JWT bearer token sent by the Assessment Tool.
*   On receiving a valid request, the backend will parse the score and status from the request body.
*   It will then look up the candidate associated with the `lineitem` (which was created as part of the launch context) and update their record in the `candidates` database table with the new score.

---

### Story 4: Candidate and Results Management

*   **As a** Recruiter,
*   **I want to** access a candidate's profile and see their assessment status (e.g., pending, in-progress, completed) and their final score,
*   **so that** I can efficiently track progress and make informed hiring decisions.

**Backend Implementation Notes:**
*   Develop standard CRUD API endpoints for managing candidates (e.g., `GET /api/candidates`, `GET /api/candidates/{id}`).
*   The `candidates` database model must include fields for `assessment_status` and `assessment_score`.
*   The `GET` endpoints for candidates must join or lookup this data to provide a complete view of the candidate's profile to the frontend.

---

### Story 5: User Authentication and Authorization

*   **As a** System,
*   **I need to** manage recruiter accounts and ensure that only authenticated users can perform actions,
*   **so that** candidate data remains secure and private.

**Backend Implementation Notes:**
*   Implement a user authentication system (e.g., using JWTs or sessions).
*   Create endpoints for login (`/api/auth/login`), logout (`/api/auth/logout`), and fetching user profiles (`/api/users/me`).
*   Protect all relevant API endpoints (like launching assessments and viewing candidate data) to ensure they can only be accessed by authenticated and authorized recruiters.
