```python
# test_backend_spec.py
# This file outlines the test specifications for the ATS backend API.
# It uses pytest-style conventions. Mocks will be required for external
# services (like the database and LTI library).

import pytest

# --- Story 1: LTI Tool Configuration ---

@pytest.mark.usefixtures("db_session", "client")
class TestLtiConfigAPI:
    """Tests for the LTI Tool Configuration endpoints (/api/admin/tools)."""

    def test_admin_can_create_tool_config(self, admin_auth_headers):
        """
        SPEC: An admin user should be able to create a new LTI tool configuration.
        VERIFIES: POST /api/admin/tools returns 201 Created.
        """
        # 1. Define valid tool configuration data.
        # 2. Make a POST request to /api/admin/tools with admin headers.
        # 3. Assert the status code is 201.
        # 4. Assert the response contains the created tool's ID.
        # 5. Assert the configuration is correctly stored in the database.
        pass

    def test_non_admin_cannot_create_tool_config(self, recruiter_auth_headers):
        """
        SPEC: A non-admin user (e.g., a recruiter) should be forbidden from creating a tool config.
        VERIFIES: POST /api/admin/tools returns 403 Forbidden.
        """
        # 1. Make a POST request to /api/admin/tools with recruiter headers.
        # 2. Assert the status code is 403.
        pass

    def test_get_tool_config_does_not_expose_secrets(self, admin_auth_headers, tool_config):
        """
        SPEC: Retrieving a tool configuration should not expose encrypted secrets.
        VERIFIES: GET /api/admin/tools/{tool_id} payload does not contain sensitive data.
        """
        # 1. Create a tool_config in the database with secrets.
        # 2. Make a GET request to /api/admin/tools/{tool_config.id}.
        # 3. Assert the status code is 200.
        # 4. Assert that sensitive fields (e.g., private key, encrypted secrets) are not in the response body.
        pass

    def test_encryption_service_is_called_on_create(self, mocker):
        """
        SPEC: Sensitive details must be encrypted before being stored.
        VERIFIES: The encryption function is called when creating a new tool config.
        """
        # 1. Mock the encryption utility function (e.g., `mocker.patch('utils.encrypt')`).
        # 2. Call the service-layer function responsible for creating the tool config.
        # 3. Assert that the mocked encryption function was called.
        pass


# --- Story 2: Secure LTI Assessment Launch ---

@pytest.mark.usefixtures("db_session", "client")
class TestAssessmentLaunchAPI:
    """Tests for the Secure LTI Assessment Launch endpoint."""

    def test_recruiter_can_launch_assessment(self, recruiter_auth_headers, candidate):
        """
        SPEC: An authenticated recruiter should be able to generate a launch link for a candidate.
        VERIFIES: POST /api/candidates/{id}/assessment/launch returns 200 and a valid URL.
        """
        # 1. Make a POST request with recruiter headers for a valid candidate.id.
        # 2. Assert the status code is 200.
        # 3. Assert the response body contains a 'launch_url'.
        # 4. Assert the URL is a well-formed URL.
        pass

    def test_unauthenticated_user_cannot_launch(self, client, candidate):
        """
        SPEC: An unauthenticated user cannot generate a launch link.
        VERIFIES: POST /api/candidates/{id}/assessment/launch returns 401 Unauthorized.
        """
        # 1. Make a POST request without authentication headers.
        # 2. Assert the status code is 401.
        pass

    def test_launch_for_nonexistent_candidate(self, recruiter_auth_headers):
        """
        SPEC: The system should handle launch requests for candidates that do not exist.
        VERIFIES: POST /api/candidates/999/assessment/launch returns 404 Not Found.
        """
        # 1. Make a POST request for a non-existent candidate ID.
        # 2. Assert the status code is 404.
        pass

    def test_launch_jwt_contains_required_claims(self, mocker, recruiter, candidate, tool_config):
        """
        SPEC: The generated LTI launch request JWT must contain all necessary claims.
        VERIFIES: The JWT payload is correctly constructed by the LTI library wrapper.
        """
        # 1. Mock the `pylti1.3` library's launch method.
        # 2. Call the service-layer function that generates the launch.
        # 3. Capture the arguments passed to the mocked LTI launch method.
        # 4. Assert that the claims dictionary contains:
        #    - 'sub' (recruiter's ID)
        #    - 'https://purl.imsglobal.org/spec/lti/claim/deployment_id'
        #    - 'https://purl.imsglobal.org/spec/lti/claim/context'
        #    - 'https://purl.imsglobal.org/spec/lti/claim/resource_link'
        #    - A custom claim for 'candidate_id'.
        pass


# --- Story 3: Receive Assessment Scores ---

@pytest.mark.usefixtures("db_session", "client")
class TestGradeServiceAPI:
    """Tests for the LTI Assignment and Grade Services (AGS) endpoint."""

    def test_receive_score_with_valid_token(self, mocker, assessment_launch):
        """
        SPEC: The system must accept and process scores from the tool when a valid token is provided.
        VERIFIES: POST /api/lti/ags/lineitems/{id}/scores updates the candidate's score.
        """
        # 1. Mock the LTI token validation utility to return a valid state.
        # 2. Define a valid score payload from the assessment tool.
        # 3. Make a POST request to the AGS endpoint with the payload.
        # 4. Assert the status code is 200.
        # 5. Query the database for the candidate linked to the assessment_launch.
        # 6. Assert the candidate's `assessment_score` and `assessment_status` have been updated.
        pass

    def test_receive_score_with_invalid_token(self, mocker):
        """
        SPEC: The system must reject score updates with an invalid or expired token.
        VERIFIES: POST to AGS endpoint returns 401 Unauthorized.
        """
        # 1. Mock the LTI token validation utility to raise an exception.
        # 2. Make a POST request to the AGS endpoint.
        # 3. Assert the status code is 401 or 403.
        pass


# --- Story 4: Candidate and Results Management ---

@pytest.mark.usefixtures("db_session", "client")
class TestCandidatesAPI:
    """Tests for the Candidate and Results Management endpoints."""

    def test_recruiter_can_get_all_candidates(self, recruiter_auth_headers, candidate):
        """
        SPEC: A recruiter can fetch a list of all candidates.
        VERIFIES: GET /api/candidates returns 200 and a list of candidates.
        """
        # 1. Make a GET request to /api/candidates with recruiter headers.
        # 2. Assert the status code is 200.
        # 3. Assert the response is a list and contains the candidate's data.
        pass

    def test_get_single_candidate_with_results(self, recruiter_auth_headers, candidate_with_score):
        """
        SPEC: A recruiter can fetch a single candidate's profile, including their score.
        VERIFIES: GET /api/candidates/{id} returns a complete candidate profile.
        """
        # 1. Make a GET request to /api/candidates/{candidate.id}.
        # 2. Assert the status code is 200.
        # 3. Assert the response body contains `assessment_status` and `assessment_score`.
        # 4. Assert the values match the `candidate_with_score` fixture.
        pass


# --- Story 5: User Authentication and Authorization ---

@pytest.mark.usefixtures("db_session", "client")
class TestAuthAPI:
    """Tests for user authentication and authorization."""

    def test_user_login_success(self, client, user):
        """
        SPEC: A user with valid credentials can log in and receive a token.
        VERIFIES: POST /api/auth/login returns 200 and an auth token.
        """
        # 1. Make a POST request to /api/auth/login with user's credentials.
        # 2. Assert status code is 200.
        # 3. Assert the response body contains a non-empty 'access_token'.
        pass

    def test_user_login_failure(self, client, user):
        """
        SPEC: A user with invalid credentials cannot log in.
        VERIFIES: POST /api/auth/login returns 401 Unauthorized.
        """
        # 1. Make a POST request with an incorrect password.
        # 2. Assert status code is 401.
        pass

    def test_protected_endpoint_is_inaccessible_without_token(self, client):
        """
        SPEC: Endpoints requiring authentication must be protected.
        VERIFIES: GET /api/candidates returns 401 Unauthorized without a token.
        """
        # 1. Make a GET request to /api/candidates without auth headers.
        # 2. Assert status code is 401.
        pass
```