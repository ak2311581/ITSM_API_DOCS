# Change Request API Documentation

This document provides comprehensive API documentation for all changerequest models, including form-data payloads for POST, PUT, and PATCH operations with file upload support.

## Base URL
```
/api/changerequest/
```

## Authentication
All endpoints require authentication. Include the Authorization header:
```
Authorization: Bearer <your-token>
```

---

## 1. ChangeRequest API

### Endpoints
- `GET /api/changerequest/` - List all change requests
- `POST /api/changerequest/` - Create new change request
- `GET /api/changerequest/{id}/` - Get specific change request
- `PUT /api/changerequest/{id}/` - Update change request (full)
- `PATCH /api/changerequest/{id}/` - Update change request (partial)
- `DELETE /api/changerequest/{id}/` - Delete change request
- `GET /api/changerequest/{id}/attachments/` - List attachments
- `POST /api/changerequest/{id}/upload_attachment/` - Upload attachment
- `POST /api/changerequest/{id}/remove_attachment/` - Remove attachment

### POST /api/changerequest/
**Content-Type:** `multipart/form-data`

**Note:** The customer field is automatically assigned from `request.user.customer` if not explicitly provided. You can override this by explicitly setting the customer field.

#### Required Fields
```
change_title: "Database Performance Optimization"
change_type: "NORMAL"  # Choices: STANDARD, NORMAL, EMERGENCY, AUTO_REMEDIATION
category: "SOFTWARE"   # Choices: HARDWARE, SOFTWARE, NETWORK, SAP_APPLICATION, OTHER_APPLICATIONS
priority: "HIGH"       # Choices: LOW, MEDIUM, HIGH, CRITICAL
impact: "DEPARTMENT"   # Choices: INDIVIDUAL, TEAM, DEPARTMENT, ENTERPRISE
urgency: "HIGH"        # Choices: LOW, MEDIUM, HIGH
business_justification: "Database queries are running slow, affecting user productivity"
initiated_by: "USER"   # Choices: USER, AI_AGENT, MONITORING_EVENT, AUTOMATION_RUNBOOK
```

#### Optional Fields
```
subcategory: "Database Optimization"
source_event_id: "EVT-12345"
correlation_id: "CORR-67890"
customer: "01J6ABCDEFGHIJK123456789AB"  # Customer ID (ULID format) - auto-assigned from request.user.customer if not provided
requester_user: 1  # User ID (auto-set to current user if not provided)
change_manager: 2  # User ID
ai_recommendation: "Consider indexing the customer table"
status: "DRAFT"    # Choices: DRAFT, SUBMITTED, IN_REVIEW, APPROVED, REJECTED, SCHEDULED, IN_PROGRESS, COMPLETED, CANCELLED, FAILED
```

#### File Upload Fields
```
uploaded_files: [file1, file2, file3]  # Multiple files
remove_files: ["attachment-id-1", "attachment-id-2", "/media/path/to/file.pdf"]  # Files to remove
```

#### Example cURL
```bash
# Example 1: Customer auto-assigned from request.user.customer (recommended)
curl -X POST "http://localhost:8000/api/changerequest/" \
  -H "Authorization: Bearer your-token" \
  -F "change_title=Database Performance Optimization" \
  -F "change_type=NORMAL" \
  -F "category=SOFTWARE" \
  -F "priority=HIGH" \
  -F "impact=DEPARTMENT" \
  -F "urgency=HIGH" \
  -F "business_justification=Database queries are running slow" \
  -F "initiated_by=USER" \
  -F "uploaded_files=@/path/to/file1.pdf" \
  -F "uploaded_files=@/path/to/file2.doc"

# Example 2: Explicitly specify customer (overrides auto-assignment)
curl -X POST "http://localhost:8000/api/changerequest/" \
  -H "Authorization: Bearer your-token" \
  -F "change_title=Database Performance Optimization" \
  -F "change_type=NORMAL" \
  -F "category=SOFTWARE" \
  -F "priority=HIGH" \
  -F "impact=DEPARTMENT" \
  -F "urgency=HIGH" \
  -F "business_justification=Database queries are running slow" \
  -F "initiated_by=USER" \
  -F "customer=01J6ABCDEFGHIJK123456789AB" \
  -F "uploaded_files=@/path/to/file1.pdf" \
  -F "uploaded_files=@/path/to/file2.doc"
```

