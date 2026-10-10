# Wildlife Information System (WIS)
### Software Artifacts — Analysis and Design

The **Wildlife Information System (WIS)** is a proposed web-based system for organizing and accessing wildlife information. It is designed to maintain animal profiles and related species, movement, DNA, and health records, with authenticated access for users and external applications through a RESTful API.

This repository section contains the project's **analysis and design artifacts**, including system models, diagrams, architecture, database relationships, and API specifications.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Key Features](#key-features)
- [User Roles and External Actors](#user-roles-and-external-actors)
- [System Analysis Artifacts](#system-analysis-artifacts)
- [System Design](#system-design)
- [Architecture](#architecture)
- [Core Data Entities](#core-data-entities)
- [API Design](#api-design)
- [Interface Design](#interface-design)
- [Technology Stack](#technology-stack)
- [Security and Validation](#security-and-validation)
- [Project Documentation](#project-documentation)
- [Future Implementation Notes](#future-implementation-notes)
- [Contributors](#contributors)

---

## Project Overview

Wildlife Information System is intended to provide a structured way to record, manage, and retrieve wildlife data. Each animal record is associated with a species and can have related movement records, DNA samples, and health records. Researchers and government officials can request relevant information, while data-entry operators maintain wildlife records and administrators manage user accounts.

The system is designed around a three-tier architecture:

1. **Presentation layer:** Web interface built with HTML, CSS, and JavaScript.
2. **Application layer:** REST API and business logic using Node.js and Express.js.
3. **Data layer:** PostgreSQL database for persistent storage.

> **Project status:** This repository describes the analysis and design artifacts. The architecture and endpoints below are design specifications; implementation and deployment status should be updated as development progresses.

## Objectives

- Maintain organized animal and species information.
- Record animal movement and location history.
- Store DNA sample and health information associated with animals.
- Support authenticated searches and data access through a REST API.
- Provide role-based access for different user types.
- Support administrative user management and audit logging.
- Define a database structure that connects related wildlife records.

## Key Features

- Animal record creation, viewing, updating, and deletion.
- Species information and conservation-status records.
- Animal movement history, including GPS coordinates and timestamps.
- CSV bulk upload design for movement data.
- DNA sample records and species-confirmation information.
- Animal health records, treatments, and vaccination details.
- JWT-based authentication and user-role authorization.
- API queries for researchers and external applications.
- Administrative user-account management.
- API responses using JSON and HTTP status codes.

## User Roles and External Actors

| Actor | Responsibility |
|---|---|
| Data Entry Operator | Enters and maintains animal, movement, DNA, and health information. |
| Researcher | Requests wildlife information through the system or API. |
| System Administrator | Manages user accounts and administrative operations. |
| Government Official | Requests wildlife information for reporting and oversight. |
| External Application | Accesses permitted data through the REST API using authentication. |

## System Analysis Artifacts

The analysis documentation models the system's behavior, data flow, and information structure.

### 1. Domain Model

Identifies the key real-world concepts and their relationships:

- Animal
- Species
- Movement Record
- DNA Sample
- Health Record
- User
- Location

An animal belongs to a species and can have multiple movement records, health records, and DNA samples.

### 2. Data Flow Diagrams (DFDs)

- **Level 0 — Context Diagram:** Shows the WIS as a single process and its interactions with researchers, data-entry operators, administrators, government officials, and external applications.
- **Level 1 — Process Diagram:** Breaks the system into user authentication, data-entry processing, data search/API request processing, and user processing.

### 3. System Sequence Diagrams

The analysis includes key interaction flows such as:

- **Add Animal Record:** The operator enters required details; the system validates the fields, stores the record, and returns a success message with the animal identifier.
- **Researcher API Query:** A researcher sends an authenticated HTTP GET request; the system validates the token, queries the database, and returns JSON with an HTTP status code.

### 4. Activity Diagram

Describes the API query workflow, including token validation, required-parameter checks, database searching, JSON response formatting, and error handling. The design specifies HTTP `401` for an invalid token and `400` when required query parameters are missing.

### 5. Entity-Relationship (ER) Diagram

Documents the database tables, primary and foreign keys, and relationships. Animal records reference species, while movement, DNA, and health records reference animals. User operations are intended to be tracked through audit logs.

## System Design

### Class Diagram

The design specifies the following core classes:

| Class | Example attributes | Main operations |
|---|---|---|
| `Animals` | `animalId`, `speciesId`, `name`, `gender`, `ageYears`, `weightKg`, `locationId`, `addedBy`, `addedAt` | `save()`, `update()`, `delete()`, `getMovementHistory()`, `getHealthRecords()`, `getDNASamples()` |
| `Movement_Record` | `recordId`, `animalId`, `latitude`, `longitude`, `recordedAt`, `source` | `save()`, `getByAnimal()`, `bulkInsert()` |
| `DNA_Sample` | `sampleId`, `animalId`, `collectedAt`, `labName`, `result`, `speciesConfirmed` | `save()`, `getByAnimal()` |
| `Health_Record` | `healthId`, `animalId`, `date`, `condition`, `treatment`, `vetName`, `vaccineGiven` | `save()`, `update()`, `getByAnimal()` |
| `User` | `userId`, `name`, `email`, `passwordHash`, `role`, `createdAt` | `login()`, `logout()`, `updateProfile()`, `changePassword()` |
| `Species` | `speciesId`, `commonName`, `scientificName`, `conservationStatus`, `habitat` | `save()`, `getAll()`, `getByName()` |

*Note: These are design-level classes and operations, not confirmation that the methods have been implemented.*

## Architecture

The system follows a **three-tier architecture**.

| Layer | Technology / component | Responsibility |
|---|---|---|
| Presentation | HTML, CSS, JavaScript | Web interface for data entry, search, and export. |
| Application | Node.js, Express.js REST API | Business logic, authentication, request validation, query processing, and response formatting. |
| Data | PostgreSQL | Persistent storage for wildlife records, users, and audit logs. |

External clients communicate with the application layer through the REST API. The application layer processes requests and interacts with PostgreSQL.

## Core Data Entities

- **Animals:** Core animal profile and its species/location references.
- **Species:** Common and scientific names, habitat, and conservation status.
- **Movement Records:** GPS coordinates, source, and recorded timestamp for animal movements.
- **DNA Samples:** Collection date, laboratory, result, and species-confirmation status.
- **Health Records:** Health condition, treatment, veterinarian, and vaccination details.
- **Users:** Account details, password hash, and assigned role.
- **Location:** Location information associated with animal records.
- **Audit Logs:** Records of user operations, as described in the analysis documentation.

## API Design

The following REST API endpoints are specified in the design documents.

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/api/animals` | Retrieve animal records, with filtering support. |
| `GET` | `/api/animals/:id` | Retrieve one animal by ID. |
| `POST` | `/api/animals` | Create an animal record. |
| `PUT` | `/api/animals/:id` | Update an animal record. |
| `DELETE` | `/api/animals/:id` | Delete an animal record. |
| `GET` | `/api/animals/:id/movements` | Retrieve an animal's movement history. |
| `POST` | `/api/movements/upload` | Bulk-upload movement data from CSV. |
| `GET` | `/api/animals/:id/health` | Retrieve an animal's health records. |
| `GET` | `/api/animals/:id/dna` | Retrieve an animal's DNA records. |
| `POST` | `/api/auth/login` | Authenticate a user and obtain a JWT token. |
| `GET` | `/api/species` | Retrieve species records. |

### Authentication and Request Flow

The design specifies JWT bearer-token authentication for protected API requests. A typical animal query is intended to follow this flow:

1. Client sends a request with query parameters and an `Authorization: Bearer <token>` header.
2. API gateway/authentication logic verifies the token and identifies the user's role.
3. The query controller passes request parameters to the animal service.
4. The service queries the relevant PostgreSQL tables.
5. The API returns the result as JSON with an appropriate HTTP status code.

Example request shape from the design:

```http
GET /api/animals?species=Snow%20Leopard
Authorization: Bearer <token>
```

The design describes `200` for a successful query, `400` for missing required parameters, and `401` for an invalid token. Exact behavior for other cases should be confirmed during implementation.

## Interface Design

The proposed web interface includes:

- **Login Page:** Email and password fields, login button, and an error message for incorrect credentials.
- **Dashboard:** Welcome message, total-animal and species statistics, recent records, and navigation.
- **Animal List Page:** Sortable table with view, edit, and delete actions.
- **Add Animal Form:** Fields for species, name, gender, age, weight, and location.
- **Movement Records Page:** Table of GPS coordinates and timestamps, with a CSV upload option.
- **API Documentation Page:** Endpoint list, required parameters, and example responses.
- **User Management:** Administrator-only user table with account creation and removal options.

### Visual Design Principles

The design document specifies a simple blue-and-white color scheme, descriptive button labels, red error messages, and green success messages.

## Technology Stack

| Technology | Intended use |
|---|---|
| HTML | Web page structure |
| CSS | Styling and layout |
| JavaScript | Client-side interactions |
| Node.js | Server-side JavaScript runtime |
| Express.js | REST API and routing |
| PostgreSQL | Relational database |
| JWT | Token-based authentication |
| JSON | API request/response data format |
| CSV | Bulk movement-data upload format |

## Security and Validation

The design identifies these security and validation measures:

- JWT bearer-token verification for API requests.
- Authentication and user-role authorization.
- Validation of required fields when creating animal records.
- Validation of required query parameters.
- Storage of password hashes rather than plain-text passwords.
- Audit logging of user operations.

Implementation should additionally verify access permissions for each endpoint, validate and sanitize inputs, protect credentials and secrets, and enforce appropriate database constraints.

## Project Documentation

The project artifacts are organized into two main documents:

1. **Analysis — Chapter 3:** Domain model, DFD Level 0 and Level 1, system sequence diagrams, activity diagram, and ER diagram.
2. **Design — Chapter 4:** Class diagram, API query sequence, three-tier architecture, API endpoint specifications, and interface design.

Suggested repository layout:

```text
wildlife-information-system/
├── README.md
├── docs/
│   ├── srs_ch3_analysis.pdf
│   └── srs_ch4_design.pdf
└── src/                         # Add when implementation begins
```

Place the two PDF documents in `docs/` and adjust their filenames if necessary. If the PDFs are stored elsewhere in your repository, update the links below.

- [Analysis Document — Chapter 3](docs/srs_ch3_analysis.pdf)
- [Design Document — Chapter 4](docs/srs_ch4_design.pdf)

## Future Implementation Notes

The analysis and design documents establish the intended system behavior and structure. Possible next steps are:

- Implement the database schema and relationships in PostgreSQL.
- Develop the Express.js routes, services, and authentication middleware.
- Implement role-based authorization and input validation.
- Build the web interface for animal and related-record management.
- Implement and test CSV movement-data imports.
- Add API examples, automated tests, setup instructions, and deployment documentation.
- Update this README as the implementation becomes available.

## Contributors

Prepared by:

- Muhammad Ehtisham
- Fahad Naeem Khan
- Muhammad Abbas

---

**Project:** Wildlife Information System (WIS)  
**Document reference:** WIS-SRS-001  
**Artifact focus:** System Analysis and Design
