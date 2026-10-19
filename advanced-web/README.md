# BentoBox — Deployment and Monitoring Workspace

## 1. Project Overview

**BentoBox** is a web application that helps software teams view the delivery status of their applications in one place.

During software development, useful information is often spread across repositories, pipeline results, deployment records, and monitoring tools. This can make it difficult to answer simple questions:

- Which applications does our team manage?
- Did an application's pipeline succeed or fail?
- Which version was deployed, and to which environment?
- Is the service healthy?
- Are there any open incidents?

BentoBox brings this information together in one workspace. The project will be built step by step as part of the **Lab for Advanced Web Engineering, Winter Semester 2026/2027**.

The first goal is a reliable, demonstrable application using sample data. Connecting to real GitLab CI/CD pipelines or hosting the application in the cloud can be considered later as optional extensions.

## 2. Project Goals

BentoBox aims to:

1. Keep application information in one place.
2. Display pipeline runs, their statuses, and execution history.
3. Record deployments and the environments they target.
4. Show service-health checks and operational incidents.
5. Connect related records so a user can investigate a failed delivery.
6. demonstrate the web technologies required by the course, including Spring Boot, Thymeleaf, REST, GraphQL, native client-side rendering, and Vue 3.

## 3. Target Users

- **Developers:** register applications, inspect pipeline runs, and review deployment history.
- **DevOps / platform teams:** review environments, releases, and service-health information.
- **Team leads and QA:** follow delivery progress and identify failures or open incidents.

The initial project does not require a complete user-account or role-permission system unless the team and instructor later decide it is necessary.

## 4. Main Features

### 4.1 Application Management

Users can:
- View the applications registered in BentoBox.
- Register an application with its name and repository URL.
- View application details and repository information.
- Edit application metadata where implemented.
- Open the pipeline and deployment history associated with an application.

### 4.2 CI/CD Pipeline Management

Users can:
- View the pipeline runs belonging to an application.
- See each run's status and execution history.
- Inspect details such as branch, commit identifier, start time, and finish time.
- View pipeline stages and test outcomes if included in the agreed scope.

For the initial version, pipeline records may use sample data stored by BentoBox. A live connection to GitLab CI/CD, triggering real pipelines, and retrieving actual logs are optional extensions rather than prerequisites for the basic history view.

### 4.3 Deployments and Environments

Users can:
- View available environments, such as development, testing, and production.
- Record a deployment for an application.
- Store the deployed version, deployment status, and timestamp.
- See which environment a deployment targets.
- Review an application's deployment history.

The initial version records deployment information; it does not have to deploy software to real servers.

### 4.4 Monitoring and Incidents

Users can:
- View health snapshots recorded for an environment.
- Inspect a health result, check time, response time, and details when available.
- View incidents affecting an environment or deployment.
- Record an incident's title, severity, status, and creation time.
- Update an incident's status and record when it is resolved.

The initial version can use sample health-check and incident records rather than a live monitoring service.

## 5. Example User Journey

A typical workflow in BentoBox is:

1. A developer registers an application and its repository URL.
2. The developer opens the application and inspects its pipeline history.
3. The developer sees that a pipeline failed and opens the run details.
4. The developer checks the application's deployment history and target environment.
5. The developer checks the latest health snapshot and any open incident.
6. The team uses these related records to understand the delivery problem.

This workflow illustrates the main value of BentoBox: connecting application, pipeline, deployment, and operational information.

## 6. Domain Model

The application uses six core domain entities. The final UML model, including attributes, relationships, and multiplicities, should be maintained in the GitLab Wiki and agreed on by the whole team.

| Entity | Purpose | Example information |
|---|---|---|
| `Application` | A software project managed by BentoBox | ID, name, repository URL, default branch, status, creation time |
| `PipelineRun` | One execution of an application's pipeline | ID, status, branch, commit SHA, start time, finish time |
| `Deployment` | A recorded deployment of an application version | ID, version, status, deployment time, triggered by |
| `Environment` | A place where an application can run | ID, name, type, base URL, active status |
| `HealthSnapshot` | A recorded health check for an environment | ID, check time, result, response time, details |
| `Incident` | An operational problem affecting a service | ID, title, severity, status, creation time, resolution time |

### Main relationships

- One `Application` can have many `PipelineRun` records.
- One `Application` can have many `Deployment` records.
- Each `Deployment` belongs to one `Application` and targets one `Environment`.
- One `Environment` can have many `HealthSnapshot` records.
- An `Incident` can be linked to an affected environment and, optionally, a deployment.

The team should agree on status and type values (enums) and confirm the exact relationships before implementation. Do not add unrelated entities unless they are needed and agreed upon.

## 7. Technology and Architecture

The project uses one shared domain model and backend, with several interfaces built during the course.

### Backend