### PUT/PATCH /api/changerequest/{id}/
**Content-Type:** `multipart/form-data`

Same fields as POST, but all fields are optional for PATCH. PUT requires all fields.

#### Additional Fields for Update Operations
```
remove_files: ["12345678-1234-1234-1234-123456789abc", "old_document.pdf"]  # Files to remove
```

#### Example cURL for Update with File Management
```bash
curl -X PATCH "http://localhost:8000/api/changerequest/12345/" \
  -H "Authorization: Bearer your-token" \
  -F "status=IN_PROGRESS" \
  -F "remove_files=12345678-1234-1234-1234-123456789abc" \
  -F "remove_files=old_document.pdf" \
  -F "uploaded_files=@/path/to/new_file.pdf"
```

---

## 2. ChangeImpactAssessment API

### Endpoints
- `GET /api/change-impact-assessments/` - List all assessments
- `POST /api/change-impact-assessments/` - Create assessment
- `GET /api/change-impact-assessments/{change_id}/` - Get assessment
- `PUT /api/change-impact-assessments/{change_id}/` - Update assessment (full)
- `PATCH /api/change-impact-assessments/{change_id}/` - Update assessment (partial)
- `GET /api/change-impact-assessments/{change_id}/attachments/` - List attachments
- `POST /api/change-impact-assessments/{change_id}/upload_attachment/` - Upload attachment

### POST /api/change-impact-assessments/
**Content-Type:** `multipart/form-data`

#### Required Fields
```
change: "change-uuid-here"  # ChangeRequest UUID
impact_summary: "This change will affect the main database server"
risk_level: "MEDIUM"        # Choices: LOW, MEDIUM, HIGH
risk_description: "Potential for brief service interruption during implementation"
rollback_plan: "Restart database service if issues occur"
communication_plan: "Notify all users 24 hours before implementation"
```

#### Optional Fields
```
impacted_users_count: 150
downtime_required: true
downtime_duration: "30 minutes"
ai_summary: "ROOT_CAUSE_PATTERN"  # Choices: ROOT_CAUSE_PATTERN, ANOMALY_TREND, DRIFT_ALERT, POLICY_MATCH
ai_risk_score: 75.50
slo_impact: "May affect response time SLO by 5%"
observability_links: "https://dashboard.example.com/monitoring"
kpi_baseline: {"response_time": 200, "cpu_usage": 65}
```

#### File Upload Fields
```
uploaded_files: [risk_analysis.pdf, impact_matrix.xlsx]
remove_files: ["attachment-id-to-remove", "old_file.pdf"]  # Files to remove
```

#### Example cURL
```bash
curl -X POST "http://localhost:8000/api/change-impact-assessments/" \
  -H "Authorization: Bearer your-token" \
  -F "change=12345678-1234-1234-1234-123456789abc" \
  -F "impact_summary=This change will affect the main database server" \
  -F "risk_level=MEDIUM" \
  -F "risk_description=Potential for brief service interruption" \
  -F "rollback_plan=Restart database service if issues occur" \
  -F "communication_plan=Notify all users 24 hours before" \
  -F "impacted_users_count=150" \
  -F "downtime_required=true" \
  -F "uploaded_files=@/path/to/risk_analysis.pdf"
```

---

## 3. ChangeApproval API

### Endpoints
- `GET /api/change-approvals/` - List all approvals
- `POST /api/change-approvals/` - Create approval
- `GET /api/change-approvals/{id}/` - Get approval
- `PUT /api/change-approvals/{id}/` - Update approval (full)
- `PATCH /api/change-approvals/{id}/` - Update approval (partial)
- `GET /api/change-approvals/{id}/attachments/` - List attachments
- `POST /api/change-approvals/{id}/upload_attachment/` - Upload attachment

### POST /api/change-approvals/
**Content-Type:** `multipart/form-data`

