# BentoBox --- AI Project Context

Use this document as background context when asking an AI assistant to
help with the BentoBox university lab project. Treat the official course
brief, lab sheets, assessment instructions, and instructor guidance as
authoritative if they conflict with this summary.

## 1. Project identity

-   **Project name:** BentoBox
-   **Course context:** Advanced Web Engineering, Winter Term 2026/27
-   **Team size:** 4 students
-   **Current team skill level:** Beginners with broadly similar
    experience
-   **Backend choice:** Java + Spring Boot
-   **Repository:** GitLab
-   **Current stage:** The team has created the GitLab repository;
    implementation and project setup are still in progress.

The name is **BentoBox**. Earlier working material may use the name
"ShipOps"; treat that as the previous project name, not the current
name.

## 2. Product concept

BentoBox is a deployment and monitoring workspace for software teams.
Its purpose is to bring application information, CI/CD pipeline history,
deployments, environments, and operational health/incidents into one
place.

The product should help a user answer questions such as: - Which
applications are registered? - What is the status of a pipeline run? -
Which version was deployed, and to which environment? - Is an
environment healthy? - Are there active incidents that need attention?

Keep the implementation appropriate for a university lab project. Do not
expand the scope into a production-grade DevOps platform unless the team
or instructor explicitly requests it.

## 3. Main functional areas

### Applications

Register and view applications. Typical information includes the
application name, repository URL, default branch, status, and creation
time.

### Pipelines

Show pipeline runs, their statuses, branches, commit identifiers,
start/end times, and execution history. The initial scope can use
stored/demo pipeline-run data. **Live GitLab CI/CD integration is
optional**, not a prerequisite unless the team decides otherwise.

### Deployments and environments

Track deployments, versions, deployment status, deployment time, who
triggered the deployment, and the target environment.

### Monitoring and incidents

Represent environment health checks, response times, health details, and
incidents with severity/status and creation/resolution times. Use
simple, clearly explained demo data if live monitoring is outside the
agreed scope.

## 4. Suggested domain model

Use the official course domain model as the source of truth if it is
available. The current working model contains these entities:

-   **Application:** `id` (UUID), `name`, `repositoryUrl`,
    `defaultBranch`, `status`, `createdAt`
-   **PipelineRun:** `id` (UUID), `status`, `branch`, `commitSha`,
    `startedAt`, `finishedAt`
-   **Deployment:** `id` (UUID), `version`, `status`, `deployedAt`,
    `triggeredBy`
-   **Environment:** `id` (UUID), `name`, `type`, `baseUrl`, `isActive`
-   **HealthSnapshot:** `id` (UUID), `checkedAt`, `result`,
    `responseTimeMs`, `details`
-   **Incident:** `id` (UUID), `title`, `severity`, `status`,
    `createdAt`, optional `resolvedAt`

Expected relationships: - One Application has many PipelineRuns. - One
Application has many Deployments. - Each Deployment belongs to one
Application and targets one Environment. - One Environment has many
HealthSnapshots. - An Incident may be associated with an Environment and
optionally a Deployment.

Do not assume exact enum values, validation rules, or cardinalities
beyond what the official UML/lab instructions specify. Check those
materials before implementing.

## 5. Expected architecture and technologies

The course project materials describe a Java/Spring web application and
cover multiple presentation/API approaches over the labs. The working
technology context includes:

-   Java and Spring Boot
-   Spring Web
-   Spring Data JPA
-   A relational persistence layer; H2 is listed in the Lab 3 setup
    material
-   Thymeleaf server-rendered views
-   REST endpoints returning JSON and XML
-   GraphQL
-   Native client-side rendering (CSR) using browser Fetch and DOM APIs
-   Vue 3, Vite, and Vue Router for a single-page application
-   BEM and Sass for styling

The team has selected **Spring Boot + Java** for the backend. Follow the
current lab sheet for required versions and dependencies; versions in
old instructions can change. Avoid introducing extra frameworks or
libraries without a clear need.

## 6. Lab and assessment context

