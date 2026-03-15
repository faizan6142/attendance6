# AttenZen – Multi-Course Attendance Management System

A fully-featured, single-file web application for managing student attendance across multiple courses, semesters, and subjects. Built with vanilla HTML/CSS/JavaScript and backed by Firebase Realtime Database for real-time cloud sync.

---

## ✨ Features

### 🔐 Authentication
- **Admin and Student roles** – separate dashboards based on role
- Firebase Authentication (email + password)
- "Remember Me" login persistence
- Auto-login on page reload
- Change password from the dashboard

### 👩‍💼 Admin Dashboard

#### User Management
- **Add individual users** (admin or student) with email, password, name, roll number, and registration number
- **Bulk add students** by entering comma-separated roll numbers (auto-generates credentials)
- **Live search/filter** in the user list (by name, email, or roll number)
- **Reset password** – sends a Firebase password-reset email
- **Delete users** with automatic cleanup of student records
- **Edit student details** – update name, roll number, registration number, username, and email; attendance records are migrated automatically when roll number changes

#### Course & Curriculum Management
- Create **Courses** with name and code
- Add **Semesters** to any course
- Add **Subjects** (name, code, credits) to any semester
- Edit and delete courses, semesters, and subjects with assignment-safety checks
- Assign students to a course/semester and select which subjects they attend

#### Attendance Management
- Filter attendance by **course → semester → subject**
- Spreadsheet-style attendance table (columns = dates, rows = students)
- Click any cell to toggle status: **Present (P) → Absent (A) → Holiday (H)**
- **All Present** button – marks every enrolled student as present for the selected date
- **Attendance % column** – per-student percentage, colour-coded (green ≥75%, orange ≥50%, red <50%)
- **Bulk Update modal** – select multiple students and set their status in one step
- **Print** the attendance sheet directly from the browser
- **Export to Excel** (`.xlsx`)
- **Mark Holiday** – marks all students in a subject as Holiday for a given date
- **Add/Delete dates** from the attendance records

#### Analytics & Reports
- **Line chart** showing present vs absent trend for the last 10 class days
- **Report tab** – filter by course, semester, subject, and date range; generate a summary table with attendance percentages
- **Download report as PDF** (jsPDF + autoTable)

#### 🔔 Send Reminder (fully implemented)
1. Choose a **subject** (or "All Subjects")
2. Set an **attendance threshold** (50–90 %, default 75 %)
3. Click **Find Students** to see everyone below the threshold with their stats
4. Then:
   - **Copy Emails** – copies all their email addresses to clipboard
   - **Open in Email Client** – opens your default mail app pre-filled with BCC list and a reminder message
   - **Download List** – exports the low-attendance list as a `.csv` file

#### Cloud Sync
- Floating sync button refreshes all data from Firebase in real time

### 🎓 Student Dashboard
- Personal profile card with photo (fetched from university portal using registration number)
- Current course and semester info
- Subject selector to view attendance for any enrolled subject
- **Attendance progress bar** for the selected subject
- **Low Attendance Warning banner** – auto-appears when attendance drops below 75 %
- **All Subjects Overview card** – instant progress bars for every enrolled subject
- Change password button

---

## 🚀 Getting Started

### Prerequisites
- A [Firebase](https://firebase.google.com/) project with:
  - **Authentication** enabled (Email/Password provider)
  - **Realtime Database** enabled (Asia Southeast 1 or your nearest region)

### Setup

1. **Clone or download** this repository.

2. **Open `index.html`** in any text editor and replace the `firebaseConfig` object in the **Firebase initialization section** (search for `const firebaseConfig`) with your own Firebase project credentials:

```js
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    databaseURL: "https://YOUR_PROJECT-default-rtdb.YOUR_REGION.firebasedatabase.app",
    projectId: "YOUR_PROJECT",
    storageBucket: "YOUR_PROJECT.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

3. **Set Firebase Realtime Database rules** (start with these permissive rules for testing, then tighten for production):

```json
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null"
  }
}
```

4. **Open `index.html`** in a browser. The app will create a default admin account (`admin@attenzen.com` / `admin123`) on first run if no admin exists.

> **Important:** Change the default admin password immediately after first login.

### Hosting
Because the entire app is a single HTML file, you can host it anywhere:
- **GitHub Pages** – push to a repo and enable Pages
- **Firebase Hosting** – `firebase deploy`
- **Any static web host** – just upload `index.html`

---

## 🗂 Firebase Data Structure

```
/
├── users/
│   └── {uid}/
│       ├── email
│       ├── role          ("admin" | "student")
│       ├── fullName
│       ├── username
│       └── rollNo        (null for admin)
│
├── students/
│   └── {rollNo}/
│       ├── name
│       ├── uid
│       └── regNo
│
├── courses/
│   └── {courseId}/
│       ├── name
│       ├── code
│       └── semesters/
│           └── {semId}/
│               ├── name
│               └── subjects/
│                   └── {subjectId}/
│                       ├── name
│                       ├── code
│                       └── credits
│
├── student_courses/
│   └── {rollNo}/
│       ├── courseId
│       ├── semester      (semId)
│       └── subjects      [subjectId, …]
│
├── attendance/
│   └── {YYYY-MM-DD}/
│       └── {subjectId}/
│           └── {rollNo}  ("P" | "A" | "H")
│
└── holidays/
    └── {YYYY-MM-DD}      (true)
```

---

## 📦 External Dependencies (CDN)

| Library | Version | Purpose |
|---|---|---|
| Bootstrap | 5.3.0-alpha1 | UI framework |
| Font Awesome | 6.0.0 | Icons |
| Google Fonts (Sora, DM Mono) | – | Typography |
| SheetJS (xlsx) | 0.18.5 | Excel export |
| Chart.js | latest | Attendance trend chart |
| jsPDF | 2.5.1 | PDF export |
| jsPDF-AutoTable | 3.5.28 | Table in PDF |
| Firebase (compat) | 10.8.0 | Auth + Realtime Database |

All dependencies are loaded from CDN – no `npm install` needed.

---

## 🖥 Default Credentials

| Role | Email | Password |
|---|---|---|
| Admin | `admin@attenzen.com` | `admin123` |

> Change these immediately in a production deployment.

---

## 🛡 Security Notes

- Firebase API keys embedded in client-side code are **not secret** – they identify your project. Use Firebase Security Rules to control data access.
- Restrict database reads/writes to authenticated users (see rules above).
- For production, create admin accounts manually and remove the auto-create-admin logic.
- Student credential defaults (`{roll}@attenzen.com` / `{roll}123`) should be changed on first login.

---

## 📝 License

This project is open source. Feel free to use, modify, and distribute it.