#### Required Fields
```
change: "change-uuid-here"     # ChangeRequest UUID
approval_type: "TECHNICAL"     # Choices: TECHNICAL, BUSINESS, CAB, EMERGENCY, MANAGER
approver: 3                    # User ID of approver
```

#### Optional Fields
```
status: "PENDING"              # Choices: PENDING, APPROVED, REJECTED, CANCELLED
comments: "Approved with minor concerns about timing"
sequence: 1                    # Order of approval
approved_date: "2025-08-28T10:30:00Z"  # ISO datetime
```

#### File Upload Fields
```
uploaded_files: [approval_document.pdf, signed_form.pdf]
remove_files: ["old-approval-id", "outdated_form.pdf"]  # Files to remove
```

#### Example cURL
```bash
curl -X POST "http://localhost:8000/api/change-approvals/" \
  -H "Authorization: Bearer your-token" \
  -F "change=12345678-1234-1234-1234-123456789abc" \
  -F "approval_type=TECHNICAL" \
  -F "approver=3" \
  -F "status=APPROVED" \
  -F "comments=Approved with minor concerns" \
  -F "uploaded_files=@/path/to/approval_doc.pdf"
```

---

## 4. ChangeImplementationMonitoring API

### Endpoints
- `GET /api/change-implementation-monitoring/` - List all implementations
- `POST /api/change-implementation-monitoring/` - Create implementation record
- `GET /api/change-implementation-monitoring/{change_id}/` - Get implementation
- `PUT /api/change-implementation-monitoring/{change_id}/` - Update implementation (full)
- `PATCH /api/change-implementation-monitoring/{change_id}/` - Update implementation (partial)
- `GET /api/change-implementation-monitoring/{change_id}/attachments/` - List attachments
- `POST /api/change-implementation-monitoring/{change_id}/upload_attachment/` - Upload attachment

### POST /api/change-implementation-monitoring/
**Content-Type:** `multipart/form-data`

#### Required Fields
```
change: "change-uuid-here"  # ChangeRequest UUID
implementation_plan: "Step-by-step plan for database optimization"
implementation_steps: "1. Backup database\n2. Apply indexes\n3. Test performance"
```

#### Optional Fields
```
runbook: "runbook-uuid-here"           # Runbook UUID
pipeline_execution_id: "PIPE-12345"   # CI/CD pipeline ID
runbook_execution_id: "RUN-67890"     # Automation job ID
assigned_team: "SAP_BASIS"             # Choices: SAP_BASIS, ABAP, INFRA, NETWORK, SECURITY, APPS
actual_start: "2025-08-28T09:00:00Z"   # ISO datetime
actual_end: "2025-08-28T11:00:00Z"     # ISO datetime
runtime_status: "COMPLETED"           # Choices: IN_PROGRESS, ON_HOLD, COMPLETED, FAILED
issue_found: false
issue_details: "No issues encountered"
backout_executed: false
backout_details: ""
ai_execution_monitor: "All systems normal during execution"
```

#### File Upload Fields
```
uploaded_files: [execution_log.txt, screenshots.zip, monitoring_data.csv]
remove_files: ["temp-log-id", "old_screenshots.zip"]  # Files to remove
```

#### Example cURL
```bash
curl -X POST "http://localhost:8000/api/change-implementation-monitoring/" \
  -H "Authorization: Bearer your-token" \
  -F "change=12345678-1234-1234-1234-123456789abc" \
  -F "implementation_plan=Step-by-step database optimization plan" \
  -F "implementation_steps=1. Backup\n2. Apply indexes\n3. Test" \
  -F "assigned_team=SAP_BASIS" \
  -F "runtime_status=COMPLETED" \
  -F "issue_found=false" \
  -F "uploaded_files=@/path/to/execution_log.txt"
```

---

## 5. ChangeClosureReview API

### Endpoints
- `GET /api/change-closure-reviews/` - List all closure reviews
- `POST /api/change-closure-reviews/` - Create closure review
- `GET /api/change-closure-reviews/{change_id}/` - Get closure review
- `PUT /api/change-closure-reviews/{change_id}/` - Update closure review (full)
- `PATCH /api/change-closure-reviews/{change_id}/` - Update closure review (partial)
- `GET /api/change-closure-reviews/{change_id}/attachments/` - List attachments
- `POST /api/change-closure-reviews/{change_id}/upload_attachment/` - Upload attachment

