# PlacementIQ — Student Placement Readiness, Assessment & Interview Intelligence Platform

PlacementIQ is an institutional SaaS platform designed for engineering colleges and universities to systematically assess, classify, train, and track students based on placement readiness using measurable evidence.

---

## 1. Core Platform Architecture

PlacementIQ is built using **React 19 + TypeScript + Vite**, conforming strictly to `DESIGN_SYSTEM.md` and `PlacementIQ_PRD.md`:

```
PlacementIQ Architecture
│
├── Frontend Client (React 19 + TypeScript + Vite)
│   ├── CSS Design Tokens (:root CSS variables matching DESIGN_SYSTEM.md)
│   ├── Reusable Component System (KpiCard, LevelBadge, Button, Modal, Tabs, ProgressBar)
│   ├── Application Shell (Role-aware Sidebar, Topbar with quick role simulation)
│   └── Role Views (Student, Faculty, Placement Officer, Administrator)
│
├── Business Logic & Scoring Engine
│   ├── Deterministic L1/L2/L3 Classification Engine (readinessEngine.ts)
│   ├── Safe Code Runner Sandbox (client test case evaluation)
│   └── Pluggable AI Provider Abstraction (Local / Gemini / OpenAI)
│
└── Persistent Storage & Audit Layer
    ├── Typed LocalStorage / IndexedDB Repository (StorageService)
    ├── Pre-seeded Institutional Dataset (Students, Assessments, Question Bank, Interviews, Logs)
    └── Immutable Audit Trail (Tracking score overrides, test publications, CSV imports)
```

---

## 2. Supported Roles & Workflows

### A. Student Experience (`/student/*`)
- **Dashboard (`/student/dashboard`)**: Overall placement readiness score (`/100`), Tier badge (`L1`, `L2`, or `L3`), KPI trends, 6-dimension competency bars, transparent classification rationale, and verified strengths vs critical gaps.
- **My Skills & Profile (`/student/skills`)**: Academic records (Roll No, CGPA, department), target placement role, programming languages, and technical concept tags with add/remove capability.
- **Coding Practice Sandbox (`/student/coding`)**: Problem statements (Two Sum, Valid Parentheses, etc.), language switcher (JS, Python, Java), syntax editor, sample & hidden test cases, Big-O complexity hints.
- **Assessment Engine (`/student/assessments`)**: Timed tests with live countdown timer, question navigator palette, auto-grading for MCQs and coding test cases, automated readiness recalculation upon submission.
- **Mock Interviews (`/student/interviews`)**: Review historical evaluations, faculty rubric scores (scale 1–10), and launch interactive AI practice rounds with response capture.
- **GitHub Repository Analysis (`/student/github`)**: Public repository URL audit assessing documentation, directory structure, testing, and dev practices. Includes PRD disclaimer: *static repository analysis does not verify individual code authorship without oral project defense.*
- **Improvement Planner (`/student/plan`)**: Personalized learning milestones derived from actual assessment weaknesses with interactive status toggles (`pending` → `in_progress` → `completed`).

### B. Faculty & Interviewer Workflows (`/faculty/*`)
- **Dashboard (`/faculty/dashboard`)**: Enrolled students, active assessments, conducted interviews, batch readiness tier distribution.
- **Student Directory (`/faculty/students`)**: Searchable candidate roster, filter by tier and department, drill-down profile inspection, and **audited faculty score overrides**.
- **AI Interview Studio (`/faculty/interviews`)**:
  - Configure interview parameters (Role, type, difficulty, duration).
  - AI-assisted question generation with expected key points.
  - Live interview mode with candidate details, question timer, and candidate answer notes.
  - Rubric scoring (1–10 per criterion).
  - **Strict separation between Private Evaluator Notes (confidential) and Student-Visible Feedback.**
- **Question Bank (`/faculty/questions`)**: Searchable and filterable question library across DSA, OS, DBMS, Networks, and Aptitude. Modal to add custom questions with options or test cases.
- **Test Builder (`/faculty/test-builder`)**: Two-panel test assembly workspace (Library on the left, selected questions on the right), target batch assignment, and publishing.
- **Results Review & Overrides (`/faculty/results`)**: Detailed inspection of student answers with manual score adjustments and audit tracking.

