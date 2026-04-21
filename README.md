# Task Manager CI/CD (GitHub Actions)

This project is an educational example of a task manager implemented in Java 21 using Maven.

## Features
- Modern structure following professional guidelines (records, sealed interfaces, Optional, Streams, guard clauses, custom exceptions)
- Unit tests with JUnit 6
- Integration tests with Mockito (top-down approach, file names ending with `IT.java`)
- Ready for CI/CD with GitHub Actions

## Production code — `src/main/java`

The production code is organized under `org.iips.actions` and split across four packages:

### `model`
- **`Task`** — immutable value object defined as a Java 21 `record`. Fields: `id` (UUID), `description` (String), `completed` (boolean), `dueDate` (LocalDate, optional). Validates that `id` is not null and `description` is not blank in the compact constructor.

### `repository`
- **`TaskRepository`** — `sealed interface` that declares the persistence contract: `save`, `findById`, `findAll`, and `deleteById`. Only permits `InMemoryTaskRepository`.
- **`InMemoryTaskRepository`** — concrete implementation backed by a `ConcurrentHashMap` for thread-safe in-memory storage.

### `service`
- **`TaskService`** — service layer injected with a `TaskRepository` via constructor. Encapsulates all business logic:
  - `createTask(description, dueDate)` — validates input and persists a new task.
  - `completeTask(id)` — marks an existing task as completed; throws `TaskNotFoundException` if not found.
  - `deleteTask(id)` — removes a task; throws `TaskNotFoundException` if not found.
  - `getAllTasks()` — returns all stored tasks.
  - `getTaskById(id)` — returns a task by id; throws `TaskNotFoundException` if not found.

### `exception`
- **`TaskNotFoundException`** — unchecked exception thrown when a task with a given id does not exist.
- **`InvalidTaskException`** — unchecked exception thrown when task input fails validation.

## Project Structure

```
src/main/java/org/iips/actions/
├── model/                # Task.java (record)
├── repository/           # TaskRepository.java (sealed interface), InMemoryTaskRepository.java
├── service/              # TaskService.java
└── exception/            # TaskNotFoundException.java, InvalidTaskException.java

src/test/java/org/iips/actions/
├── model/                # TaskTest.java
├── repository/           # InMemoryTaskRepositoryTest.java
├── service/              # TaskServiceTest.java
├── exception/            # ExceptionTest.java
└── integration/          # TaskServiceIT.java (integration tests with Mockito)
```

## CI/CD Workflows

This repository includes three approaches to CI/CD with GitHub Actions:

- **`build.yml`** — Single job covering all stages (compile, test, package, javadoc). Mirrors the reference `cicdCalculator` pipeline.
- **`ci-single-job.yml`** — All stages as steps within a single job. Simple and sufficient for small projects.
- **`ci-multi-job.yml`** — Each stage as an independent job with `needs:` dependencies (compile → test → build → integration-test; coverage and javadoc run in parallel). Enables per-job status badges and finer failure control.
- **Individual workflows** (`compile.yml`, `test.yml`, `integration-test.yml`, `javadoc.yml`) — one workflow per stage; each badge shows the stage name clearly.

### Status badges (one workflow per stage)

> Replace `YOUR_GITHUB_USERNAME/YOUR_REPO_NAME` with your own GitHub username and repository name.

# Status badges

[![Compile](https://github.com/jesujaque/CICDTaskManager/actions/workflows/compile.yml/badge.svg)](https://github.com/jesujaque/CICDTaskManager/actions/workflows/compile.yml)
[![Test](https://github.com/jesujaque/CICDTaskManager/actions/workflows/test.yml/badge.svg)](https://github.com/jesujaque/CICDTaskManager/actions/workflows/test.yml)
[![Build](https://github.com/jesujaque/CICDTaskManager/actions/workflows/build.yml/badge.svg)](https://github.com/jesujaque/CICDTaskManager/actions/workflows/build.yml)
[![Integration Test](https://github.com/jesujaque/CICDTaskManager/actions/workflows/integration-test.yml/badge.svg)](https://github.com/jesujaque/CICDTaskManager/actions/workflows/integration-test.yml)
[![Javadoc](https://github.com/jesujaque/CICDTaskManager/actions/workflows/javadoc.yml/badge.svg)](https://github.com/jesujaque/CICDTaskManager/actions/workflows/javadoc.yml)

## How to build and test

```bash
# Compile the source code
mvn compile

# Run unit tests
mvn test

# Build the JAR artifact
mvn package

# Run integration tests
mvn integration-test

# Generate coverage report (opt-in)
mvn test -P coverage

# Generate Javadoc
mvn javadoc:javadoc
```

## Main Dependencies
- Java 21
- Maven 3.9+
- JUnit Jupiter 6.0.3
- Mockito 5.23.0 (core + mockito-junit-jupiter)

## License
MIT