### POST /api/change-closure-reviews/
**Content-Type:** `multipart/form-data`

#### Required Fields
```
change: "change-uuid-here"         # ChangeRequest UUID
success_status: "SUCCESSFUL"       # Choices: SUCCESSFUL, SUCCESSFUL_WITH_ISSUES, FAILED, ROLLED_BACK
```

#### Optional Fields
```
validation_result: "PASS"                    # Choices: PASS, PARTIAL, FAIL
service_health_ok: true
stakeholder_acceptance: "ACCEPTED"           # Choices: ACCEPTED, PENDING, REJECTED
metric_delta: {"response_time": -50, "cpu_usage": -10}  # JSON object
anomaly_detected_post_change: false
post_change_review: "Change implemented successfully with improved performance"
lessons_learned: "Database indexing should be standard practice"
ai_performance_review: "AI analysis shows 25% performance improvement"
linked_incidents: "INC-12345, INC-67890"
linked_service_requests: "SR-11111, SR-22222"
closure_approved_by: 4                       # User ID
closure_date: "2025-08-28T15:00:00Z"        # ISO datetime
kb_link: "https://kb.example.com/article/db-optimization"
```

#### File Upload Fields
```
uploaded_files: [closure_report.pdf, performance_metrics.xlsx, final_validation.doc]
remove_files: ["draft-report-id", "temp_metrics.xlsx"]  # Files to remove
```

#### Example cURL
```bash
curl -X POST "http://localhost:8000/api/change-closure-reviews/" \
  -H "Authorization: Bearer your-token" \
  -F "change=12345678-1234-1234-1234-123456789abc" \
  -F "success_status=SUCCESSFUL" \
  -F "validation_result=PASS" \
  -F "service_health_ok=true" \
  -F "stakeholder_acceptance=ACCEPTED" \
  -F "post_change_review=Change implemented successfully" \
  -F "lessons_learned=Database indexing best practices" \
  -F "uploaded_files=@/path/to/closure_report.pdf"
```

---

## 6. File Upload Endpoints

### Upload Attachment to Specific Model
Each model has dedicated upload endpoints:

#### POST /api/changerequest/{id}/upload_attachment/
#### POST /api/change-impact-assessments/{id}/upload_attachment/
#### POST /api/change-approvals/{id}/upload_attachment/
#### POST /api/change-implementation-monitoring/{id}/upload_attachment/
#### POST /api/change-closure-reviews/{id}/upload_attachment/

**Content-Type:** `multipart/form-data`

#### Required Fields
```
file: @/path/to/file.pdf
```

#### Optional Fields
```
description: "Risk analysis document"
```

#### Example cURL
```bash
curl -X POST "http://localhost:8000/api/changerequest/12345/upload_attachment/" \
  -H "Authorization: Bearer your-token" \
  -F "file=@/path/to/document.pdf" \
  -F "description=Supporting documentation"
```

### Remove Attachment
#### POST /api/changerequest/{id}/remove_attachment/
**Content-Type:** `application/json` or `multipart/form-data`

#### Required Fields
```
attachment_id: "attachment-uuid-here"
```

#### Example cURL
```bash
curl -X POST "http://localhost:8000/api/changerequest/12345/remove_attachment/" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{"attachment_id": "attachment-uuid-here"}'
```

---

## 7. Supporting Models APIs

### CI Types API
- `GET /api/ci-types/` - List CI types
- `POST /api/ci-types/` - Create CI type

#### POST /api/ci-types/
```
name: "Database Server"
description: "Database server configuration items"
is_active: true
```

### Configuration Items API
- `GET /api/configuration-items/` - List CIs
- `POST /api/configuration-items/` - Create CI

#### POST /api/configuration-items/
```
ci_name: "PROD-DB-01"
ci_type: 1  # CI Type ID
status: "ACTIVE"  # Choices: ACTIVE, INACTIVE, RETIRED, UNDER_MAINTENANCE
description: "Production database server"
owner: 2  # User ID
business_service: "Customer Management"
environment: "PROD"  # Choices: PROD, UAT, DEV, TEST
```