- **Java + Spring Boot:** application framework.
- **Spring Web:** web controllers and REST endpoints.
- **Spring Data JPA:** database access and entity persistence.
- **H2:** simple database for development and demonstrations, unless the team agrees on another option.
- **Thymeleaf:** server-rendered HTML views.
- **Service layer:** shared business rules and validation.
- **Repositories:** database access.
- **DTOs and mapping:** recommended to keep API/view data separate from persistence entities.

Use the dependency and version requirements stated in the current lab sheet.

### Frontend

- **HTML:** semantic page structure, forms, and tables.
- **CSS and Sass:** layout and styling.
- **BEM:** consistent CSS class naming.
- **Thymeleaf:** server-rendered views.
- **Native JavaScript and browser APIs:** client-side rendering (CSR) views for the relevant lab.
- **Vue 3, Vite, and Vue Router:** the final single-page application (SPA).
- **Fetch/AJAX:** communication between browser views and the backend.

### APIs

- **REST API:** endpoints used by the frontend; JSON support is required, and the Spring Boot implementation must also support XML.
- **GraphQL API:** queries and mutations for the application's required frontend functionality.
- **GraphiQL:** a convenient interface for testing GraphQL, where available.

The REST API remains available after GraphQL is added because each native CSR view must demonstrate requests to both APIs.

### High-level architecture

```text
Thymeleaf Views ─────────┐
Native CSR Views ────────┼── REST API / GraphQL API
Vue 3 SPA ───────────────┘             |
                                       v
                              Spring Boot Services
                                       |
                                       v
                                Repositories
                                       |
                                       v
                                  Database
```

All interfaces should use the same business logic and underlying domain model. Avoid duplicating business rules in individual pages.

## 8. Team Responsibilities

The team has four members. Feature ownership is divided vertically, so each member leads their feature from the data model and business logic through the views, APIs, and tests.

| Feature owner | Main responsibility |
|---|---|
| Member 1 — Applications | Application registration, metadata, repository links, and application views |
| Member 2 — Pipelines | Pipeline runs, statuses, execution history, and pipeline views |
| Member 3 — Deployments | Environments, versions, deployment records, and deployment history |
| Member 4 — Monitoring | Health snapshots, incidents, status changes, and monitoring views |

Ownership does not mean that a member works alone or only works on their own feature. Everyone contributes during each technology phase, reviews other members' work, and prepares to explain the complete system.

## 9. Lab-by-Lab Development Plan

The project will be developed incrementally. Each lab adds a required artifact or technology to the same overall application.

### Lab 1 — Project Setup, Scenario, and Click Prototype

**Goal:** agree on what BentoBox does and create an initial interface.

Deliverables:
- Register the team and GitLab repository as required by the course.
- Create a project profile in the GitLab Wiki with the title, benefit, target users, team members, responsibilities, and planned features.
- Create a UML domain model in the Wiki.
- Create a clickable HTML/CSS prototype of the main screens.
- Use semantic HTML, including suitable forms and tables.
- Keep CSS in external stylesheets.
- Validate the HTML and CSS using W3C validators or an appropriate IDE tool.
- Create GitLab issues/work items and record decisions and contributions as required.

### Lab 2 — BEM and Sass

**Goal:** make the prototype styling consistent and maintainable.

Deliverables:
- Apply BEM naming to the prototype's CSS classes.
- Use Sass variables for repeated design values.
- Organize styles with Sass modules such as `@use` and `@forward`.
- Use mixins where they help avoid repeated styling.
- Keep styles understandable and consistent across views.
- Retain the required branch/artifacts according to the lab instructions.

### Lab 3 — Persistence and Business Logic

**Goal:** create a functional Spring Boot backend.

Deliverables:
- Generate the Spring Boot project using the required course settings.
- Implement entities based on the agreed UML.
- Implement repositories for accessing stored records.
- Create a separate `services` package for business logic.
- Add realistic sample data for demonstration.
- Test service methods and the main data-access functions.
- Keep the backend modular and avoid unrelated features.

### Lab 4 — Server-Rendered Views

**Goal:** render pages on the server using Spring MVC and Thymeleaf.

Deliverables:
- Create a controller for each team member's view.
- Create the corresponding Thymeleaf templates.
- Pass data from controllers to templates.
- Reuse the shared layout and existing styles.
- Ensure the pages render actual application data or agreed sample data.

Each member must have at least one server-rendered view.

### Lab 5 — REST API

**Goal:** make the required application features accessible through REST.

Deliverables:
- Implement REST controllers for the required frontend functionality.
- Return appropriate responses for listing and viewing records and for supported create/update operations.
- Support JSON.
- Support XML in the Spring Boot implementation.
- Test requests and responses with a tool such as Postman.
- Handle invalid requests and missing records sensibly.

### Lab 6 — GraphQL API

**Goal:** provide a working GraphQL interface for the required application functionality.

