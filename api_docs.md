# CC API Documentation

## Authentication
Most endpoints require authentication using the `@login_required` decorator. You can obtain an authentication token using the `/api/token` endpoint.

## Base URL
All endpoints are prefixed with `/api`

## Endpoints

### Authentication & Session Management

#### Get API Health Status
```
GET /health
```
Returns the API health status.

**Response**
```json
{
    "message": "ok"
}
```

#### Generate API Token
```
GET /token
```
Generates an authentication token for the current user.

**Query Parameters**
- `expiration` (optional): Token expiration time in seconds (default: 600)

**Response**
```json
{
    "token": "your-auth-token",
    "expires_in": 600
}
```

### Session Management

#### Get Current Session
```
GET /session
```
Returns the current session information.

**Response**
```json
{
    "tenant-id": "current-tenant-id",
    "tenant-uuid": "current-tenant-uuid"
}
```

#### Set Session
```
PUT /session/{id}
```
Sets the current session for a specific tenant.

**Response**
```json
{
    "message": "ok"
}
```

#### Delete Session
```
GET /session/delete
```
Clears the current session.

**Response**
```json
{
    "message": "ok"
}
```

### Questionnaire Management

#### Get Questionnaire
```
GET /questionnaires/{qid}
```
Retrieves a specific questionnaire.

**Query Parameters**
- `available-guests` (optional): Include available guests in response

**Response**
Returns questionnaire details and optionally available guests.

#### Get Questionnaire Guests
```
GET /questionnaires/{qid}/guests
```
Retrieves available guests for a specific questionnaire.

#### Publish Questionnaire
```
PUT /questionnaires/{qid}/publish
```
Updates the publication status of a questionnaire.

**Request Body**
```json
{
    "enabled": boolean
}
```

#### Update Questionnaire Guests
```
PUT /questionnaires/{qid}/guests
```
Updates the guest list for a questionnaire.

**Request Body**
```json
{
    "guests": ["guest-list"]
}
```

#### Update Questionnaire Form
```
PUT /questionnaires/{qid}/form
```
Updates the form content of a questionnaire.

**Request Body**
```json
{
    "form": {
        // form content
    }
}
```

### Project Management

#### Generate Project Report
```
POST /projects/{id}/reports
```
Generates a report for a specific project.

**Response**
```json
{
    "name": "report-name"
}
```

#### Scratchpad Operations
```
GET /projects/{id}/scratchpad
```
Retrieves project scratchpad notes.

```
PUT /projects/{id}/scratchpad
```
Updates project scratchpad content.

**Request Body**
```json
{
    "data": "scratchpad-content"
}
```

### Project Comments

#### Add Comment
```
POST /projects/{id}/comments
```
Adds a comment to a project.

**Request Body**
```json
{
    "data": "comment-content"
}
```

**Features**
- Supports user mentions
- Sends email notifications to mentioned users

#### Delete Comment
```
DELETE /projects/{pid}/comments/{cid}
```
Deletes a specific comment from a project.

#### Get Comments
```
GET /projects/{pid}/comments
```
Retrieves all comments for a project.

### Project Matrix

#### Get Matrix Summary
```
GET /projects/{pid}/matrix/summary
```
Retrieves a summary of the responsibility matrix.

**Response**
```json
{
    "total": number,
    "owners": [
        {
            "email": "user-email",
            "user_id": number,
            "subcontrols": number
        }
    ],
    "operators": [
        // similar structure as owners
    ]
}
```

#### Get User Matrix
```
GET /projects/{pid}/matrix/users/{uid}
```
Retrieves matrix information for a specific user.

### Project Members

#### Get Project Members
```
GET /projects/{pid}/members
```
Retrieves all members of a project with their access levels.

#### Add Project Members
```
POST /projects/{pid}/members
```
Adds new members to a project.

**Request Body**
```json
{
    "members": [
        {
            "id": "user-id"
        }
    ]
}
```

## Error Handling

The API uses standard HTTP status codes:
- 200: Success
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 500: Internal Server Error

## Authorization

Most endpoints require specific authorization checks using the `Authorizer` class. The following permissions are checked:
- User access to tenant
- User admin privileges for tenant
- User management privileges for projects
- User read/write access to project components