### Change Categories API
- `GET /api/change-categories/` - List categories
- `POST /api/change-categories/` - Create category

#### POST /api/change-categories/
```
name: "Database Changes"
description: "Changes related to database systems"
is_active: true
```

### Change Subcategories API
- `GET /api/change-subcategories/` - List subcategories
- `POST /api/change-subcategories/` - Create subcategory

#### POST /api/change-subcategories/
```
category: 1  # Category ID
name: "Performance Optimization"
description: "Database performance improvements"
is_active: true
```

---

## 8. Response Formats

### Success Response (201 Created)
```json
{
  "change_id": "12345678-1234-1234-1234-123456789abc",
  "change_number": "CHG-12345678",
  "change_title": "Database Performance Optimization",
  "status": "DRAFT",
  "created_at": "2025-08-28T08:00:00Z",
  "attachments": [
    {
      "attachment_id": "attach-uuid-here",
      "file_name": "risk_analysis.pdf",
      "file_url": "/media/change_attachments/2025/08/risk_analysis.pdf",
      "uploaded_at": "2025-08-28T08:00:00Z",
      "uploaded_by_details": {
        "id": 1,
        "username": "admin",
        "first_name": "Admin",
        "last_name": "User"
      }
    }
  ]
}
```

### Error Response (400 Bad Request)
```json
{
  "change_title": ["This field is required."],
  "priority": ["Invalid choice: 'INVALID'. Valid choices are: LOW, MEDIUM, HIGH, CRITICAL."]
}
```

---

## 9. Query Parameters

### Filtering
```
GET /api/changerequest/?status=APPROVED&priority=HIGH&change_type=NORMAL
GET /api/changerequest/?date_from=2025-08-01&date_to=2025-08-31
GET /api/changerequest/?requester=admin
```

### Searching
```
GET /api/changerequest/?search=database
```

### Ordering
```
GET /api/changerequest/?ordering=-request_date
GET /api/changerequest/?ordering=priority,status
```

---

## 10. File Management During Updates

### Overview
All changerequest models support advanced file management during update operations. You can:
1. **Add new files** using `uploaded_files`
2. **Remove existing files** using `remove_files`
3. **Keep existing files** by not including them in `remove_files`

### File Removal Methods
The `remove_files` field accepts two types of identifiers:

#### 1. Attachment ID (Recommended)
```
remove_files: ["12345678-1234-1234-1234-123456789abc"]
```

#### 2. File URL or Filename
```
remove_files: ["/media/change_attachments/2025/08/document.pdf", "old_file.pdf"]
```

### Complete Update Example
```bash
# Update with file management
curl -X PATCH "http://localhost:8000/api/changerequest/12345/" \
  -H "Authorization: Bearer your-token" \
  -F "status=IN_PROGRESS" \
  -F "priority=CRITICAL" \
  -F "remove_files=old-attachment-uuid" \
  -F "remove_files=outdated_document.pdf" \
  -F "uploaded_files=@/path/to/updated_plan.pdf" \
  -F "uploaded_files=@/path/to/new_requirements.doc"
```

### File Management Workflow

#### Step 1: Get Current Attachments
```bash
curl -X GET "http://localhost:8000/api/changerequest/12345/" \
  -H "Authorization: Bearer your-token"
```

Response includes current attachments:
```json
{
  "attachments": [
    {
      "attachment_id": "12345678-1234-1234-1234-123456789abc",
      "file_name": "old_document.pdf",
      "file_url": "/media/change_attachments/2025/08/old_document.pdf"
    },
    {
      "attachment_id": "87654321-4321-4321-4321-cba987654321",
      "file_name": "keep_this.xlsx",
      "file_url": "/media/change_attachments/2025/08/keep_this.xlsx"
    }
  ]
}
```

#### Step 2: Update with Selective File Management
```bash
# Remove old_document.pdf but keep keep_this.xlsx, add new files
curl -X PATCH "http://localhost:8000/api/changerequest/12345/" \
  -H "Authorization: Bearer your-token" \
  -F "remove_files=12345678-1234-1234-1234-123456789abc" \
  -F "uploaded_files=@/path/to/new_document.pdf"
```

