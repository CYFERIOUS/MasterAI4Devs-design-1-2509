# ATS Frontend QA Guide

## 1. Introduction

This document provides a comprehensive set of test cases for the Applicant Tracking System (ATS) frontend. The goal is to ensure that the application meets the requirements defined in the user stories, providing a stable, reliable, and seamless user experience for recruiters and administrators.

## 2. General Test Scenarios

| Test Case ID | Test Scenario | Test Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **GEN-001** | Browser Compatibility | 1. Open the application in Chrome. <br> 2. Open in Firefox. <br> 3. Open in Safari. <br> 4. Open in Edge. | The application renders correctly and is fully functional across all major modern browsers. |
| **GEN-002** | Responsive Design | 1. Open the application on a desktop. <br> 2. Resize the browser window to tablet size. <br> 3. Resize to mobile size. | The layout adjusts gracefully to different screen sizes without breaking or hiding critical information. |
| **GEN-003** | Protected Routes | 1. Log out of the application. <br> 2. Attempt to navigate directly to the dashboard URL (e.g., `/`). <br> 3. Attempt to navigate to a candidate profile (e.g., `/candidates/1`). | The user is redirected to the `/login` page for all protected routes. |

---

## 3. Test Cases per User Story

### Story 1: Recruiter Authentication

| Test Case ID | Test Scenario | Test Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **AUTH-001** | Successful Login | 1. Navigate to the `/login` page. <br> 2. Enter valid recruiter credentials. <br> 3. Click the "Login" button. | The user is successfully authenticated and redirected to the main candidate dashboard. |
| **AUTH-002** | Invalid Credentials | 1. Navigate to the `/login` page. <br> 2. Enter invalid credentials (wrong password or username). <br> 3. Click "Login". | An error message is displayed on the screen (e.g., "Invalid username or password"). The user remains on the login page. |
| **AUTH-003** | Form Validation | 1. Navigate to `/login`. <br> 2. Leave the username and/or password fields empty. <br> 3. Click "Login". | Validation messages appear below the empty fields, and the login request is not sent. |
| **AUTH-004** | Logout | 1. Log in successfully. <br> 2. Find and click the "Logout" button. | The user is logged out, the session/token is cleared, and they are redirected to the `/login` page. |

### Story 2: Candidate Dashboard & Status Overview

| Test Case ID | Test Scenario | Test Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **DASH-001** | View Candidate List | 1. Log in as a recruiter. <br> 2. Observe the main dashboard. | A data grid or table is displayed, populated with a list of candidates. A loading indicator is shown while data is being fetched. |
| **DASH-002** | Correct Table Columns | 1. Examine the candidate table. | The table contains the following columns: Candidate Name, Job Position, Date Applied, and Assessment Status. |
| **DASH-003** | Visual Status Indicator | 1. Look at the "Assessment Status" column. | The status is represented by a colored chip: Grey for "Not Sent", Blue for "Sent", Green for "Completed". |
| **DASH-004** | Sort Functionality | 1. Click on the header of each column (Name, Date, Status). | The table rows are sorted correctly (ascending/descending) based on the selected column. |
| **DASH-005** | Filter Functionality | 1. Use the table's filter controls to filter by candidate name or status. | The table data is filtered in real-time to show only the matching rows. |

### Story 3: Initiating an Assessment

| Test Case ID | Test Scenario | Test Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **ASSESS-001** | Send Assessment | 1. On the dashboard, find a candidate with the status "Not Sent". <br> 2. Click the "Send Assessment" button for that candidate. | A new browser tab opens with the LTI assessment launch URL. The candidate's status on the dashboard updates to "Sent". |
| **ASSESS-002** | Disabled Send Button | 1. On the dashboard, find a candidate whose status is "Sent" or "Completed". <br> 2. Hover over the "Send Assessment" button. | The button is disabled. A tooltip appears explaining why (e.g., "Assessment already sent"). |
| **ASSESS-003** | Status Update on Send | 1. Send an assessment to a candidate. <br> 2. Observe the candidate's row in the dashboard. | The status immediately (or after a brief loading state) changes from "Not Sent" to "Sent" without requiring a manual page refresh. |

### Story 4: Viewing Candidate Profile and Assessment Results

| Test Case ID | Test Scenario | Test Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **PROF-001** | Navigate to Profile | 1. On the dashboard, click the name of any candidate. | The application navigates to the candidate's detailed profile page (e.g., `/candidates/{id}`). |
| **PROF-002** | View Score | 1. Navigate to the profile of a candidate with a "Completed" status. | The assessment score is displayed clearly and prominently, ideally with a visual indicator like a circular progress bar. |
| **PROF-003** | Pending Result | 1. Navigate to the profile of a candidate whose status is "Sent" but not "Completed". | A message like "Result Pending" or "In Progress" is displayed instead of a score. |
| **PROF-004** | Correct Profile Data | 1. Navigate to a candidate's profile. | All displayed information (name, contact info, etc.) matches the data for the selected candidate. |

### Story 5: LTI Tool Management (Admin)

| Test Case ID | Test Scenario | Test Steps | Expected Result |
| :--- | :--- | :--- | :--- |
| **ADMIN-001** | Role-Based Access | 1. Log in as a recruiter (non-admin). <br> 2. Attempt to navigate to `/admin/settings`. | Access is denied, and the user is redirected (e.g., to the dashboard or a "Not Authorized" page). |
| **ADMIN-002** | Admin Access | 1. Log in as an administrator. <br> 2. Navigate to `/admin/settings`. | The LTI Tool Management page loads successfully. |
| **ADMIN-003** | Add New LTI Tool | 1. On the settings page, fill out the form with valid LTI tool details. <br> 2. Click "Save" or "Add Tool". | The new tool configuration is saved, and it appears in the list of configured tools on the same page. |
| **ADMIN-004** | Edit LTI Tool | 1. Click the "Edit" button for an existing tool. <br> 2. Modify one of the fields. <br> 3. Click "Save". | The form is pre-filled with the tool's data. After saving, the updated information is reflected in the list. |
| **ADMIN-005** | Form Validation | 1. Attempt to submit the "Add Tool" form with required fields left empty. | Validation errors are displayed, and the form is not submitted. |
