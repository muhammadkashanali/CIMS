# Cybersecurity Incident Management System (CIMS) — Project Context

> **Project name:** Cybersecurity Incident Management System (CIMS)  
> **Project timeline:** 20-day version  
> **Purpose:** Main project reference for implementation, team coordination, and AI coding assistants.  
> **Deployment target:** Local laptop demonstration using free tools.

## 1. Project overview

CIMS is a guided cybersecurity incident-response platform for a single organization. Employees report cybersecurity incidents, receive safe first-response guidance with explanations, and can request assistance from an appropriate expert.

The system includes role-based access, incident tracking, response playbooks, evidence management, expert matching, smart incident analysis, notifications, dashboards, and audit logging.

The original proposal describes `organization_id` as a field retained in the schema for possible future multi-tenant support. The first version remains scoped to one organization.

## 2. Roles

Implement the five roles listed in the proposal:

1. **Admin** — manages users and administrative functions.
2. **Manager** — monitors incident handling and reporting.
3. **Analyst** — investigates assigned incidents, records findings, and performs response actions.
4. **Reporter (employee)** — submits incidents, views permitted incident status, follows first-aid guidance, and requests help.
5. **Expert** — maintains expertise/availability, receives help requests, and provides specialist support.

Enforce authorization on the backend for every protected operation. Frontend route guards or hidden buttons are not sufficient security controls.

## 3. Technology stack

Use the stack specified in the project proposal:

| Area | Technology | Purpose |
|---|---|---|
| Backend | Python with Flask | Web routes, API endpoints, and application logic |
| Database | SQLite with SQLAlchemy | Relational persistence and ORM |
| Frontend | HTML, CSS, JavaScript, Bootstrap | User interface and responsive layout |
| Charts | Chart.js | Incident and performance dashboards |
| AI/ML | scikit-learn | Category prediction and similarity analysis |
| Text processing | TF-IDF and cosine similarity | Text classification features and similar/duplicate incident detection |
| Sensitive-data masking | Python regular expressions | Detect and mask selected sensitive text patterns |
| Security | bcrypt, Jinja2 auto-escaping, validation | Password hashing and safer input/output handling |
| Testing | pytest, Postman | Automated tests and manual API verification |
| Collaboration | Git, GitHub, task board | Source control and team coordination |
| Hosting | None for initial version | Demo runs locally on a laptop |

All tools should be free for the intended local demonstration. Do not introduce paid services or an external AI API as a project requirement.

## 4. Scope and features

### 4.1 User and access management

- Login and logout.
- Admin-managed password reset, as specified in the proposal.
- Role-based access control for all five roles.
- User profiles with department information.
- Secure password hashing.
- Session protection and backend authorization.

### 4.2 Incident management

- Submit an incident through a simple form.
- Incident categories: phishing, malware, DDoS, unauthorized access, data breach, and other.
- Severity and priority assignment.
- Assign incidents to analysts.
- Track status: `New`, `In Progress`, `Escalated`, `Resolved`, `Closed`.
- Record status history.
- Record escalation as a status change with reason and target person.
- Track affected assets such as servers, devices, and systems.
- Upload evidence with file type, size, and filename validation.
- Maintain case notes, investigation notes, comments, and response actions in a chronological log.
- Search and filter incidents.

### 4.3 First-response guidance (first aid)

- Predict the incident category from the reporter's description and show a confidence score.
- Retrieve pre-written steps from database playbooks in three stages: `Immediate`, `Containment`, and `Prevention`.
- Show warnings for potentially risky actions. For example, preserve logs and relevant evidence before taking disruptive actions.
- Use keyword rules as a fallback if model prediction fails.
- If confidence is low, show generic safe guidance and recommend direct expert escalation.
- Let users provide feedback on whether the playbook helped.

**Safety rule:** Guidance must be conservative and appropriate to the incident type. Do not let a model invent destructive commands or present unverified instructions as approved organizational procedures. Playbook content should be reviewed by a knowledgeable human.

### 4.4 Expert and help requests

- Expert profiles with skills such as malware analysis, network security, forensics, and cloud security.
- Availability states: `Free`, `Busy`, `On Leave`.
- Create a help request linked to an incident.
- Match experts using a weighted score based on skill fit, availability, workload, and past ratings.
- Notify the selected expert inside the application with relevant incident details.
- Let the expert accept or decline.
- If declined, automatically try the next best eligible expert.
- Track workload as the number of open cases per expert.
- Collect feedback and a rating after case closure.

Show the expert-match score breakdown so that users can understand why an expert was recommended.

### 4.5 Smart features

The proposal specifies these smart features:

1. **Incident category prediction:** keyword rules followed by TF-IDF with Logistic Regression.
2. **Severity prediction:** transparent scoring rules based on category, danger keywords, and number of affected assets.
3. **Similar/duplicate incident detection:** one cosine-similarity engine with two thresholds, including links to relevant past resolutions.
4. **Incident summarization:** extractive summary based on sentence scores; do not require a generative AI service.
5. **Sensitive-data masking:** regex-based detection and masking for patterns such as passwords, card numbers, email addresses, and IP addresses.

For model output, show the predicted category, confidence, and top contributing terms when supported by the trained model. Provide a low-confidence fallback rather than implying certainty.

### 4.6 Monitoring, reports, and security

- In-app notifications and alerts.
- Audit trail and activity logs.
- Dashboard charts for:
  - incidents by type;
  - incidents by severity;
  - incidents by status;
  - resolution time.
- Validate inputs on the server.
- Use parameterized ORM/database operations.
- Use Jinja2 auto-escaping and avoid unsafe rendering of user-submitted text.
- Validate upload extension, content type where feasible, file size, and generated storage name.
- Restrict evidence downloads to authorized users.
- Do not expose stack traces or secrets in production-style error messages.

## 5. Explainability requirements

Include the proposal's user-visible explainability features:

- **Explainable prediction:** predicted category, confidence bar, and top keywords associated with the prediction.
- **Masking preview:** show original text and masked text side by side before saving the masked version.
- **Explained expert match:** show the top three expert candidates and their score breakdown for skill, availability, workload, and rating.
- **Smart severity score:** show how category, danger keywords, and number of affected assets contribute to the score.
- **Model report page:** show evaluation metrics and a confusion matrix/table generated from the project's own held-out test data.

Do not hardcode model accuracy or claim a model is effective before evaluation. The model report must use actual test results.

## 6. Recommended repository folder structure

This is the proposed structure for the project. Build it incrementally; do not create large numbers of empty files in advance.

```text
cims/
├── README.md
├── context.md
├── .gitignore
├── requirements.txt
├── run.py
├── config.py
├── instance/
│   └── .gitkeep
├── docs/
│   ├── requirements.md
│   ├── architecture.md
│   ├── use-cases.md
│   ├── database-design.md
│   ├── api-routes.md
│   ├── incident-playbooks.md
│   ├── model-evaluation.md
│   ├── security-checklist.md
│   └── testing-plan.md
├── app/
│   ├── __init__.py
│   ├── extensions.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── organization.py
│   │   ├── user.py
│   │   ├── role.py
│   │   ├── incident.py
│   │   ├── category.py
│   │   ├── severity.py
│   │   ├── status_history.py
│   │   ├── asset.py
│   │   ├── evidence.py
│   │   ├── case_note.py
│   │   ├── playbook.py
│   │   ├── ai_prediction.py
│   │   ├── expert.py
│   │   ├── skill.py
│   │   ├── help_request.py
│   │   ├── assignment.py
│   │   ├── feedback.py
│   │   ├── notification.py
│   │   └── audit_log.py
│   ├── auth/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   ├── forms.py
│   │   └── decorators.py
│   ├── incidents/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   ├── forms.py
│   │   └── services.py
│   ├── playbooks/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   └── services.py
│   ├── experts/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   └── matching.py
│   ├── smart_features/
│   │   ├── __init__.py
│   │   ├── classification.py
│   │   ├── severity.py
│   │   ├── similarity.py
│   │   ├── summarization.py
│   │   └── masking.py
│   ├── dashboard/
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── notifications/
│   │   ├── __init__.py
│   │   └── services.py
│   ├── admin/
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── audit/
│   │   ├── __init__.py
│   │   └── services.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── errors/
│   │   │   ├── 403.html
│   │   │   ├── 404.html
│   │   │   └── 500.html
│   │   ├── auth/
│   │   │   ├── login.html
│   │   │   └── profile.html
│   │   ├── dashboard/
│   │   │   └── index.html
│   │   ├── incidents/
│   │   │   ├── list.html
│   │   │   ├── create.html
│   │   │   ├── detail.html
│   │   │   └── edit.html
│   │   ├── playbooks/
│   │   │   └── detail.html
│   │   ├── experts/
│   │   │   ├── list.html
│   │   │   ├── profile.html
│   │   │   └── help_requests.html
│   │   ├── admin/
│   │   │   ├── users.html
│   │   │   └── user_form.html
│   │   └── reports/
│   │       └── model_report.html
│   ├── static/
│   │   ├── css/
│   │   │   └── styles.css
│   │   ├── js/
│   │   │   ├── main.js
│   │   │   ├── incident-form.js
│   │   │   └── dashboard.js
│   │   └── uploads/                  # Runtime files; gitignored
│   └── utils/
│       ├── __init__.py
│       ├── validators.py
│       ├── permissions.py
│       └── text_processing.py
├── ml/
│   ├── README.md
│   ├── data/
│   │   ├── raw/                      # Only safe, permitted datasets
│   │   └── processed/
│   ├── notebooks/
│   │   └── incident_classification.ipynb
│   ├── artifacts/                    # Generated model/vectorizer; gitignored
│   ├── train.py
│   ├── evaluate.py
│   └── predict.py
├── scripts/
│   ├── init_db.py
│   ├── seed_demo_data.py
│   └── create_admin.py
├── tests/
│   ├── conftest.py
│   ├── test_auth.py
│   ├── test_permissions.py
│   ├── test_incidents.py
│   ├── test_playbooks.py
│   ├── test_expert_matching.py
│   ├── test_smart_features.py
│   ├── test_upload_validation.py
│   └── test_dashboard.py
└── instance/
    └── cims.sqlite3                   # Runtime database; gitignored
```

