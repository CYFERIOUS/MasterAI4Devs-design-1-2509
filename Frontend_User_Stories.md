# ATS Frontend User Stories

This document provides the key user stories for the development of the Applicant Tracking System (ATS) frontend. These stories are based on the `Technical_Architecture.md` and are written from the perspective of the end-user (a recruiter or administrator). They describe the necessary UI components and interactions for a seamless and efficient user experience.

---

### Story 1: Recruiter Authentication

*   **As a** Recruiter,
*   **I want to** see a professional login page where I can securely enter my credentials,
*   **so that** I can access my candidate dashboard.

**Frontend Implementation Notes:**
*   Create a dedicated `/login` route using `react-router-dom`.
*   Design a simple and clean login form using `Material-UI (MUI)` components (`TextField`, `Button`, `Card`).
*   Use a form management library like `React Hook Form` for validation.
*   On form submission, use a `React Query` mutation to call the backend's `/api/auth/login` endpoint.
*   Securely store the returned authentication token and redirect the user to the main dashboard upon success.
*   Implement protected routes to prevent unauthenticated access to the rest of the application.

---

### Story 2: Candidate Dashboard & Status Overview

*   **As a** Recruiter,
*   **I want to** view a central dashboard that lists all my candidates and their current assessment status at a glance,
*   **so that** I can easily track the entire hiring pipeline.

**Frontend Implementation Notes:**
*   Create a main dashboard page as the default route after login.
*   Use `React Query`'s `useQuery` hook to fetch the list of candidates from the `/api/candidates` endpoint.
*   Display the data in an `MUI DataGrid` or `Table` component.
*   Columns should include Candidate Name, Job Position, Date Applied, and Assessment Status.
*   Use `MUI Chip` components with different colors and icons to visually represent the status (e.g., Grey for "Not Sent", Blue for "Sent", Green for "Completed").
*   The table should be sortable and filterable.

---

### Story 3: Initiating an Assessment

*   **As a** Recruiter,
*   **I want to** be able to send an assessment to a candidate with a single click from the dashboard,
*   **so that** I can efficiently move candidates to the next stage.

**Frontend Implementation Notes:**
*   In the candidate data grid, add a column with an action button (e.g., an `MUI Button` with a "Send Assessment" label or a send icon).
*   The button should be disabled with a tooltip if the status is not "Not Sent".
*   On click, trigger a `React Query` mutation that calls the backend endpoint `POST /api/candidates/{candidate_id}/assessment/launch`.
*   The backend will respond with a URL. The frontend should then open this URL in a new browser tab using `window.open(url, '_blank')`.
*   After the mutation is successful, `React Query` should automatically refetch the candidate list to update the status to "Sent".

---

### Story 4: Viewing Candidate Profile and Assessment Results

*   **As a** Recruiter,
*   **I want to** click on a candidate in the dashboard to navigate to a detailed profile page where I can see their score,
*   **so that** I can review their performance in detail.

**Frontend Implementation Notes:**
*   Make each candidate's name in the dashboard a link to their profile page (e.g., `/candidates/{id}`).
*   On the profile page, use `React Query` to fetch the specific candidate's data from `/api/candidates/{id}`.
*   Display the assessment score prominently using `MUI` components like `Card`, `Typography`, and a `CircularProgress` with a label to create a visually appealing score indicator.
*   Show "Result Pending" or a similar message if the score is not yet available.
*   This page can also contain other candidate details (e.g., contact info, resume link).

---

### Story 5: LTI Tool Management (Admin)

*   **As an** Administrator,
*   **I want** a secure settings page where I can add or edit the configuration for an LTI Assessment Tool,
*   **so that** the platform can correctly connect to and trust the external tool.

**Frontend Implementation Notes:**
*   Create an admin-only route (e.g., `/admin/settings`) protected by role-based access control.
*   Design a form using `MUI` components for inputting LTI configuration fields (Client ID, Issuer URL, etc.).
*   Use `React Query` mutations to send the form data to the backend's admin endpoints (`POST /api/admin/tools`, `PUT /api/admin/tools/{tool_id}`).
*   Display a list of currently configured tools and allow for editing or deleting them.
