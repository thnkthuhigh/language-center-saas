# API Reference & Examples

> Real request/response examples from the production REST API.  
> The system exposes **200+ endpoints** across **44 route modules** with Swagger/OpenAPI documentation.

---

## Table of Contents

- [Authentication](#authentication)
- [Standard Response Format](#standard-response-format)
- [Students](#students)
- [Classes](#classes)
- [Attendance](#attendance)
- [Payments](#payments)
- [Enrollments](#enrollments)
- [Teacher Salary](#teacher-salary)
- [Analytics](#analytics)
- [Receipts & Invoices](#receipts--invoices)
- [Endpoint Summary](#endpoint-summary)
- [Rate Limiting](#rate-limiting)

---

## Authentication

All protected endpoints require a Bearer token:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

| Token Type | Expiry | Purpose |
|-----------|--------|---------|
| Access Token | 15 min | API requests |
| Refresh Token | 7 days | Obtain new access token |

### `POST /api/auth/login`

Authenticate user and receive JWT token. Supports 2FA via Google Authenticator.

**Request:**
```json
{
  "username": "admin@example.com",
  "password": "secure_password",
  "twoFactorToken": "123456"       // optional, required if 2FA enabled
}
```

**Response `200 OK`:**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1Ni...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "username": "admin",
    "email": "admin@example.com",
    "fullName": "Nguyễn Văn A",
    "role": "owner",
    "branch": "507f1f77bcf86cd799439012",
    "branches": ["507f1f77bcf86cd799439012"],
    "primaryBranch": "507f1f77bcf86cd799439012",
    "phone": "0912345678",
    "avatar": "https://s3.amazonaws.com/...",
    "emailVerified": true,
    "assignedClasses": ["L1-A", "L2-B"]
  }
}
```

**Response `200` — 2FA Required:**
```json
{
  "success": true,
  "requires2FA": true,
  "message": "Please enter 2FA code from Google Authenticator"
}
```

**Response `423 Locked`:**
```json
{
  "success": false,
  "message": "Account locked after 5 failed attempts",
  "isLocked": true,
  "loginAttempts": 5,
  "lockedAt": "2026-01-08T10:30:00Z"
}
```

### Other Auth Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/auth/register` | Create initial owner account |
| `POST` | `/api/auth/google-login` | OAuth 2.0 Google login |
| `GET` | `/api/auth/profile` | Get current user profile |
| `PUT` | `/api/auth/profile` | Update profile |
| `POST` | `/api/auth/change-password` | Change password |
| `POST` | `/api/auth/2fa/setup` | Enable 2FA |
| `POST` | `/api/auth/2fa/verify` | Verify 2FA token |
| `POST` | `/api/auth/2fa/disable` | Disable 2FA |
| `POST` | `/api/auth/refresh-token` | Refresh access token |
| `POST` | `/api/auth/logout` | Logout current session |
| `POST` | `/api/auth/logout-all-devices` | Logout all sessions |
| `GET` | `/api/auth/active-sessions` | List active sessions |
| `GET` | `/api/auth/login-history` | View login history |
| `POST` | `/api/auth/request-password-reset` | Request password reset |
| `POST` | `/api/auth/reset-password` | Reset password with code |
| `POST` | `/api/auth/unlock-account` | Unlock locked account |
| `POST` | `/api/auth/force-logout/:userId` | Force logout user (owner/admin) |

---

## Standard Response Format

All endpoints follow a consistent response structure:

**Success:**
```json
{
  "success": true,
  "data": { },
  "message": "Operation successful"
}
```

**Error:**
```json
{
  "success": false,
  "message": "Error description",
  "error": "Detailed error info"
}
```

**Paginated:**
```json
{
  "success": true,
  "data": [ ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 145,
    "totalPages": 15,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "message": "Page 1/15 — 10 items"
}
```

---

## Students

### `GET /api/students`

Retrieve paginated student list with search, filters, and enrollment info.

**Roles:** `owner` `admin` `accountant` `teacher`

**Query Parameters:**
| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `page` | integer | 1 | Page number |
| `limit` | integer | 10 | Items per page |
| `search` | string | — | Search by name, code, or phone |
| `branch` | ObjectId | — | Filter by branch |
| `level` | string | — | Filter by level (e.g., "Level 1") |
| `enrollmentStatus` | string | — | `active` \| `inactive` \| `enrolled` \| `not-enrolled` |
| `classId` | ObjectId | — | Filter by active class |

**Response `200 OK`:**
```json
{
  "success": true,
  "data": [
    {
      "_id": "507f1f77bcf86cd799439011",
      "fullName": "Nguyễn Thị B",
      "studentCode": "HS001",
      "phone": "0987654321",
      "email": "student@example.com",
      "dateOfBirth": "2010-05-15",
      "currentLevel": "Level 1",
      "branch": "507f1f77bcf86cd799439012",
      "parentName": "Nguyễn Văn A",
      "parentPhone": "0912345678",
      "status": "active",
      "activeEnrollments": [
        {
          "_id": "507f1f77bcf86cd799439015",
          "classInfo": {
            "className": "Level 1 - Lớp A",
            "classCode": "L1-A"
          },
          "status": "ACTIVE"
        }
      ],
      "hasActiveEnrollment": true,
      "createdAt": "2025-12-10T08:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 145,
    "totalPages": 15,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "message": "Page 1/15 — 10 students"
}
```

### `POST /api/students`

Create a new student record.

**Roles:** `owner` `admin`

**Request:**
```json
{
  "fullName": "Nguyễn Thị B",
  "studentCode": "HS001",
  "phone": "0987654321",
  "email": "student@example.com",
  "dateOfBirth": "2010-05-15",
  "parentName": "Nguyễn Văn A",
  "parentPhone": "0912345678",
  "parentEmail": "parent@example.com",
  "address": "123 Nguyễn Huệ, Q.1",
  "branch": "507f1f77bcf86cd799439012",
  "notes": "Optional notes"
}
```

**Response `201 Created`:**
```json
{
  "success": true,
  "message": "Student created successfully",
  "data": {
    "_id": "507f1f77bcf86cd799439011",
    "fullName": "Nguyễn Thị B",
    "studentCode": "HS001",
    "phone": "0987654321",
    "status": "active",
    "isActive": true,
    "createdAt": "2026-01-08T10:30:00Z"
  }
}
```

### Other Student Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/students/:id/with-enrollments` | Student detail with all enrollments |
| `PUT` | `/api/students/:id` | Update student info |
| `DELETE` | `/api/students/:id` | Soft delete student |
| `GET` | `/api/students/code/:studentCode` | Find by student code |
| `GET` | `/api/students/deleted` | List deleted students |
| `POST` | `/api/students/:id/restore` | Restore deleted student |
| `POST` | `/api/students/:id/permanent-delete` | Hard delete |
| `POST` | `/api/students/import-excel` | Bulk import from Excel |
| `GET` | `/api/students/import-template` | Download Excel template |
| `GET` | `/api/students/duplicates` | Find duplicate records |
| `POST` | `/api/students/merge` | Merge duplicate students |

---

## Classes

### `GET /api/classes`

Retrieve classes with schedule, teacher, enrollment stats, and salary config.

**Query Parameters:**
| Param | Type | Description |
|-------|------|-------------|
| `page` | integer | Page number |
| `limit` | integer | Items per page (0 = no limit) |
| `status` | string | `ACTIVE,PLANNED` (comma-separated) |
| `search` | string | Search by class code or name |
| `branchId` | ObjectId | Filter by branch |

**Response `200 OK`:**
```json
{
  "success": true,
  "data": [
    {
      "_id": "507f1f77bcf86cd799439020",
      "classCode": "L1-A",
      "className": "Level 1 - Lớp A",
      "classStatus": "ACTIVE",
      "branch": {
        "name": "Branch 1",
        "branchCode": "CN1",
        "address": "123 Nguyễn Huệ"
      },
      "assignedTeacher": {
        "fullName": "Mr. Minh",
        "email": "teacher@example.com",
        "teacherCode": "GV001"
      },
      "tuitionFee1Week": 500000,
      "tuitionFee4Weeks": 1800000,
      "tuitionFee8Weeks": 3200000,
      "tuitionFee12Weeks": 4500000,
      "teacherSalaryPerSession": 100000,
      "salaryCode": {
        "code": "CODE1",
        "subCodes": [
          { "subCode": "K1", "amount": 100000, "isActive": true },
          { "subCode": "K2", "amount": 120000, "isActive": true },
          { "subCode": "K3", "amount": 150000, "isActive": true }
        ]
      },
      "totalEnrollments": 12,
      "activeEnrollments": 10,
      "completedEnrollments": 2,
      "detailedSchedule": [
        {
          "dayOfWeek": 1,
          "dayName": "Monday",
          "startTime": "19:00",
          "endTime": "20:30",
          "duration": 90
        },
        {
          "dayOfWeek": 3,
          "dayName": "Wednesday",
          "startTime": "19:00",
          "endTime": "20:30",
          "duration": 90
        }
      ],
      "levelCode": "L1",
      "totalStudyWeeks": 12,
      "startDate": "2025-12-01",
      "endDate": "2026-03-31"
    }
  ],
  "pagination": { "page": 1, "limit": 10, "total": 24, "totalPages": 3 }
}
```

### Other Class Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/classes` | Create new class |
| `PUT` | `/api/classes/:id` | Update class info |
| `DELETE` | `/api/classes/:id` | Delete class |
| `GET` | `/api/classes/teacher/:teacherId` | Classes by teacher |
| `GET` | `/api/classes/branch/:branch` | Classes by branch |
| `GET` | `/api/classes/code/:classCode` | Find by class code |
| `PUT` | `/api/classes/:classCode/salary-config` | Update salary config |
| `POST` | `/api/classes/check-schedule-conflict` | Check teacher schedule conflicts |
| `POST` | `/api/classes/import-excel` | Bulk import from Excel |
| `POST` | `/api/classes/recalculate-students` | Recalculate enrollment counts |

---

## Attendance

### `POST /api/attendance`

Record or update attendance for a class session. Automatically creates makeup sessions for excused students and calculates teacher salary.

**Roles:** `teacher` `admin` `owner`

**Request:**
```json
{
  "classCode": "L1-A",
  "sessionDate": "2026-01-08",
  "attendances": [
    {
      "student": "507f1f77bcf86cd799439011",
      "status": "present",
      "notes": ""
    },
    {
      "student": "507f1f77bcf86cd799439012",
      "status": "excused",
      "notes": "Sick"
    }
  ],
  "sessionType": "regular",
  "notes": "Chapter 5 completed"
}
```

| Field | Type | Required | Values |
|-------|------|----------|--------|
| `classCode` | string | ✅ | Class code (e.g., "L1-A") |
| `sessionDate` | string | ✅ | ISO date |
| `attendances` | array | ✅ | Student attendance records |
| `attendances[].status` | string | ✅ | `present` \| `absent` \| `excused` \| `late` \| `makeup` |
| `sessionType` | string | — | `regular` \| `makeup` \| `extra` |
| `teacher` | ObjectId | — | Override default teacher |
| `makeupReason` | string | — | Required for makeup/extra sessions |

**Response `201 Created`:**
```json
{
  "success": true,
  "data": {
    "_id": "507f1f77bcf86cd799439100",
    "classCode": "L1-A",
    "className": "Level 1 - Lớp A",
    "sessionDate": "2026-01-08T00:00:00Z",
    "sessionType": "regular",
    "attendances": [
      {
        "student": "507f1f77bcf86cd799439011",
        "studentCode": "HS001",
        "studentName": "Nguyễn Thị B",
        "status": "present",
        "checkedAt": "2026-01-08T19:00:00Z"
      }
    ],
    "stats": {
      "totalStudents": 12,
      "presentCount": 10,
      "absentCount": 1,
      "excusedCount": 1,
      "lateCount": 0
    },
    "teacher": "507f1f77bcf86cd799439050",
    "teacherSalaryAmount": 100000,
    "isCompleted": true,
    "notes": "Chapter 5 completed"
  },
  "makeupCreated": [
    {
      "_id": "507f1f77bcf86cd799439110",
      "reason": "Sick",
      "createdFor": "507f1f77bcf86cd799439012"
    }
  ],
  "message": "Attendance recorded successfully"
}
```

**Error `400` — Holiday Conflict:**
```json
{
  "success": false,
  "message": "Cannot create session on holiday: Lunar New Year",
  "holidayInfo": {
    "name": "Lunar New Year",
    "date": "2026-02-01",
    "type": "lunar"
  }
}
```

### Other Attendance Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/attendance/class/:classCode` | Attendance by class |
| `GET` | `/api/attendance/stats/:classCode` | Attendance statistics |
| `GET` | `/api/attendance/student/:studentId` | Student's attendance |
| `GET` | `/api/attendance/class/:classCode/sessions` | All sessions for a class |
| `POST` | `/api/attendance/:id/edit` | Edit attendance record |
| `POST` | `/api/attendance/:id/change-teacher` | Change session teacher |
| `PUT` | `/api/attendance/:id/lesson-content` | Update lesson content |
| `PUT` | `/api/attendance/:id/evaluations` | Update student evaluations |
| `PUT` | `/api/attendance/:id/notes` | Update teacher notes |
| `POST` | `/api/attendance/:id/update-salary` | Recalculate session salary |
| `DELETE` | `/api/attendance/:id` | Delete attendance record |
| `GET` | `/api/attendance/:studentId/learning-history` | Full learning history |

---

## Payments

### `POST /api/payments/:paymentId/record`

Record a payment for a student's enrollment. Supports partial payments, multiple payment methods, and multi-cycle billing.

**Roles:** `owner` `admin` `accountant` `teacher`

**Request:**
```json
{
  "amount": 900000,
  "paymentMethod": "transfer",
  "notes": "Bank transfer received",
  "receiptNumber": "INV-HS001-202601-001",
  "sessionsToAdd": 8,
  "companyName": "English Center ABC",
  "companyAddress": "123 Nguyễn Huệ, HCM",
  "bookNumber": "01",
  "periodStartDate": "2026-01-06",
  "periodEndDate": "2026-02-02"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `amount` | number | Payment amount (VND) |
| `paymentMethod` | string | `cash` \| `transfer` \| `card` \| `momo` |
| `sessionsToAdd` | integer | Sessions granted for this payment |
| `receiptNumber` | string | Auto-generated invoice number |

**Response `200 OK`:**
```json
{
  "success": true,
  "data": {
    "_id": "507f1f77bcf86cd799439200",
    "student": {
      "fullName": "Nguyễn Thị B",
      "studentCode": "HS001",
      "phone": "0987654321"
    },
    "enrollment": {
      "classInfo": {
        "className": "Level 1 - Lớp A",
        "classCode": "L1-A",
        "branch": { "name": "Branch 1", "branchCode": "01" }
      },
      "status": "ACTIVE"
    },
    "amountDue": 1800000,
    "amountPaid": 900000,
    "remainingAmount": 900000,
    "paymentPeriod": 1,
    "status": "partial",
    "paymentMethod": "transfer",
    "paymentHistory": [
      {
        "date": "2026-01-08T10:30:00Z",
        "amountPaid": 900000,
        "sessionsGranted": 8,
        "notes": "Bank transfer received",
        "recordedBy": "507f1f77bcf86cd799439070"
      }
    ],
    "invoiceNumber": "INV-HS001-202601-001",
    "sessionsForPeriod": 16,
    "isActive": true
  },
  "message": "Payment recorded successfully"
}
```

### Other Payment Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/payments` | List payments (filterable, paginated) |
| `GET` | `/api/payments/stats` | Payment statistics |
| `GET` | `/api/payments/student/:studentId` | Student payment details |
| `GET` | `/api/payments/student/:studentId/history` | Full payment history |
| `POST` | `/api/payments/per-session` | Per-session payment |
| `POST` | `/api/payments/:paymentId/approve` | Approve pending payment |
| `POST` | `/api/payments/:paymentId/void` | Void a payment |
| `POST` | `/api/payments/cross-payment` | Cross-class transfer payment |
| `GET` | `/api/payments/:paymentId/audit-log` | Payment audit trail |
| `GET` | `/api/payments/voided/list` | List voided payments |
| `GET` | `/api/payments/invoice/next` | Next invoice number |

---

## Enrollments

### `POST /api/enrollments`

Enroll a student in a class with payment cycle configuration.

**Roles:** `admin` `owner`

**Key Features:**
- CEFR-based auto-level detection (A1 → C2)
- Holiday-aware session calculation
- Multi-cycle payment scheduling
- Transfer between classes with prorated refunds

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/enrollments` | List all enrollments |
| `POST` | `/api/enrollments` | Create enrollment |
| `GET` | `/api/enrollments/student/:studentId` | Enrollments by student |
| `GET` | `/api/enrollments/class/:classCode` | Enrollments by class |
| `POST` | `/api/enrollments/:id/complete` | Mark as completed |
| `POST` | `/api/enrollments/:id/extend` | Extend enrollment |
| `POST` | `/api/enrollments/:id/pause` | Pause enrollment |
| `POST` | `/api/enrollments/:id/resume` | Resume enrollment |
| `POST` | `/api/enrollments/:id/transfer` | Transfer to another class |
| `POST` | `/api/enrollments/:id/execute-transfer` | Execute transfer with cross-payment |
| `GET` | `/api/enrollments/session-balance/:studentId` | Remaining sessions |
| `GET` | `/api/enrollments/low-balance` | Students with low session balance |
| `GET` | `/api/enrollments/auto/suggest-classes/:studentId` | AI-suggested next classes |
| `GET` | `/api/enrollments/auto/ready-for-upgrade` | Students ready for level upgrade |
| `POST` | `/api/enrollments/auto/bulk-upgrade` | Bulk level upgrade |

---

## Teacher Salary

### `POST /api/teacher-salary/calculate`

Calculate salary for a teacher based on attendance records and salary codes (K1/K2/K3 tiers).

**Roles:** `owner` `admin` `accountant`

**Salary Flow:** `Attendance → Salary Code → K1/K2/K3 tier → Amount`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/teacher-salary/calculate` | Calculate teacher salary |
| `POST` | `/api/teacher-salary/preview` | Preview salary before saving |
| `POST` | `/api/teacher-salary/period` | Create salary period (month/year) |
| `GET` | `/api/teacher-salary/periods` | List salary periods |
| `GET` | `/api/teacher-salary/periods/:periodId` | Period detail with all salaries |
| `POST` | `/api/teacher-salary/periods/:periodId/finalize` | Finalize period |
| `POST` | `/api/teacher-salary/periods/:periodId/approve` | Approve for disbursement |
| `POST` | `/api/teacher-salary/periods/:periodId/disburse` | Mark as disbursed |
| `GET` | `/api/teacher-salary/history` | Salary history |
| `GET` | `/api/teacher-salary/stats` | Salary statistics |
| `GET` | `/api/teacher-salary/report` | Exportable salary report |
| `GET` | `/api/teacher-salary/my-latest` | Teacher views own salary |
| `POST` | `/api/teacher-salary/employee/confirm` | Teacher confirms salary |
| `GET` | `/api/teacher-salary/available-attendances` | Unprocessed attendances |

---

## Analytics

### `GET /api/analytics/dashboard`

Comprehensive dashboard data for center owners. Cached with 15-minute TTL.

**Roles:** `owner`

**Response `200 OK`:**
```json
{
  "success": true,
  "data": {
    "totalRevenue": 45500000,
    "totalExpenses": 12000000,
    "netProfit": 33500000,
    "revenueByMonth": [
      { "month": "2025-11", "revenue": 4500000 },
      { "month": "2025-12", "revenue": 5000000 }
    ],
    "classesByStatus": {
      "ACTIVE": 12,
      "PLANNED": 3,
      "COMPLETED": 2,
      "CANCELLED": 1
    },
    "totalStudents": 145,
    "activeStudents": 98,
    "inactiveStudents": 47,
    "totalTeachers": 8,
    "activeTeachers": 7,
    "fillRate": 0.85,
    "pendingPayments": 7500000,
    "overduePayments": 2000000,
    "unpaidEnrollments": 23
  },
  "cached": true,
  "message": "Dashboard analytics loaded successfully"
}
```

### Other Analytics Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/analytics/revenue` | Revenue analysis |
| `GET` | `/api/analytics/students` | Student analytics |
| `GET` | `/api/analytics/occupancy` | Class occupancy rates |
| `GET` | `/api/analytics/payments` | Payment analytics |
| `GET` | `/api/analytics/teachers` | Teacher performance |
| `GET` | `/api/analytics/comparison` | Period comparison |
| `GET` | `/api/analytics/cashflow` | Cash flow analysis |
| `GET` | `/api/analytics/revenue/upcoming` | Upcoming revenue forecast |
| `GET` | `/api/analytics/fill-rate` | Class fill rates |
| `GET` | `/api/analytics/churn-rate` | Student churn rate |
| `GET` | `/api/analytics/teacher-performance` | Teacher performance metrics |
| `POST` | `/api/analytics/cache/clear` | Clear analytics cache |

---

## Receipts & Invoices

### Receipt Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/receipts` | List receipts |
| `POST` | `/api/receipts` | Create receipt |
| `GET` | `/api/receipts/:id` | Get receipt detail |
| `PUT` | `/api/receipts/:id` | Update receipt |
| `DELETE` | `/api/receipts/:id` | Delete receipt |
| `POST` | `/api/receipts/:id/print` | Mark as printed |
| `GET` | `/api/receipts/next-number` | Next receipt number |

### Invoice Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/invoices/:paymentId/preview` | Invoice preview |
| `GET` | `/api/invoices/:paymentId/pdf` | Generate PDF invoice |
| `GET` | `/api/invoices/:paymentId/receipt` | Generate PDF receipt |
| `POST` | `/api/invoices/archive` | Archive invoice |
| `GET` | `/api/invoices/settings` | Invoice settings |
| `PUT` | `/api/invoices/settings` | Update invoice settings |

---

## Endpoint Summary

| Module | Routes | Auth | Description |
|--------|--------|------|-------------|
| **Auth** | 28 | Partial | Login, 2FA, sessions, password reset |
| **Students** | 15 | ✅ | CRUD, import, merge, soft delete |
| **Classes** | 17 | ✅ | CRUD, scheduling, salary config |
| **Attendance** | 21 | ✅ | Record, edit, stats, salary calc |
| **Payments** | 22 | ✅ | Record, approve, void, cross-payment |
| **Enrollments** | 30 | ✅ | CRUD, transfer, pause, auto-upgrade |
| **Teacher Salary** | 28 | ✅ | Calculate, periods, approve, disburse |
| **Analytics** | 14 | ✅ | Dashboard, revenue, churn, forecast |
| **Receipts** | 7 | ✅ | CRUD, print, numbering |
| **Invoices** | 7 | ✅ | PDF generation, preview, settings |
| **+ 34 more modules** | ~30+ | ✅ | Users, branches, books, notifications, etc. |
| **Total** | **200+** | | |

---

## Rate Limiting

| Tier | Limit | Applied To |
|------|-------|-----------|
| Global | 100 req/15min | All endpoints |
| Auth | 5 req/15min | Login, password reset |
| API Read | 60 req/min | GET endpoints |
| API Write | 30 req/min | POST/PUT/DELETE |
| Upload | 10 req/hour | File uploads |
| Sensitive | 3 req/15min | Password change, 2FA |

---

## Middleware Pipeline

Every request passes through this middleware stack:

```
Request
  → Helmet (security headers)
  → CORS (origin validation)
  → Rate Limiter (tier-based)
  → Body Parser (JSON/URL-encoded)
  → Request Logger (Winston)
  → JWT Authentication
  → Role Authorization
  → Input Validation
  → Controller Logic
  → Activity Logger (write ops)
  → Response
```

---

## Caching Strategy

| Key Pattern | TTL | Description |
|-------------|-----|-------------|
| `STUDENTS:*` | 5 min | Student queries |
| `CLASSES:*` | 5 min | Class queries |
| `ENROLLMENTS:*` | 10 min | Enrollment data |
| `PAYMENTS:*` | 5 min | Payment stats |
| `DASHBOARD:*` | 15 min | Analytics dashboard |

Cache is automatically invalidated on write operations (POST/PUT/DELETE).

---

> **Note:** This is a showcase of the API design. The full source code is proprietary.  
> For access requests, see [Source Code](README.md#-source-code) in the main README.