### Folder-structure notes

- Flask blueprints separate application areas and keep routes organized.
- SQLAlchemy models represent relational entities; shared database setup belongs in `app/extensions.py`.
- Keep ML training scripts separate from runtime prediction code.
- Save generated models and vectorizers under `ml/artifacts/`, but do not commit them unless the team deliberately chooses to version them.
- Uploaded evidence and the SQLite database are runtime data and should be excluded from Git.
- Do not store user passwords, secrets, real incident evidence, or sensitive logs in source control.
- For a 20-day build, it is acceptable to combine very small modules where that reduces unnecessary complexity. Preserve clear separation of responsibilities.

## 7. Database design — 22 proposed tables

The proposal lists these 22 tables. Use them as the initial schema inventory and document any changes in `docs/database-design.md`.

1. `Organization`
2. `User`
3. `Role`
4. `Incident`
5. `Category`
6. `Severity`
7. `Status_History` — includes escalation history
8. `Asset`
9. `Incident_Asset` — many-to-many relationship between incidents and affected assets
10. `Evidence`
11. `Case_Note` — comments, investigation notes, and response actions
12. `Playbook`
13. `Playbook_Step`
14. `AI_Prediction`
15. `Expert`
16. `Skill`
17. `Expert_Skill` — expert-to-skill relationship
18. `Help_Request`
19. `Assignment`
20. `Feedback` — playbook and expert feedback
21. `Notification`
22. `Audit_Log`

### Database requirements

- Use primary keys and foreign keys for entity relationships.
- Add unique, not-null, and check constraints where appropriate.
- Normalize repeated reference data such as categories, statuses, roles, and skills.
- Use SQLAlchemy relationships intentionally and avoid N+1 query problems in dashboard/report queries.
- Keep status changes in history rather than overwriting the only record of prior states.
- Use transactions when creating incidents and related records or updating assignment/status.
- Include timestamps for creation and updates where useful.
- Keep `organization_id` available for future multi-tenant support, but enforce the single-organization scope for this version.
- Store evidence metadata in the database; store file bytes on disk with randomized names and strict access controls for the local demo.
- Store model version, predicted category, confidence, and explanation metadata in `AI_Prediction`.
- Seed demo users, categories, statuses, skills, and reviewed sample playbooks through a repeatable script.

## 8. Suggested route map

Use Flask blueprints. The following is an implementation guide; the final route names should be documented consistently and tested.

| Blueprint | Example routes | Purpose |
|---|---|---|
| `auth` | `GET/POST /login`, `POST /logout`, `GET /profile` | Authentication and profile |
| `admin` | `GET /admin/users`, `GET/POST /admin/users/new`, `POST /admin/users/<id>/reset-password` | User administration |
| `incidents` | `GET /incidents`, `GET/POST /incidents/new`, `GET /incidents/<id>`, `POST /incidents/<id>/status`, `POST /incidents/<id>/assign` | Reporting and incident lifecycle |
| `incidents` | `POST /incidents/<id>/evidence`, `POST /incidents/<id>/notes` | Evidence and case notes |
| `playbooks` | `GET /playbooks/<category>` | First-response steps |
| `experts` | `GET /experts`, `GET /experts/<id>`, `POST /incidents/<id>/help-requests` | Expert profiles and help requests |
| `experts` | `POST /help-requests/<id>/accept`, `POST /help-requests/<id>/decline` | Expert response and reassignment |
| `dashboard` | `GET /dashboard`, `GET /reports/model` | Charts and model evaluation |
| `notifications` | `GET /notifications`, `POST /notifications/<id>/read` | In-app notifications |

