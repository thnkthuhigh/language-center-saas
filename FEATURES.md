<div align="center">

# 📋 Complete Feature Breakdown

### Language Center SaaS — Every Feature in Detail

</div>

---

## Table of Contents

- [Academic Management](#-academic-management)
- [Financial System](#-financial-system)
- [Salary & Payroll](#-salary--payroll)
- [Analytics & Reporting](#-analytics--reporting)
- [User & Access Management](#-user--access-management)
- [Communication & Notifications](#-communication--notifications)
- [AI Integration](#-ai-integration)
- [Cloud & Storage](#-cloud--storage)
- [System Administration](#-system-administration)
- [Accessibility & UX](#-accessibility--ux)

---

## 🎓 Academic Management

### Student Management

| Feature | Details |
|---------|---------|
| **Registration** | Student registration with unique auto-generated codes, personal info, CEFR level assignment |
| **Profile Management** | Full profile with contact info, parent/guardian details, emergency contacts |
| **CEFR Level Tracking** | Automated progression tracking through levels: A1 → A2 → B1 → B2 → C1 → C2 |
| **Enrollment History** | Complete history of all class enrollments, transfers, and status changes |
| **Student Search** | Real-time search by name, code, phone, email with server-side filtering |
| **Bulk Operations** | Batch enrollment, status updates, and data export |
| **Transfer System** | Transfer students between classes with full history preservation and activity logging |
| **Status Management** | Active, Inactive, Graduated, Withdrawn — with audit trail on every change |

### Class Management

| Feature | Details |
|---------|---------|
| **Class Creation** | Create classes with branch, level, teacher, capacity, and schedule configuration |
| **Flexible Scheduling** | Multiple sessions per week with configurable time slots and rooms |
| **Teacher Assignment** | Assign primary teachers with substitution handling capabilities |
| **Capacity Management** | Maximum student limits per class with enrollment validation |
| **Class Status** | Lifecycle tracking: Upcoming → Active → Completed → Cancelled |
| **Holiday Awareness** | Automatic schedule adjustment based on holiday definitions |
| **Class Cancellation** | Formal cancellation workflow with reason tracking and student notification |
| **Tuition Configuration** | Per-class tuition pricing with support for multiple payment cycles |
| **Salary Code Binding** | Link salary codes to classes for automated teacher compensation |

### Attendance System

| Feature | Details |
|---------|---------|
| **Session-Based Tracking** | Mark attendance per teaching session with timestamp |
| **Status Options** | Present, Absent, Excused, Late — with notes field |
| **Real-Time Updates** | Socket.IO live sync — attendance changes reflect instantly across all connected clients |
| **Makeup Sessions** | Automatic credit generation for excused absences |
| **Attendance History** | Full history per student and per class with date range filtering |
| **Teacher Attendance** | Track teacher attendance for salary calculation |
| **Admin/Staff Attendance** | Separate shift-based attendance for office and assistant staff |
| **Attendance Reports** | Exportable attendance summaries with percentage calculations |

### Teaching Schedule

| Feature | Details |
|---------|---------|
| **Calendar View** | Visual calendar displaying all teaching sessions across classes |
| **Teacher Schedule** | Per-teacher weekly/monthly schedule overview |
| **Schedule Adjustments** | Handle schedule changes with history tracking |
| **Timezone Support** | All dates stored in UTC, displayed in Asia/Ho_Chi_Minh (GMT+7) |
| **Conflict Detection** | Prevent scheduling overlaps for teachers and rooms |

### Enrollment Management

| Feature | Details |
|---------|---------|
| **Student Enrollment** | Enroll students into classes with start date and payment cycle selection |
| **Enrollment Status** | Pending → Active → Completed → Cancelled — with date tracking |
| **Auto-Activation** | Cron job automatically activates enrollments on their start date |
| **Payment Linking** | Each enrollment linked to payment records and invoice generation |
| **Makeup Credits** | Track available makeup session credits per enrollment |
| **Extension Proposals** | Request and approve enrollment/payment deadline extensions |
| **Bulk Enrollment** | Enroll multiple students at once with validation |
| **Transfer Between Classes** | Move student enrollment from one class to another with prorated pricing |

---

## 💰 Financial System

### Payment Processing

| Feature | Details |
|---------|---------|
| **Payment Cycles** | Support for 1-week, 4-week, 8-week, and 12-week payment periods |
| **Payment Status** | Pending → Partial → Paid → Overdue — with automatic status transitions |
| **Multiple Methods** | Cash, bank transfer, and other payment methods supported |
| **Partial Payments** | Track partial payments with remaining balance calculation |
| **Cross-Class Payments** | Handle payments spanning multiple class enrollments |
| **Payment History** | Complete transaction history per student with date filtering |
| **Overdue Detection** | Automated overdue flagging with configurable grace periods |
| **Payment Calculator** | Automated tuition calculation based on enrollment duration and pricing tiers |

### Invoice System

| Feature | Details |
|---------|---------|
| **Auto-Generation** | Invoices automatically created with sequential numbering (e.g., INV-2026-00001) |
| **Invoice Details** | Student info, class info, tuition breakdown, payment terms |
| **PDF Export** | Professional invoice PDF generation via Puppeteer |
| **Email Delivery** | Send invoices directly to student/parent email |
| **Status Tracking** | Draft → Sent → Paid → Overdue → Cancelled |
| **Batch Processing** | Generate invoices for multiple students at once |

### Receipt System

| Feature | Details |
|---------|---------|
| **Receipt Generation** | Professional receipts with QR codes for verification |
| **Sequential Numbering** | Auto-incremented receipt codes (e.g., RCP-2026-00001) |
| **PDF Download** | High-quality PDF receipts with center branding |
| **Email Delivery** | Automatic receipt email to parent/student on payment |
| **QR Code** | Each receipt includes a unique QR code for authenticity verification |
| **Receipt History** | Searchable receipt archive with date and student filters |

### Tuition Price Management

| Feature | Details |
|---------|---------|
| **Price Tables** | Configurable tuition rates based on class level and hours |
| **Date Range Support** | Pricing effective within specified date ranges |
| **Multiple Tiers** | Different pricing for 1/4/8/12-week payment cycles |
| **Proration** | Automatic fee adjustment for mid-term enrollments |
| **Miscellaneous Fees** | Track additional fees (books, materials, registration) |

---

## 📊 Salary & Payroll

### Teacher Salary

| Feature | Details |
|---------|---------|
| **Attendance-Based** | Salary calculated from actual teaching sessions attended |
| **Salary Codes** | Configurable salary tiers: K1, K2, K3 variants with per-session rates |
| **Per-Session Breakdown** | Detailed view of each teaching session's contribution to salary |
| **Salary Periods** | Monthly salary periods with open/close lifecycle |
| **Batch Calculation** | Calculate salary for all teachers in a period at once |
| **Adjustment Support** | Manual adjustments with reason tracking |

### Admin & Staff Salary

| Feature | Details |
|---------|---------|
| **Shift-Based Tracking** | Track office hours and assistant teaching shifts |
| **Staff Attendance** | Separate attendance system for non-teaching staff |
| **Salary Calculation** | Compute salary based on shifts worked and hourly rates |

### Approval Workflow

| Feature | Details |
|---------|---------|
| **Multi-Tier Approval** | Draft → Reviewed → Approved → Paid |
| **Role-Based** | Admin creates → Accountant reviews → Owner approves |
| **Real-Time Notification** | Socket.IO notifications at each approval stage |
| **Rejection Flow** | Reject with comments, return to previous stage |
| **Audit Trail** | Complete history of all approval actions with timestamps |

### Salary Configuration

| Feature | Details |
|---------|---------|
| **Salary Code Management** | Create and manage salary code definitions |
| **Sub-Code System** | K1, K2, K3 tiers within each salary code |
| **Rate Configuration** | Per-session rates configurable per salary code |
| **Class Binding** | Link salary codes to specific classes |

---

## 📈 Analytics & Reporting

### Dashboards

| Dashboard | Key Metrics |
|-----------|-------------|
| **Owner Dashboard** | Total revenue, student count, class count, enrollment rate, payment collection rate, month-over-month trends |
| **Teacher Dashboard** | Teaching schedule, classes assigned, attendance stats, salary summary |
| **Accountant Dashboard** | Pending payments, overdue invoices, salary approval queue, daily/weekly revenue |
| **Admin Dashboard** | Student management stats, class utilization, enrollment pipeline |

### Analytics Features

| Feature | Details |
|---------|---------|
| **Revenue Analytics** | Period-over-period comparison, revenue by class/branch, collection efficiency |
| **Attendance Analytics** | Student attendance rates, class attendance trends, teacher punctuality |
| **Student Analytics** | Enrollment funnels, retention rates, level distribution, demographics |
| **Class Analytics** | Class utilization rates, capacity fill percentage, performance metrics |
| **Custom Date Ranges** | Filter all analytics by custom date ranges |
| **Trend Charts** | Interactive Recharts and Chart.js visualizations |

### Report Generation

| Report Type | Export Formats |
|-------------|---------------|
| **Tuition Fee Report** | PDF, Excel |
| **Student Report** | PDF, Excel |
| **Class Report** | PDF, Excel |
| **Attendance Report** | PDF, Excel |
| **Revenue Report** | PDF, Excel |
| **Salary Report** | PDF, Excel |

### Export Capabilities

| Feature | Details |
|---------|---------|
| **PDF Export** | Professional PDF documents via jsPDF (client-side) and Puppeteer (server-side) |
| **Excel Export** | Structured XLSX files with multiple sheets and formatting |
| **Print Support** | Print-optimized CSS for direct browser printing |
| **Batch Export** | Export multiple reports at once |

---

## 👥 User & Access Management

### User Management

| Feature | Details |
|---------|---------|
| **User CRUD** | Create, read, update, and delete user accounts |
| **Role Assignment** | Assign roles: Owner, Admin, Teacher, Accountant |
| **Branch Assignment** | Assign users to specific branches |
| **Account Locking** | Temporarily or permanently lock user accounts |
| **Auto-Unlock** | Cron job to automatically unlock temporarily locked accounts |
| **Profile Settings** | Users can update their own profile and preferences |

### Authentication

| Feature | Details |
|---------|---------|
| **Email/Password Login** | Standard credential-based authentication |
| **Google OAuth 2.0** | One-click social login via Google |
| **JWT Tokens** | Access + refresh token strategy with rotation |
| **2FA (TOTP)** | Time-based one-time passwords via authenticator apps |
| **Backup Codes** | Emergency access codes for 2FA recovery |
| **Password Reset** | Email-based password recovery flow |
| **Session Management** | View and revoke active login sessions |

### Device & Security

| Feature | Details |
|---------|---------|
| **Device Trust** | Mark devices as trusted to skip 2FA |
| **Login History** | Full audit of login attempts with IP, user-agent, timestamp |
| **Session Tracking** | View all active sessions with device info |
| **Remote Logout** | Terminate sessions on other devices |

### Branch Management

| Feature | Details |
|---------|---------|
| **Multi-Branch** | Support for multiple physical locations |
| **Branch Codes** | 2-letter unique branch identifiers |
| **Branch-Scoped Data** | All data filtered by user's assigned branch |
| **Cross-Branch Reports** | Owner can view aggregated data across branches |

---

## 🔔 Communication & Notifications

### Real-Time Notifications (Socket.IO)

| Event | Description |
|-------|-------------|
| **Attendance Update** | Live sync when teacher marks attendance |
| **Payment Received** | Instant notification on payment processing |
| **Salary Approved** | Alert when salary moves through approval stages |
| **New Enrollment** | Notification for new student enrollments |
| **System Alerts** | Health and security alerts |

### Email Notifications

| Email Type | Trigger |
|------------|---------|
| **Payment Reminders** | Upcoming and overdue payment dates |
| **Receipt Delivery** | Automatic receipt on successful payment |
| **Invoice Delivery** | Invoice sent to student/parent email |
| **Birthday Greetings** | Automated birthday emails to students |
| **Welcome Email** | New student registration confirmation |
| **Password Reset** | Security email for password recovery |
| **Account Notifications** | Lock/unlock, role changes, security alerts |

### Email Infrastructure

| Provider | Purpose |
|----------|---------|
| **SendGrid** | Primary transactional email delivery |
| **Azure Communication** | Enterprise email service (backup) |
| **Nodemailer** | SMTP-based email for development and fallback |

### In-App Notifications

| Feature | Details |
|---------|---------|
| **Notification Bell** | Real-time notification indicator with unread count |
| **Read/Unread Management** | Mark individual or all notifications as read |
| **Notification History** | Scrollable history with timestamp and category |
| **Role-Based Filtering** | Users only see notifications relevant to their role |

---

## 🤖 AI Integration

| Feature | Details |
|---------|---------|
| **Google Vertex AI** | Integration with Google Cloud's AI/ML platform |
| **Smart Suggestions** | AI-powered recommendations for class management decisions |
| **Data Analysis** | Intelligent analysis of enrollment and performance trends |
| **Document Formatting** | AI-assisted content structuring and formatting |
| **Rate Limited** | 15 requests/minute to balance cost and availability |

---

## ☁️ Cloud & Storage

### File Storage (AWS S3)

| Feature | Details |
|---------|---------|
| **Presigned URLs** | Secure, time-limited upload and download links |
| **Folder Organization** | In-app folder browser for organizing uploaded files |
| **File Management** | Upload, download, rename, delete operations |
| **Access Control** | Role-based file access permissions |

### Backup System

| Feature | Details |
|---------|---------|
| **Automated Backups** | Scheduled database backups via Docker service |
| **Multi-Cloud Storage** | Backups stored on Oracle Cloud and Google Cloud Storage |
| **Backup Verification** | Automated backup integrity checks |
| **Disaster Recovery** | Documented restore procedures for emergency scenarios |

---

## ⚙️ System Administration

### Activity Logging

| Feature | Details |
|---------|---------|
| **Comprehensive Audit Trail** | Every data mutation logged with full details |
| **Before/After Diffs** | State comparison showing exactly what changed |
| **User Attribution** | Who made the change, when, and from which IP |
| **Module Classification** | Logs organized by module (Student, Class, Payment, etc.) |
| **Searchable History** | Filter logs by date, user, module, and action type |
| **Export** | Download activity logs for compliance reporting |

### Holiday Management

| Feature | Details |
|---------|---------|
| **Holiday Calendar** | Define holidays that affect class scheduling |
| **Auto-Adjustment** | Classes automatically skip holiday dates |
| **Recurring Holidays** | Set annual recurring holidays |

### Time Slot Configuration

| Feature | Details |
|---------|---------|
| **Customizable Slots** | Define available teaching time slots |
| **Per-Branch Settings** | Different time slots for different branches |
| **Schedule Integration** | Time slots used in class scheduling UI |

### System Configuration

| Feature | Details |
|---------|---------|
| **Environment-Based** | Configuration via environment variables |
| **Feature Flags** | Enable/disable features without code deployment |
| **System Settings** | Configurable system-wide parameters |

### Developer Tools

| Feature | Details |
|---------|---------|
| **Dev Dashboard** | Debug tools and system inspection (development only) |
| **API Documentation** | Swagger/OpenAPI 3.0 interactive docs at `/api-docs` |
| **Generic CRUD** | Dev-only CRUD operations for rapid prototyping |
| **Database Inspection** | View collection stats and document counts |
| **Protected Access** | 5-layer protection: JWT + Dev role + IP whitelist + audit + alerts |

---

## ♿ Accessibility & UX

### WCAG 2.1 Level AA Compliance

| Feature | Implementation |
|---------|---------------|
| **Focus Management** | `useFocusTrap` hook for modal dialogs |
| **Skip Navigation** | `SkipToContent` component for keyboard users |
| **Screen Reader Support** | `useAnnounce` hook for live region announcements |
| **Keyboard Navigation** | `useTableKeyboardNav` for full table keyboard access |
| **ARIA Attributes** | Comprehensive ARIA labels on all interactive elements |
| **Color Contrast** | Designed for sufficient color contrast ratios |

### UX Design Principles

| Principle | Implementation |
|-----------|---------------|
| **Loading States** | Skeleton loaders on every data-fetching page |
| **Empty States** | Informative empty state messages with action suggestions |
| **Error States** | User-friendly error messages with retry options |
| **Responsive Design** | Tablet-optimized layouts (primary target device) |
| **Consistent Design System** | White/Gray/Black/Blue color palette across all 80+ pages |
| **Smooth Animations** | Framer Motion transitions for page and component animations |
| **Command Palette** | `cmdk` integration for power-user keyboard shortcuts |
| **Toast Notifications** | Non-intrusive feedback with react-hot-toast |

---

<div align="center">

**[← Back to README](README.md)** · **[Architecture →](ARCHITECTURE.md)** · **[Database Schema →](DATABASE-SCHEMA.md)**

</div>