Deliverables:
- Define a GraphQL schema.
- Implement the required queries and mutations.
- Reuse the existing service layer instead of duplicating business logic.
- Test queries and mutations using GraphiQL or another suitable GraphQL client.
- Keep REST working alongside GraphQL.

### Lab 7 — Native Client-Side Rendering (CSR)

**Goal:** demonstrate that browser JavaScript can retrieve and render data without a frontend framework.

Deliverables:
- Create one native CSR view for each team member.
- Use browser APIs such as `fetch`, the DOM API, and event listeners.
- Retrieve data through AJAX.
- Ensure each member's view calls both the REST API and the GraphQL API.
- Display the returned data in the page.
- Do not use Vue or another frontend framework for these CSR views.

### Lab 8 — Vue 3 Single-Page Application

**Goal:** create the final modern frontend using Vue.

Deliverables:
- Set up the frontend using Vue 3, Vite, and NPM.
- Create reusable Vue single-file components.
- Organize components according to the agreed UI concept.
- Use AJAX to communicate with the backend.
- Configure Vue Router and make navigation functional.
- Verify that the main application workflows work from the Vue interface.

### Integration, Testing, and Final Submission

After the main lab implementations:
- Integrate the features and resolve conflicts.
- Test the full workflow from application registration to pipeline inspection, deployment, and monitoring.
- Check the APIs and all required views.
- Merge all assessed artifacts into the required submission branch.
- Keep any separately required BEM branch.
- Update the README with setup, build, run, and branch instructions.
- Test from a clean checkout so the project can be reproduced.
- Prepare the live demo and ensure all team members can explain the architecture and their contributions.

## 10. Suggested Main Screens

The prototype and application can start with these essential screens:

1. **Overview:** summary of applications, recent pipeline results, deployments, and open incidents.
2. **Applications:** list and register applications.
3. **Application Details:** repository information and related pipeline/deployment history.
4. **Pipeline History:** runs, statuses, branch/commit information, and timing.
5. **Pipeline Run Details:** details of one run, including stages or test results if implemented.
6. **Deployments and Environments:** deployment records and their target environments.
7. **Monitoring and Incidents:** health snapshots, incident details, and incident status.

The exact screen count can be adjusted, but the prototype should cover the important user journeys and the views required by the labs.

## 11. Scope and Priorities

### Required core

- Application, pipeline, deployment, environment, health snapshot, and incident records.
- A functional persistence and business logic layer.
- The required Thymeleaf, REST, GraphQL, native CSR, and Vue implementations.
- The project documentation, styling artifacts, tests, and submission evidence.

### Optional extensions

- Live GitLab CI/CD integration.
- Triggering real pipelines.
- Reading actual pipeline logs and test reports.
- Deploying BentoBox or managed applications to cloud infrastructure.
- Live monitoring integrations.

Complete the core requirements before investing time in optional extensions. The initial application can demonstrate the delivery workflow using sample records.

## 12. Git and Collaboration Rules

- Use the university's GitLab repository as the official project repository.
- Create an issue/ticket for each meaningful task.
- Include an owner, acceptance criteria, and an estimate where required.
- Use feature branches and merge requests.
- Review changes before merging.
- Commit small, understandable changes.
- Record decisions, tests, and time tracking as required by the course.
- Keep the project runnable as features are integrated.
- Never commit passwords, access tokens, or other secrets.
- Make sure every member has visible contributions throughout the project.

## 13. Definition of Done

A feature is ready to merge when:
- Its agreed behavior is implemented.
- It uses the shared domain model and business logic.
- Its page or API works with the expected data.
- Relevant tests or manual checks have been performed.
- Errors and empty states are handled reasonably.
- The code has been reviewed.
- The issue and documentation are updated where needed.
- The change does not break the existing application.

## 14. Final Demonstration

The planned pitch is approximately 10 minutes:

1. **Project idea:** explain the problem, target users, and value of BentoBox.
2. **Architecture:** show Vue, Thymeleaf, REST, GraphQL, Spring Boot, and persistence.
3. **Main workflow:** register an application, inspect a pipeline, review a deployment, and open health/incident information.
4. **Technical evidence:** demonstrate REST JSON/XML, GraphiQL, one native CSR request, and the BEM/Sass work.
5. **Wrap-up:** explain what is implemented, which extensions are optional, and how the team divided responsibilities.

Every member should be able to explain the complete application and demonstrate their own contribution.

## 15. Schedule and Course References

The current project materials list these dates:
- **Submission:** 11 January 2027 at 20:00 CET.
- **Project pitch:** 13 January 2027.
- **Assessment interviews:** 20 and 27 January 2027.

These dates should be checked against official course announcements in case they change.

---

**BentoBox — One delivery lifecycle, one operational view.**

Build a small, coherent application that connects application records, pipeline history, deployments, and operational health. Implement the required course technologies step by step, integrate frequently, and make sure every team member can explain and demonstrate the result.