Use POST for state-changing operations. Validate all IDs against the current user's permissions, not merely whether the record exists.

## 9. Smart-feature design

### 9.1 Category prediction

Initial approach:
1. Prepare labeled incident descriptions with categories.
2. Split data into training and held-out test sets.
3. Build a scikit-learn pipeline using TF-IDF and Logistic Regression.
4. Compare it with a keyword-rule baseline.
5. Evaluate using precision, recall, F1-score, per-class metrics, and a confusion matrix.
6. Save the fitted pipeline and model metadata under `ml/artifacts/`.
7. Load the saved pipeline in the application; do not retrain on every request.
8. Use keyword fallback and a low-confidence response when needed.

Use a permitted dataset or clearly labeled synthetic demonstration examples. Do not claim results from a dataset that was not actually tested.

### 9.2 Severity scoring

Use transparent rules based on:
- incident category;
- danger keywords or indicators;
- number and importance of affected assets.

Display the score and the contributing factors. Keep category prediction and severity scoring as distinct functions.

### 9.3 Similar and duplicate incidents

- Convert incident descriptions to TF-IDF vectors.
- Compute cosine similarity against eligible prior incidents.
- Use two documented thresholds, as specified in the proposal, to distinguish likely duplicates from related incidents.
- Show the most relevant past incidents and their recorded resolutions only to authorized users.
- Exclude closed-case content from unauthorized access and handle sensitive data carefully.

The exact threshold values should be selected through testing, not guessed and presented as validated.

### 9.4 Extractive summarization

- Split the description/notes into sentences.
- Score sentences using a simple documented method, such as term frequency or TF-IDF weights.
- Select the highest-scoring sentences while preserving readability.
- Label the output as an automatically generated extractive summary.
- Do not add facts that were not present in the source text.

### 9.5 Sensitive-data masking

- Use carefully tested regular expressions for supported patterns.
- Provide a before-save preview with original and masked text side by side.
- Mask patterns such as passwords when they appear in recognized formats, card-number-like strings, email addresses, and IP addresses.
- Explain that regex masking is imperfect and may miss or over-mask values.
- Avoid retaining the original sensitive text unnecessarily; define what is stored and who can access it.

### 9.6 Expert matching

Build a weighted score from:
- skill match;
- availability;
- current workload;
- past user ratings.

Normalize component scores to a documented scale, define weights in configuration, exclude unavailable experts when appropriate, and show the top three candidates with a breakdown. If an expert declines, try the next eligible candidate and record the assignment event.

## 10. Initial playbook categories

At minimum, include playbooks for the categories in the proposal:

- Phishing
- Malware
- DDoS
- Unauthorized access
- Data breach
- Other

Each playbook should have:
- category and title;
- approved first-response steps grouped into Immediate, Containment, and Prevention;
- warnings and steps to avoid;
- source/owner or reviewer;
- version and last-reviewed date.

Seed the database with concise, conservative demonstration playbooks. Have a knowledgeable person review them before presenting them as organizational policy.

## 11. Security and privacy checklist

- Hash passwords with bcrypt; never store plaintext passwords.
- Use CSRF protection for session-authenticated forms that change state.
- Use secure session settings appropriate to the local environment.
- Apply role-based and object-level authorization to incidents, evidence, notes, assignments, and reports.
- Validate uploaded file extension, size, actual content where feasible, and generated filename.
- Store uploads outside public/static paths and serve them only through an authorization-checked route.
- Never execute uploaded files or trust their filenames.
- Use SQLAlchemy/parameterized queries; do not concatenate user input into SQL.
- Rely on Jinja2 auto-escaping and avoid marking untrusted content as safe HTML.
- Limit login attempts and return generic authentication errors where practical.
- Write audit records for login/security events, status changes, assignments, evidence access, and administrative changes.
- Do not log passwords, session secrets, or full sensitive evidence.
- Keep `.env`, SQLite runtime files, uploads, caches, and model artifacts out of Git.
- Use synthetic or approved sample incident data for the demo.
- Make it clear that smart predictions are advisory and require human judgment.

## 12. Configuration and setup

Use `requirements.txt` for this project to keep setup straightforward. Pin compatible versions after the team tests the environment.

Suggested configuration values (names can be adjusted in code):

```text
FLASK_ENV=development
SECRET_KEY=replace-with-a-long-random-secret
DATABASE_URL=sqlite:///cims.sqlite3
UPLOAD_FOLDER=instance/uploads
MAX_CONTENT_LENGTH=10485760
MODEL_PATH=ml/artifacts/incident_classifier.joblib
```

