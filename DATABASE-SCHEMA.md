<div align="center">

# Database Schema Documentation

### Language Center SaaS — MongoDB Collections & Relationships

</div>

---

## Table of Contents

- [Overview](#overview)
- [Core Models](#core-models)
- [Academic Models](#academic-models)
- [Financial Models](#financial-models)
- [Salary Models](#salary-models)
- [Communication Models](#communication-models)
- [Security Models](#security-models)
- [System Models](#system-models)
- [Relationships Diagram](#relationships-diagram)
- [Indexing Strategy](#indexing-strategy)

---

## Overview

The database consists of **39 MongoDB collections** (MongoDB 7.0 with Mongoose 8.0 ODM) organized into 7 logical domains. All models use Mongoose ODM with:

- **Automatic timestamps** (`createdAt`, `updatedAt`)
- **Referential integrity** via ObjectId references
- **Virtual fields** for computed properties
- **Pre/post hooks** for cascading operations
- **Compound indexes** for query optimization

---

## Core Models

### User
The central identity model supporting 4 roles with comprehensive security features.

```
User
├── _id                    ObjectId
├── username               String (unique)
├── email                  String (unique)
├── password               String (bcrypt hashed)
├── role                   Enum: 'owner' | 'admin' | 'teacher' | 'accountant'
├── fullName               String
├── phone                  String
├── branch                 ObjectId → Branch
├── isActive               Boolean
├── isLocked               Boolean
├── lockReason             String
├── lockUntil              Date
├── googleId               String (OAuth)
├── twoFactorEnabled       Boolean
├── twoFactorSecret        String (TOTP)
├── backupCodes            [String] (hashed)
├── lastLogin              Date
├── createdAt              Date (auto)
└── updatedAt              Date (auto)
```

### Branch
Multi-location support with unique 2-letter branch codes.

```
Branch
├── _id                    ObjectId
├── name                   String
├── code                   String (2 chars, unique)
├── address                String
├── phone                  String
├── email                  String
├── isActive               Boolean
├── createdAt              Date
└── updatedAt              Date
```

---

## Academic Models

### Student
Student profiles with CEFR level tracking and parent/guardian contacts.

```
Student
├── _id                    ObjectId
├── studentCode            String (unique, auto-generated)
├── fullName               String
├── dateOfBirth            Date
├── gender                 Enum: 'male' | 'female' | 'other'
├── phone                  String
├── email                  String
├── address                String
├── level                  Enum: 'A1' | 'A2' | 'B1' | 'B2' | 'C1' | 'C2'
├── status                 Enum: 'active' | 'inactive' | 'graduated' | 'withdrawn'
├── branch                 ObjectId → Branch
├── parentName             String
├── parentPhone            String
├── parentEmail            String
├── notes                  String
├── enrollmentHistory      [ObjectId → Enrollment]
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date
```

### Class
Classes with detailed scheduling, tuition pricing, and teacher assignments.

```
Class
├── _id                    ObjectId
├── className              String
├── classCode              String (unique)
├── branch                 ObjectId → Branch
├── level                  Enum: 'A1' | 'A2' | 'B1' | 'B2' | 'C1' | 'C2'
├── teacher                ObjectId → User (role: teacher)
├── maxStudents            Number
├── currentStudents        Number (virtual)
├── status                 Enum: 'upcoming' | 'active' | 'completed' | 'cancelled'
├── schedule               [{
│   ├── dayOfWeek          Number (0-6)
│   ├── startTime          String (HH:mm)
│   ├── endTime            String (HH:mm)
│   └── room               String
│   }]
├── startDate              Date
├── endDate                Date
├── tuitionFee             [{
│   ├── weeks              Number (1 | 4 | 8 | 12)
│   └── amount             Number
│   }]
├── salaryCode             ObjectId → SalaryCode
├── description            String
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date
```

### Enrollment
The bridge between Students and Classes with payment and status tracking.

```
Enrollment
├── _id                    ObjectId
├── student                ObjectId → Student
├── class                  ObjectId → Class
├── enrollmentDate         Date
├── startDate              Date
├── endDate                Date
├── status                 Enum: 'pending' | 'active' | 'completed' | 'cancelled'
├── paymentCycle           Number (1 | 4 | 8 | 12 weeks)
├── tuitionAmount          Number
├── paymentStatus          Enum: 'pending' | 'partial' | 'paid' | 'overdue'
├── makeupCredits          Number
├── notes                  String
├── cancelReason           String
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date
```

### Attendance
Session-based attendance records linking students, classes, and teachers.

```
Attendance
├── _id                    ObjectId
├── class                  ObjectId → Class
├── date                   Date (UTC)
├── timeSlot               String
├── teacher                ObjectId → User
├── students               [{
│   ├── student            ObjectId → Student
│   ├── status             Enum: 'present' | 'absent' | 'excused' | 'late'
│   └── notes              String
│   }]
├── sessionSalary          {
│   ├── salaryCode         ObjectId → SalaryCode
│   ├── amount             Number
│   └── calculated         Boolean
│   }
├── isCancelled            Boolean
├── cancelReason           String
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date
```

### Book
Learning materials and textbook inventory management.

```
Book
├── _id                    ObjectId
├── title                  String
├── author                 String
├── level                  Enum: 'A1' - 'C2'
├── isbn                   String
├── publisher              String
├── price                  Number
├── quantity               Number
├── description            String
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

### Holiday
Holiday definitions for automatic schedule adjustments.

```
Holiday
├── _id                    ObjectId
├── name                   String
├── date                   Date
├── endDate                Date
├── isRecurring            Boolean
├── branch                 ObjectId → Branch
├── description            String
├── createdAt              Date
└── updatedAt              Date
```

### TimeSlot
Configurable teaching time slot definitions.

```
TimeSlot
├── _id                    ObjectId
├── name                   String (e.g., "Ca 1")
├── startTime              String (HH:mm)
├── endTime                String (HH:mm)
├── branch                 ObjectId → Branch
├── isActive               Boolean
├── createdAt              Date
└── updatedAt              Date
```

### MakeupSession & ClassCancellation

```
MakeupSession
├── _id                    ObjectId
├── student                ObjectId → Student
├── originalClass          ObjectId → Class
├── originalDate           Date
├── makeupClass            ObjectId → Class
├── makeupDate             Date
├── status                 Enum: 'pending' | 'completed' | 'cancelled'
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date

ClassCancellation
├── _id                    ObjectId
├── class                  ObjectId → Class
├── date                   Date
├── reason                 String
├── makeupDate             Date
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date
```

---

## Financial Models

### Payment
Payment transactions linked to enrollments and invoices.

```
Payment
├── _id                    ObjectId
├── student                ObjectId → Student
├── enrollment             ObjectId → Enrollment
├── invoice                ObjectId → Invoice
├── amount                 Number
├── method                 Enum: 'cash' | 'bank_transfer' | 'other'
├── status                 Enum: 'pending' | 'confirmed' | 'cancelled'
├── paymentDate            Date
├── notes                  String
├── processedBy            ObjectId → User
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

### Invoice
Auto-generated invoices with sequential numbering.

```
Invoice
├── _id                    ObjectId
├── invoiceCode            String (unique, e.g., "INV-2026-00001")
├── student                ObjectId → Student
├── enrollment             ObjectId → Enrollment
├── class                  ObjectId → Class
├── amount                 Number
├── dueDate                Date
├── status                 Enum: 'draft' | 'sent' | 'paid' | 'overdue' | 'cancelled'
├── items                  [{
│   ├── description        String
│   ├── quantity           Number
│   ├── unitPrice          Number
│   └── total              Number
│   }]
├── notes                  String
├── sentAt                 Date
├── paidAt                 Date
├── branch                 ObjectId → Branch
├── createdBy              ObjectId → User
├── createdAt              Date
└── updatedAt              Date
```

### Receipt
Payment receipts with QR code verification.

```
Receipt
├── _id                    ObjectId
├── receiptCode            String (unique, e.g., "RCP-2026-00001")
├── payment                ObjectId → Payment
├── student                ObjectId → Student
├── amount                 Number
├── paymentMethod          String
├── qrCode                 String (generated QR data)
├── notes                  String
├── issuedBy               ObjectId → User
├── branch                 ObjectId → Branch
├── emailSent              Boolean
├── emailSentAt            Date
├── createdAt              Date
└── updatedAt              Date
```

### TuitionPriceTable
Configurable tuition pricing with date-range validity.

```
TuitionPriceTable
├── _id                    ObjectId
├── name                   String
├── level                  String
├── hoursPerWeek           Number
├── prices                 [{
│   ├── weeks              Number (1 | 4 | 8 | 12)
│   └── amount             Number
│   }]
├── effectiveFrom          Date
├── effectiveTo            Date
├── isActive               Boolean
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

### OtherPayment & CrossPayment

```
OtherPayment                          CrossPayment
├── _id             ObjectId          ├── _id             ObjectId
├── student         → Student         ├── student         → Student
├── type            String            ├── fromClass       → Class
├── amount          Number            ├── toClass         → Class
├── description     String            ├── amount          Number
├── paymentDate     Date              ├── adjustmentType  String
├── branch          → Branch          ├── reason          String
├── createdBy       → User            ├── createdBy       → User
├── createdAt       Date              ├── createdAt       Date
└── updatedAt       Date              └── updatedAt       Date
```

---

## Salary Models

### TeacherSalary
Individual teacher salary records calculated from attendance.

```
TeacherSalary
├── _id                    ObjectId
├── teacher                ObjectId → User
├── period                 ObjectId → SalaryPeriod
├── month                  Number
├── year                   Number
├── baseSalary             Number
├── attendanceSalary       Number
├── totalSalary            Number
├── sessions               [{
│   ├── date               Date
│   ├── class              ObjectId → Class
│   ├── attendance         ObjectId → Attendance
│   ├── salaryCode         String
│   ├── amount             Number
│   └── notes              String
│   }]
├── adjustments            [{
│   ├── type               String
│   ├── amount             Number
│   └── reason             String
│   }]
├── status                 Enum: 'draft' | 'reviewed' | 'approved' | 'paid'
├── approvedBy             ObjectId → User
├── approvedAt             Date
├── notes                  String
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

### SalaryPeriod
Monthly salary period containers for batch processing.

```
SalaryPeriod
├── _id                    ObjectId
├── month                  Number (1-12)
├── year                   Number
├── status                 Enum: 'open' | 'closed' | 'paid'
├── salaries               [ObjectId → TeacherSalary]
├── totalAmount            Number (virtual)
├── branch                 ObjectId → Branch
├── closedBy               ObjectId → User
├── closedAt               Date
├── createdAt              Date
└── updatedAt              Date
```

### SalaryCode
Salary tier definitions with sub-codes for different compensation levels.

```
SalaryCode
├── _id                    ObjectId
├── code                   String (unique)
├── name                   String
├── description            String
├── subCodes               [{
│   ├── code               String (K1 | K2 | K3)
│   ├── name               String
│   ├── amount             Number (per session)
│   └── description        String
│   }]
├── isActive               Boolean
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

### AdminSalary & StaffAttendance

```
AdminSalary
├── _id                    ObjectId
├── user                   ObjectId → User
├── month                  Number
├── year                   Number
├── shifts                 [{
│   ├── date               Date
│   ├── type               String (office | assistant)
│   ├── hours              Number
│   └── amount             Number
│   }]
├── totalAmount            Number
├── status                 Enum: 'draft' | 'approved' | 'paid'
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

---

## Communication Models

### Notification

```
Notification
├── _id                    ObjectId
├── recipient              ObjectId → User
├── title                  String
├── message                String
├── type                   String (payment | attendance | salary | system)
├── isRead                 Boolean (default: false)
├── metadata               Mixed (contextual data)
├── createdAt              Date
└── updatedAt              Date
```

### EmailLog

```
EmailLog
├── _id                    ObjectId
├── to                     String (email address)
├── subject                String
├── template               String
├── status                 Enum: 'sent' | 'failed' | 'queued'
├── provider               String (sendgrid | azure | nodemailer)
├── error                  String
├── sentAt                 Date
├── createdAt              Date
└── updatedAt              Date
```

---

## Security Models

### LoginHistory

```
LoginHistory
├── _id                    ObjectId
├── user                   ObjectId → User
├── ip                     String
├── userAgent              String (parsed via ua-parser-js)
├── browser                String
├── os                     String
├── device                 String
├── success                Boolean
├── failReason             String
├── location               String
├── createdAt              Date
└── updatedAt              Date
```

### TrustedDevice

```
TrustedDevice
├── _id                    ObjectId
├── user                   ObjectId → User
├── deviceFingerprint      String
├── deviceName             String
├── browser                String
├── os                     String
├── lastUsed               Date
├── isTrusted              Boolean
├── createdAt              Date
└── updatedAt              Date
```

### RefreshToken & PasswordReset

```
RefreshToken                          PasswordReset
├── _id             ObjectId          ├── _id             ObjectId
├── user            → User            ├── user            → User
├── token           String (hashed)   ├── token           String (hashed)
├── expiresAt       Date              ├── expiresAt       Date
├── isRevoked       Boolean           ├── used            Boolean
├── createdAt       Date              ├── createdAt       Date
└── updatedAt       Date              └── updatedAt       Date
```

---

## System Models

### ActivityLog
Comprehensive audit trail with before/after state diffs.

```
ActivityLog
├── _id                    ObjectId
├── user                   ObjectId → User
├── module                 String (student | class | payment | salary | ...)
├── action                 String (create | update | delete | ...)
├── description            String
├── targetId               ObjectId
├── targetModel            String
├── before                 Mixed (previous state snapshot)
├── after                  Mixed (new state snapshot)
├── diff                   Mixed (computed diff)
├── ip                     String
├── userAgent              String
├── branch                 ObjectId → Branch
├── createdAt              Date
└── updatedAt              Date
```

### SystemConfig, StorageFolder, StoredFile, ScheduleAdjustment, ExtensionProposal, Description

```
SystemConfig                    StorageFolder / StoredFile
├── key          String         ├── name          String
├── value        Mixed          ├── path          String
├── description  String         ├── parent        → StorageFolder
├── category     String         ├── size          Number
└── updatedAt    Date           ├── mimeType      String
                                ├── s3Key         String
                                ├── uploadedBy    → User
                                └── createdAt     Date
```

---

## Relationships Diagram

```
                         ┌──────────┐
                         │   User   │
                         │ (4 roles)│
                         └─┬──┬──┬──┘
                           │  │  │
          ┌────────────────┘  │  └────────────────┐
          │                   │                   │
    ┌─────▼──────┐    ┌──────▼──────┐     ┌──────▼──────┐
    │LoginHistory│    │   Student   │     │   Branch    │
    │TrustedDev  │    │   (CEFR)   │     │  (multi)    │
    │RefreshToken│    └──┬────┬──┬─┘     └─────────────┘
    └────────────┘       │    │  │
                         │    │  └──────────────────┐
          ┌──────────────┘    │                     │
          │                   │                     │
    ┌─────▼──────┐    ┌──────▼──────┐      ┌───────▼─────┐
    │ Enrollment │◄───┤    Class    │      │  Attendance  │
    │            │    │ (schedule)  │      │  (sessions)  │
    └──┬───┬─────┘    └──┬────┬────┘      └──────┬───────┘
       │   │             │    │                   │
       │   │             │    │            ┌──────▼───────┐
       │   │             │    │            │TeacherSalary │
       │   │             │    │            │ (per-session)│
       │   │             │    │            └──────┬───────┘
  ┌────▼───▼──┐          │    │                   │
  │  Payment  │          │  ┌─▼────────┐   ┌──────▼───────┐
  │           │          │  │ Holiday  │   │ SalaryPeriod │
  └──┬────┬───┘          │  │ TimeSlot │   │  (monthly)   │
     │    │              │  └──────────┘   └──────────────┘
     │    │              │
┌────▼──┐ └────▼────┐   └──▼──────────┐
│Invoice│  │Receipt │   │TuitionPrice │
│       │  │(+QR)   │   │   Table     │
└───────┘  └────────┘   └─────────────┘

Cross-cutting:
  ActivityLog ──── logs all mutations across all models
  Notification ─── linked to recipients (User)
  EmailLog ─────── tracks all outbound emails
  SalaryCode ───── referenced by Class + TeacherSalary
```

---

## Indexing Strategy

### Primary Indexes

| Collection | Index | Type | Purpose |
|------------|-------|------|---------|
| User | `{ email: 1 }` | Unique | Login lookup |
| User | `{ username: 1 }` | Unique | Username lookup |
| Student | `{ studentCode: 1 }` | Unique | Student search |
| Student | `{ fullName: "text" }` | Text | Full-text search |
| Class | `{ classCode: 1 }` | Unique | Class lookup |
| Invoice | `{ invoiceCode: 1 }` | Unique | Invoice lookup |
| Receipt | `{ receiptCode: 1 }` | Unique | Receipt lookup |

### Compound Indexes

| Collection | Index | Purpose |
|------------|-------|---------|
| Attendance | `{ class: 1, date: 1 }` | Find attendance by class + date |
| Enrollment | `{ student: 1, class: 1 }` | Find enrollment by student + class |
| Payment | `{ student: 1, status: 1 }` | Find payments by student + status |
| TeacherSalary | `{ teacher: 1, month: 1, year: 1 }` | Find salary by teacher + period |
| ActivityLog | `{ module: 1, createdAt: -1 }` | Query logs by module + time |
| LoginHistory | `{ user: 1, createdAt: -1 }` | User login history |
| Notification | `{ recipient: 1, isRead: 1 }` | Unread notifications per user |

### TTL Indexes

| Collection | Field | TTL | Purpose |
|------------|-------|-----|---------|
| RefreshToken | `expiresAt` | 0 | Auto-delete expired tokens |
| PasswordReset | `expiresAt` | 0 | Auto-delete expired reset links |

---

<div align="center">

**[← Back to README](README.md)** · **[Architecture →](ARCHITECTURE.md)** · **[Features →](FEATURES.md)**

</div>