#### Step 3: Verify Results
The response will show:
- `keep_this.xlsx` still attached
- `old_document.pdf` removed
- `new_document.pdf` added

### Error Handling
- **File Not Found**: If `remove_files` contains an invalid ID/URL, it's logged as a warning but doesn't fail the request
- **Permission Denied**: Users can only remove files from models they have update permissions for
- **Storage Cleanup**: Physical files are automatically deleted from storage when attachments are removed

### Best Practices

#### 1. Use Attachment IDs
```bash
# Preferred - use attachment ID
-F "remove_files=12345678-1234-1234-1234-123456789abc"

# Avoid - using filename (less reliable)
-F "remove_files=document.pdf"
```

#### 2. Atomic Updates
File operations are atomic - if the update fails, no files are added or removed.

#### 3. Batch Operations
```bash
# Remove multiple files and add multiple files in one request
curl -X PATCH "http://localhost:8000/api/changerequest/12345/" \
  -H "Authorization: Bearer your-token" \
  -F "remove_files=old-id-1" \
  -F "remove_files=old-id-2" \
  -F "remove_files=old-id-3" \
  -F "uploaded_files=@/path/to/new1.pdf" \
  -F "uploaded_files=@/path/to/new2.doc" \
  -F "status=IN_PROGRESS"
```

#### 4. JavaScript Example
```javascript
const formData = new FormData();

// Update model data
formData.append('status', 'IN_PROGRESS');
formData.append('priority', 'HIGH');

// Remove existing files
const filesToRemove = ['12345678-1234-1234-1234-123456789abc', 'old_doc.pdf'];
filesToRemove.forEach(fileId => {
    formData.append('remove_files', fileId);
});

// Add new files
const newFiles = document.querySelector('#file-input').files;
for (let i = 0; i < newFiles.length; i++) {
    formData.append('uploaded_files', newFiles[i]);
}

fetch('/api/changerequest/12345/', {
    method: 'PATCH',
    headers: {
        'Authorization': 'Bearer your-token'
    },
    body: formData
})
.then(response => response.json())
.then(data => {
    console.log('Updated with file management:', data);
});
```

---

## 11. File Upload Best Practices

1. **File Size Limits**: Typically 10MB per file
2. **Supported Formats**: PDF, DOC, DOCX, XLS, XLSX, TXT, PNG, JPG, ZIP
3. **Multiple Files**: Use array notation for multiple files: `uploaded_files[]`
4. **File Naming**: Files are automatically renamed to avoid conflicts
5. **Storage**: Files are organized by year/month in `change_attachments/YYYY/MM/`
6. **File Removal**: Use attachment IDs for reliable file removal during updates
7. **Atomic Operations**: File uploads and removals are part of model transactions

### Complete JavaScript Example (Frontend)
```javascript
const formData = new FormData();

// Model data
formData.append('change_title', 'Database Optimization');
formData.append('change_type', 'NORMAL');
formData.append('category', 'SOFTWARE');
formData.append('priority', 'HIGH');
formData.append('impact', 'DEPARTMENT');
formData.append('urgency', 'HIGH');
formData.append('business_justification', 'Performance improvement needed');
formData.append('initiated_by', 'USER');

// File management for updates
const filesToRemove = ['old-attachment-id-1', 'old-attachment-id-2'];
filesToRemove.forEach(id => formData.append('remove_files', id));

// Add multiple new files
const files = document.querySelector('#file-input').files;
for (let i = 0; i < files.length; i++) {
    formData.append('uploaded_files', files[i]);
}

// Create or Update
const url = isUpdate ? `/api/changerequest/${id}/` : '/api/changerequest/';
const method = isUpdate ? 'PATCH' : 'POST';

fetch(url, {
    method: method,
    headers: {
        'Authorization': 'Bearer your-token'
    },
    body: formData
})
.then(response => response.json())
.then(data => console.log(data));
```

This documentation covers all the changerequest models with complete form-data payloads for POST, PUT, and PATCH operations, including advanced file upload and removal support.