Store actual secrets in a local `.env` file and load them through the chosen configuration method. Commit only a `.env.example` containing placeholders. Do not rely on the development server for production deployment; this project is scoped to a local demonstration.

### Setup outline

1. Create and activate a Python virtual environment.
2. Install dependencies from `requirements.txt`.
3. Create local configuration from `.env.example`.
4. Initialize the SQLite database.
5. Seed demo roles, users, categories, playbooks, skills, and safe sample incidents.
6. Train/evaluate the model or load the documented demo artifact.
7. Start the Flask application with `python run.py`.
8. Run tests with `pytest`.
9. Verify the main workflows manually using the local browser and, where relevant, Postman.

Exact commands should be added to `README.md` once the application entry point and environment setup are implemented.

## 13. Testing plan

Test at least the following:

- Authentication success/failure and logout.
- Role restrictions for all five roles.
- User cannot view or edit another user's restricted incident.
- Incident creation, category, severity, assignment, and valid status transitions.
- Status history and escalation reason/target are persisted.
- Evidence upload rejects oversized and disallowed files.
- Playbook fallback works when prediction fails or confidence is low.
- Model output includes a category and confidence without fabricated metrics.
- Similar-incident thresholds return expected results on known examples.
- Masking preview covers supported patterns and documents limitations.
- Expert scoring uses skill, availability, workload, and rating as intended.
- Expert decline triggers reassignment and records history.
- Notifications and audit records are created for relevant events.
- Dashboard counts and resolution-time charts agree with test data.
- Database initialization and seeding are repeatable.
- Application handles missing model artifacts and database errors safely.

Use Flask's test client and pytest fixtures. Keep tests independent of any real organization or sensitive incident data.

## 14. Twenty-day implementation plan

The following is a practical proposed sequence based on the 20-day scope. Adjust task ownership to team size.

| Days | Focus | Deliverable |
|---|---|---|
| 1–2 | Requirements, repo setup, ERD, UI sketch, Flask skeleton | Running app and agreed schema |
| 3–4 | SQLAlchemy models, initialization, seed data, authentication | Login, roles, database ready |
| 5–7 | Incident reporting, listing/detail, status history, assignments, notes | Core incident lifecycle |
| 8–9 | Playbooks and safe first-response workflow | Category-based guidance and fallback |
| 10–11 | TF-IDF + Logistic Regression, evaluation, explanation output | Trained/evaluated classifier |
| 12 | Severity score, similarity detection, extractive summary, masking | Smart-feature module |
| 13–14 | Expert profiles, weighted matching, help requests, reassignment | End-to-end human assistance |
| 15 | Evidence upload validation, audit trail, notifications | Security and tracking improvements |
| 16–17 | Dashboard charts, search/filter, model report page | Reporting interface |
| 18 | Integration and permission testing | Major bugs fixed |
| 19 | UI polish, demo data, documentation | Demo-ready system |
| 20 | Full regression, presentation, contingency | Final local demonstration |

Prioritize the end-to-end incident workflow over decorative UI or advanced model work. If time becomes limited, keep core reporting, access control, playbook guidance, expert help, and the required smart-feature demonstrations working before optional refinements.

## 15. Definition of done

The project is ready for its intended demo when:
- All five roles have documented permissions and the backend enforces them.
- An employee can submit an incident and view permitted updates.
- Analysts can update status, record notes, and assign/escalate incidents.
- Evidence uploads are validated and access-controlled.
- Playbook guidance displays Immediate, Containment, and Prevention steps.
- Category prediction has a reproducible held-out evaluation and understandable output.
- Severity scoring, similarity detection, extractive summarization, and masking are demonstrated.
- Users can request help and the expert matching/reassignment flow works.
- Dashboard charts and audit logs use persisted data.
- Automated tests cover key workflows and authorization boundaries.
- The application runs locally from documented setup instructions.
- No secrets or private incident data are committed to Git.

## 16. Recommended first task

Begin with a small, working foundation rather than implementing every module at once:

1. Create the repository with `README.md`, `context.md`, `.gitignore`, and `requirements.txt`.
2. Implement the Flask application factory and `GET /health` or a simple home page.
3. Configure SQLAlchemy and initialize SQLite.
4. Create the initial `User`, `Role`, and `Incident` models.
5. Seed one demo user for each role and a few clearly synthetic incidents.
6. Add authentication and backend role checks.
7. Build incident creation and incident listing.
8. Add tests before proceeding to ML and expert matching.

Keep the application runnable after every stage. Use this document as the shared plan, but update it when the team makes an explicit scope or architecture decision.