The project is developed through a sequence of labs. The known
assessment breakdown is:

  Component                                 Points
  --------------------------------------- --------
  Lab 1: setup, profile, UML, prototype          6
  Lab 2: BEM and Sass                            3
  Lab 3: persistence and business logic          4
  Lab 4: Thymeleaf views                         3
  Lab 5: REST                                    4
  Lab 6: GraphQL                                 4
  Lab 7: CSR using both APIs                     4
  Lab 8: Vue SPA                                 7
  Project pitch                                  5
  **Total**                                 **40**

Verify this breakdown against the latest official assessment document
before relying on it for submission planning.

## 7. Team organization

There are four feature areas, with one person taking primary ownership
of each:

1.  Applications
2.  Pipelines
3.  Deployments and Environments
4.  Monitoring and Incidents

The user is primarily interested in **Pipelines**.

Ownership means coordinating the feature across backend, persistence,
APIs, UI, and tests---not working in isolation or learning only one
layer. All team members should contribute during the labs and be able to
explain the complete application.

Keep interfaces and shared model changes coordinated. Agree on entity
names, enum values, endpoint conventions, branch names, and integration
points before parallel implementation.

## 8. GitLab workflow

The team uses GitLab, not GitHub, for this project.

When giving Git instructions: - Distinguish Git (the tool) from GitLab
(the hosting platform). - Prefer small, focused commits. - Use a feature
branch and merge request rather than committing directly to the default
branch, unless the team has agreed otherwise. - Explain each command and
identify whether it is run in a terminal or performed in the GitLab web
interface. - Do not ask the user to paste a personal access token into
chat, source code, a commit, or a repository file. - Recommend only the
minimum token scopes required for the actual task; do not default to
"all permissions." - Do not assume the repository is already cloned or
that a Spring Boot project has already been generated. Ask or provide
checks when that affects the steps.

## 9. Guidance for the AI assistant

When helping with BentoBox:

1.  **Use English** unless the user asks for another language.
2.  Assume the user is a beginner. Explain unfamiliar terms plainly and
    give executable steps in order.
3.  For troubleshooting, ask for the exact error/output when it is
    needed; do not invent what the terminal or IDE shows.
4.  When giving code, state the intended file path and provide complete,
    compatible snippets where practical.
5.  Follow the team's current Java/Spring Boot setup and the relevant
    lab sheet. Avoid silently changing the architecture.
6.  Prefer the simplest solution that satisfies the lab requirements and
    is easy for all four students to explain.
7.  Explain what each important class, annotation, endpoint, repository,
    service, and relationship does.
8.  Keep feature ownership in mind, but explain how the feature
    integrates with the rest of the application.
9.  Do not claim that a GitLab integration, test, build, or deployment
    works unless there is evidence from actual output.
10. Distinguish required course features from optional enhancements.
    Live GitLab CI/CD integration and cloud hosting were described as
    optional in the working project brief.
11. If the user's request is ambiguous, ask one focused question rather
    than presenting a large questionnaire.
12. Do not fabricate instructor requirements, deadlines, repository
    settings, or assessment rules. Ask for or inspect the relevant
    official material when necessary.

## 10. Pipeline feature --- user's focus

The initial Pipeline feature should prioritize: - Listing pipeline
runs - Showing each run's status - Showing branch and commit
information - Showing start and finish times where available -
Displaying execution history - Using a clear empty state when there are
no runs - Using seeded/demo data if live integration is not yet
implemented

Possible later enhancement: fetch real pipeline data from GitLab CI/CD.
Treat this as optional and separate it from the basic pipeline-history
feature. If implemented, discuss token permissions, secret storage,
error handling, and API limitations. Never hardcode an access token.

## 11. Definition of done

Before calling a task complete, check that: - It meets the relevant lab
requirements. - The project builds and the relevant tests pass, where
applicable. - The feature is integrated with the agreed domain model and
architecture. - The UI handles loading, empty, success, and error states
where relevant. - The code is understandable to beginner team members. -
The team can explain the implementation during assessment. - Any
optional or unfinished work is described honestly.

## 12. Important caveat

This is a practical working summary, not a replacement for the official
course documents. If the exact lab sheet, assessment PDF, domain-model
diagram, or instructor instruction is available, use it to verify
versions, required deliverables, naming, relationships, and deadlines
before making consequential changes.