### C. Placement Officer Analytics (`/placement/*`)
- **Overview (`/placement/overview`)**: Total registered candidates, L3 placement ready count, L2 developing pipeline, at-risk alerts.
- **Students Roster (`/placement/students`)**: Multi-column candidate roster with CGPA, target roles, and readiness tiers.
- **Readiness Distribution (`/placement/distribution`)**: Cross-department stacked tier comparisons (CSE vs IT).
- **At-Risk Interventions (`/placement/at-risk`)**: Filtered view of students with scores < 50 or in Tier L1, with one-click remedial bootcamp assignments.
- **Reports & Data Export (`/placement/reports`)**: **Functional CSV Exporter** that generates and downloads certified student readiness spreadsheets.
- **Placement Outcomes (`/placement/outcomes`)**: Company pipeline alignment against Day-0 product drives.

### D. Administrator Management (`/admin/*`)
- **Users & Roles (`/admin/users`)**: Directory of all accounts and **Bulk Student CSV Importer** with validation and preview.
- **Departments & Batches (`/admin/departments`)**: Manage branches and graduation cohorts.
- **Assessment Settings (`/admin/settings`)**: Configurable scoring formula weights (Coding 25%, CS Fund 20%, Projects 15%, Mock Int 20%, Apt 10%, Comm 10%) and L1/L2/L3 thresholds. Changes immediately recalculate cohort readiness.
- **Integrations (`/admin/integrations`)**: GitHub API credentials, AI provider configuration (Gemini / OpenAI / Local).
- **Audit Logs (`/admin/audit`)**: Immutable ledger recording sensitive administrative events and faculty overrides.

---

## 3. L1 / L2 / L3 Readiness Classification Engine

The platform implements the mathematical model specified in PRD Module 4:

$$\text{Overall Score} = \sum (\text{Dimension Score}_i \times \text{Weight}_i)$$

- **Weights (Default)**:
  - DSA & Coding: `25%`
  - CS Fundamentals: `20%`
  - Projects & GitHub: `15%`
  - Mock Interviews: `20%`
  - Aptitude: `10%`
  - Technical Communication: `10%`
- **Thresholds**:
  - **L1 — Foundation**: Score `< 50.0`
  - **L2 — Developing**: Score `50.0 – 74.99`
  - **L3 — Placement Ready**: Score `≥ 75.0`
- **Critical Barrier**: A student cannot achieve L3 if Coding `< 65` or CS Fundamentals `< 60`, regardless of overall score.
- **Unassessed Dimensions**: Treated as missing rather than defaulted to zero. Confidence is marked as `high` (≥5 assessed), `medium` (≥3), or `provisional` (<3).

---

## 4. Design System Tokens (`DESIGN_SYSTEM.md`)

- **Primary Blue**: `#2449D8` (Buttons, active navigation, links)
- **Primary Blue Soft**: `#E8EDFF`
- **Teal Accent**: `#0F9D8A` (Progress bars, growth trends)
- **App Canvas**: `#F5F7FB`
- **Surface**: `#FFFFFF`
- **Border**: `#E4E8F0`
- **Text Primary**: `#172033`
- **Text Secondary**: `#667085`
- **Typography**: `Inter` from Google Fonts with tabular numerals for scores and timers.
- **Radii**: 6px (sm), 10px (md), 12px (lg), 16px (xl).

---

## 5. Development & Running Locally

### Prerequisites
- Node.js `v18+` (Tested on `v24.15.0`)
- npm `v9+` (Tested on `v11.12.1`)

### Setup Commands
```bash
# 1. Install dependencies
npm install

# 2. Run TypeScript build verification
npm run build

# 3. Launch local development server
npm run dev
```

The application will be available at `http://localhost:5173/`.

### Quick Role Testing
In the top navigation bar, use the **Switch Role** dropdown to switch between:
- **Aarav Sharma** (Student - L3 Placement Ready)
- **Priya Verma** (Student - L2 Developing)
- **Kabir Singh** (Student - L1 Foundation)
- **Dr. Rajesh Kumar** (Faculty - Test Builder & Interview Studio)
- **Vikram Malhotra** (Placement Officer - Analytics & CSV Reports)
- **Dr. Sarah Jenkins** (Administrator - Settings & CSV Imports)
