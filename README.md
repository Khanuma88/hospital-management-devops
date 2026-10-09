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
- A new requirement or a change in the scenario appears.
- A bug or technical problem blocks a planned story.
- Acceptance criteria are unclear when planning starts.

Refinement is done regularly, once per sprint, around the middle of the sprint.
