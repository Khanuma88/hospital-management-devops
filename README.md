# Hospital Management — DevOps Project

## 1. Project overview

## 2. Requirement analysis

### 2.1 Functional requirements

### 2.2 Non-functional requirements

## 3. Roles and access control

### 3.1 Roles

- **Receptionist**: works at the front desk. Registers patients, edits their info and books appointments. Cannot see any medical information.
- **Doctor**: sees their own appointments and their own patients. Writes diagnoses and visit notes.
- **Administrator**: manages the system itself, so doctors, user accounts and roles. Does not deal with patients' medical data.

Patients are **not** system users. They have no account and cannot log in. Only staff enter and change their data.

### 3.2 RBAC matrix

| Capability | Receptionist | Doctor | Administrator |
|---|---|---|---|
| Register, search and edit a patient | Yes | No | Yes |
| Deactivate a patient record | Yes | No | Yes |
| Register, update or deactivate a doctor | No | No | Yes |
| Schedule an appointment | Yes | No | Yes |
| View appointments | All | Own only | All |
| Update or cancel an appointment | Yes | Own only | Yes |
| Record diagnosis and visit notes | No | Yes | No |
| Read a patient's medical history | No | Own patients | No |
| Export authorised data to CSV | No | Yes | Yes |
| Manage user accounts and roles | No | No | Yes |

### 3.3 My decisions

The scenario did not say what to do in three cases, so I decided myself:

- **Doctor, update or cancel appointment = Own only.** A doctor can only view their own appointments, so they should only change their own too.
- **Administrator, read medical history = No.** The admin manages the system, not treatment. Fewer people seeing medical data is safer.
- **Administrator, export CSV = Yes.** The admin can export only admin data (users, doctors, appointments), not medical history.

Every "No" in this table means the app must refuse the request, and there should be an automated test that proves it.


## 4. Product backlog

## 5. Process and ceremonies

### 5.1 Sprint length

Sprint duration: **2 weeks**. It is long enough to finish a few stories and short enough to correct course quickly.

### 5.2 Sprint backlog (Sprint 1)

Taken from the top of the Product Backlog:

| ID | Story |
|---|---|
| US-01 | Manage user accounts and roles |
| US-04 | Register, search and edit patients |
| US-02 | Manage doctors |

### 5.3 Ceremonies

| Ceremony | When | Duration | Purpose |
|---|---|---|---|
| Sprint Planning | First day of the sprint | 1-2 hours | Choose stories from the top of the Product Backlog and split them into tasks. |
| Daily Scrum | Every day | 15 minutes | Say what was done, what is next, and what blocks us. |
| Sprint Review | Last day of the sprint | 1 hour | Demo the finished stories and collect feedback. |
| Sprint Retrospective | After the review | 45 minutes | Discuss what went well, what did not, and what to improve next sprint. |

### 5.4 What triggers backlog refinement

- A story is too big to finish in one sprint, so it needs splitting.
- Feedback from the Sprint Review changes priorities.


## 6. Task decomposition

Hierarchy: **Epic → User Story → Task**.
Branch names follow the story identifier, e.g. `feature/us-01-user-management`.
Test tasks are marked **[AUTO]** (automated code test, later run in CI) or **[MANUAL]** (executed by hand).
Only the stories selected for Sprint 1 and the first scheduling story are decomposed here.

### 6.1 Epics and stories

| Epic | Name | Stories | Depends on |
|---|---|---|---|
| EP-1 | Users and access control | US-01 user-management | none |
| EP-2 | Patients and doctors | US-04 patient-management, US-02 doctor-management | US-01 |
| EP-3 | Appointments | US-05 appointment-scheduling | US-04, US-02 |

### 6.2 EP-1 Users and access control

#### US-01 user-management (no dependencies)

| Task | Description | Depends on |
|---|---|---|
| T-01.1 | Create User and Role data model | none |
| T-01.2 | Implement login and role-based authorization check | T-01.1 |
| T-01.3 | Implement create, edit and disable user endpoints (admin only) | T-01.2 |
| T-01.4 | [AUTO] Integration tests: admin can manage users; Receptionist and Doctor are refused (403) | T-01.3 |
| T-01.5 | [MANUAL] Create a user in the UI and log in with that role | T-01.3 |

### 6.3 EP-2 Patients and doctors

#### US-04 patient-management (depends on US-01)

| Task | Description | Depends on |
|---|---|---|
| T-04.1 | Create Patient data model | none |
| T-04.2 | Implement register, search and edit patient endpoints with validation | T-04.1 |
| T-04.3 | [AUTO] Unit tests: missing required fields are rejected | T-04.2 |
| T-04.4 | [AUTO] Integration tests: Receptionist and Admin allowed, Doctor refused | T-04.2 |
| T-04.5 | [MANUAL] Register a patient and find them by last name | T-04.2 |

#### US-02 doctor-management (depends on US-01)

| Task | Description | Depends on |
|---|---|---|
| T-02.1 | Create Doctor data model | none |
| T-02.2 | Implement register, update and deactivate doctor endpoints (admin only) | T-02.1 |
| T-02.3 | [AUTO] Integration tests: Receptionist and Doctor are refused | T-02.2 |
| T-02.4 | [MANUAL] Deactivated doctor does not appear in the booking list | T-02.2 |

### 6.4 EP-3 Appointments

#### US-05 appointment-scheduling (depends on US-04, US-02)

| Task | Description | Depends on |
|---|---|---|
| T-05.1 | Create Appointment data model (patient, doctor, date, time, status) | none |
| T-05.2 | Implement schedule, change and cancel endpoints | T-05.1 |
| T-05.3 | Validation: no past dates, no inactive patient or doctor | T-05.2 |
| T-05.4 | [AUTO] Unit tests for the validation rules | T-05.3 |
| T-05.5 | [AUTO] Integration test: Doctor cannot schedule appointments | T-05.2 |
- A new requirement or a change in the scenario appears.
- A bug or technical problem blocks a planned story.
- Acceptance criteria are unclear when planning starts.

Refinement is done regularly, once per sprint, around the middle of the sprint.