[Previous sections remain the same up to Project Management]

### Project Operations

#### Get Project Details
```
GET /projects/{pid}
```
Retrieves details for a specific project.

**Query Parameters**
- `review-summary` (optional): Include review summary in response (boolean)

**Response**
Returns project details including review summary if requested.

#### Delete Project
```
DELETE /projects/{pid}
```
Deletes a specific project.

#### Get Project Controls
```
GET /projects/{pid}/controls
```
Retrieves controls associated with a project.

**Query Parameters**
- `view` (optional): Filter controls by status
  - `with-evidence`: Controls with evidence
  - `missing-evidence`: Controls without evidence
  - `not-implemented`: Unimplemented controls
  - `implemented`: Fully implemented controls
  - `applicable`: Applicable controls
  - `not-applicable`: Non-applicable controls
  - `complete`: Completed controls
  - `not-complete`: Incomplete controls
- `stats` (optional): Include statistics (boolean)

#### Get Project Policies
```
GET /projects/{pid}/policies
```
Retrieves all policies associated with a project.

#### Get Specific Project Policy
```
GET /projects/{pid}/policies/{ppid}
```
Retrieves a specific policy for a project.

#### Update Project Policy
```
PUT /projects/{pid}/policies/{ppid}
```
Updates a specific policy in a project.

**Request Body**
```json
{
    "content": "policy-content",
    "public_viewable": boolean,
    "owner_id": "owner-id",
    "reviewer_id": "reviewer-id"
}
```

#### Delete Project Policy
```
DELETE /projects/{pid}/policies/{ppid}
```
Removes a policy from a project.

### Policy Management

#### Get Policy
```
GET /policies/{pid}
```
Retrieves a specific policy.

#### Update Policy
```
PUT /policies/{pid}
```
Updates a policy.

**Request Body**
```json
{
    "name": "policy-name",
    "ref_code": "reference-code",
    "description": "policy-description",
    "template": "policy-template",
    "content": "policy-content"
}
```

#### Delete Policy
```
DELETE /policies/{pid}
```
Marks a policy as not visible.

#### Update Policy Controls
```
PUT /policies/{pid}/controls/{cid}
```
Associates a control with a policy.

### Framework Management

#### Get Framework
```
GET /frameworks/{fid}
```
Retrieves a specific framework.

### Evidence Management

#### Get Evidence
```
GET /evidence/{eid}
```
Retrieves specific evidence.

#### Add Evidence
```
POST /tenants/{tid}/evidence
```
Adds new evidence to a tenant.

**Form Data**
- `name`: Evidence name
- `description`: Evidence description
- `content`: Evidence content
- `collected`: Collection date (MM/DD/YYYY)
- `file`: Evidence files (multipart/form-data)

#### Update Evidence
```
PUT /evidence/{eid}
```
Updates existing evidence.

**Form Data**
- `name`: Evidence name
- `description`: Evidence description
- `content`: Evidence content
- `collected`: Collection date (optional)
- `file`: Evidence files (multipart/form-data)

#### Delete Evidence
```
DELETE /evidence/{eid}
```
Deletes specific evidence.

#### Associate Evidence with Controls
```
PUT /evidence/{eid}/controls
```
Associates evidence with controls.

### Control Management

#### Get Control
```
GET /controls/{cid}
```
Retrieves a specific control.

#### Delete Control
```
DELETE /controls/{cid}
```
Marks a control as not visible.

## Authorization

Most endpoints require specific authorization checks through the `Authorizer` class:
- `can_user_access_project`
- `can_user_manage_project`
- `can_user_read_policy`
- `can_user_manage_policy`
- `can_user_read_framework`
- `can_user_read_evidence`
- `can_user_manage_evidence`
- `can_user_manage_control`
- `can_user_read_control`
- `can_user_read_project_policy`
- `can_user_manage_project_policy`
- `can_user_delete_policy_from_project`

## Response Format
All endpoints return JSON responses. Success responses include either requested data or a success message:

```json
{
    "message": "ok"
}
```

## Error Handling
The API uses standard HTTP status codes:
- 200: Success
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 500: Internal Server Error

