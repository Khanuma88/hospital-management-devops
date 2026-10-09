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

Hierarchy: **Epic → User Story → Task**. Branches will be named `feature/US-05-short-name`, issue titles `T-05.3 short description`.
Task kinds: **Code**, **Test - automated** (runs in CI), **Test - manual** (checklist).
Stories US-10 to US-14 were added to the Lab 1 backlog because the pipeline and CSV export are also work.

### 6.1 Epics

| Epic | Name | Stories |
|---|---|---|
| E1 | Authentication and access | US-01, US-03 |
| E2 | Patient management | US-04 |
| E3 | Doctor management | US-02 |
| E4 | Appointment scheduling | US-05, US-06, US-07, US-09 |
| E5 | Medical records and export | US-08, US-12 |
| E6 | Platform and pipeline | US-10, US-11, US-13, US-14 |

New stories:
- **US-10**: As a developer, I want a CI pipeline that builds and tests on every push, so that broken code is caught early.
- **US-11**: As a developer, I want the app containerised and published automatically, so that it can be deployed the same way every time.
- **US-12**: As a Doctor or Administrator, I want to export authorised data to CSV, so that I can use it in reports.
- **US-13**: As a developer, I want end-to-end tests of the main flows, so that role and booking rules are checked in a real browser.
- **US-14**: As an Administrator, I want basic monitoring metrics, so that I can see if the system is healthy.

### 6.2 Decomposed stories

#### US-01 user-management (Epic E1) - Blocked by: none

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-01.1 | User and Role entity and migration | Code | none |
| T-01.2 | Login, password hashing, sessions | Code | T-01.1 |
| T-01.3 | Role check on every route (refuse with 403) | Code | T-01.2 |
| T-01.4 | Admin endpoints: create, edit, disable user | Code | T-01.3 |
| T-01.5 | Tests: every "No" in the RBAC matrix is refused; disabled user cannot log in | Test - automated | T-01.4 |
| T-01.6 | Create a user in the UI and log in with that role | Test - manual | T-01.4 |

#### US-10 ci-pipeline (Epic E6) - Blocked by: none

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-10.1 | Project skeleton and test runner | Code | none |
| T-10.2 | GitHub Actions workflow: install, lint, test on every push | Code | T-10.1 |
| T-10.3 | Require a green build before merging a pull request | Code | T-10.2 |
| T-10.4 | A deliberately failing test turns the build red | Test - automated | T-10.2 |

#### US-04 patient-management (Epic E2) - Blocked by: US-01

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-04.1 | Patient entity and migration | Code | none |
| T-04.2 | Service: register, search, edit patient with validation | Code | T-04.1 |
| T-04.3 | Patient form and search page | Code | T-04.2 |
| T-04.4 | Unit tests: missing required fields are rejected | Test - automated | T-04.2 |
| T-04.5 | Integration tests: Receptionist and Admin allowed, Doctor refused | Test - automated | T-04.2 |
| T-04.6 | Check error wording and empty search result screen | Test - manual | T-04.3 |

#### US-02 doctor-management (Epic E3) - Blocked by: US-01

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-02.1 | Doctor entity and migration | Code | none |
| T-02.2 | Service: register, update, deactivate doctor (admin only) | Code | T-02.1 |
| T-02.3 | Doctor management page | Code | T-02.2 |
| T-02.4 | Integration tests: Receptionist and Doctor are refused | Test - automated | T-02.2 |

#### US-05 appointment-scheduling (Epic E4) - Blocked by: US-02, US-04

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-05.1 | Appointment entity and migration (patient, doctor, datetime, status) | Code | none |
| T-05.2 | Service: schedule, change, cancel appointment | Code | T-05.1 |
| T-05.3 | Validation: no past dates, no inactive patient or doctor | Code | T-05.1 |
| T-05.4 | Booking form with patient and doctor pickers | Code | T-05.2 |
| T-05.5 | Unit tests for the validation rules | Test - automated | T-05.3 |
| T-05.6 | Integration test: Doctor cannot schedule | Test - automated | T-05.2 |
| T-05.7 | Check validation wording and booking screen | Test - manual | T-05.4 |

#### US-07 doctor-own-appointments (Epic E4) - Blocked by: US-05

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-07.1 | Service: list appointments of the logged-in doctor | Code | none |
| T-07.2 | Doctor schedule page | Code | T-07.1 |
| T-07.3 | Integration test: Doctor A cannot see Doctor B's appointments | Test - automated | T-07.1 |

#### US-08 visit-notes (Epic E5) - Blocked by: US-04, US-05

| Task | Description | Kind | Blocked by |
|---|---|---|---|
| T-08.1 | VisitNote entity and migration | Code | none |
| T-08.2 | Service: add note (own patients only) | Code | T-08.1 |
| T-08.3 | Service: read medical history (own patients only) | Code | T-08.1 |
| T-08.4 | Visit notes page | Code | T-08.2, T-08.3 |
| T-08.5 | Integration tests: Receptionist, Admin and other doctors are refused | Test - automated | T-08.2, T-08.3 |
| T-08.6 | Check that a note shows date and doctor name | Test - manual | T-08.4 |

Not decomposed yet (refined in later sprints): US-03, US-06, US-09, US-11, US-12, US-13, US-14.

## 7. Estimation

Story points are relative: they compare stories with each other and are not hours. They consider amount of work, technical complexity, uncertainty, dependencies and testing effort.

| Points | Meaning |
|---|---|
| 1 | Trivial: one or two files, barely any test |
| 2 | Small: routine work on something that already exists |
| 3 | Standard: several tasks, a few tests, nothing new. The anchor is here |
| 5 | Substantial: multiple layers, a business rule with boundary cases |
| 8 | Large or unfamiliar: new technology or security, needs reading first |
| 13 | Too big: not an estimate, split the story before it enters a sprint |

**Anchor story:** US-04 (register, search and edit patients) = 3. Every other story is compared with it.

| ID | Story | Points | Why |
|---|---|---|---|
| US-01 | User accounts, login and roles | 8 | Security and role checks everywhere, high uncertainty |
| US-02 | Manage doctors | 2 | Routine form on a simple entity |
| US-03 | Audit log | 3 | Logging hooks in several places |
| US-04 | Manage patients | 3 | Anchor |
| US-05 | Schedule, change, cancel appointments | 5 | Two relations and validation rules |
| US-06 | Double-booking warning | 2 | One extra check |
| US-07 | Doctor sees own appointments | 2 | One filtered query and a list |
| US-08 | Visit notes and medical history | 3 | Like patients, plus an ownership check |
| US-09 | Mark appointment completed | 2 | Status change with an ownership check |
| US-10 | CI pipeline | 5 | New tool, first workflow |
| US-11 | Container and automatic publishing | 5 | New tool, uncertainty |
| US-12 | CSV export | 3 | Simple output, authorisation filter is easy to get wrong |
| US-13 | End-to-end tests | 3 | Browser test tooling |
| US-14 | Monitoring metrics | 3 | New tool, small scope |

## 8. Sprint backlogs

Sprint length: 2 weeks. First-sprint capacity: about 13 points (a guess). After Sprint 1 it is replaced by the points that were actually finished.

| Sprint | Goal | Stories | Points |
|---|---|---|---|
| 1 | Login works and the pipeline builds and tests | US-01, US-10 | 13 |
| 2 | Patient and doctor records work and the container is published | US-04, US-02, US-11 | 10 |
| 3 | Appointments and clinical notes work | US-05, US-06, US-07, US-08 | 12 |
| 4 | Completion, export, audit log and monitoring | US-09, US-12, US-03, US-13, US-14 | 14 |

## 9. Tools and stack

- **Language and framework:** Python with FastAPI. Simple to write and containerise.
- **Database:** PostgreSQL. Has an official container image and is easy to run in CI.
- **Test runner:** pytest. Runs headless in GitHub Actions.
- **End-to-end tests:** Playwright for Python. Works headless in CI.
- **CI/CD:** GitHub Actions. Lives next to the repository and the board.
- **Container:** Docker.

Refinement is done regularly, once per sprint, around the middle of the sprint.
