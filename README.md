# Wildlife Information System (WIS)

A centralized web-based system for storing, managing, and sharing wildlife data  including animal profiles, movement tracking, DNA records, and health information through a secure RESTful API.

> **Document No:** WIS-SRS-001 &nbsp;|&nbsp; **Version:** 2.0 &nbsp;|&nbsp; **Date:** June 2026

<!-- Add your system preview screenshot below -->
<p align="center">
  <img src=".resources/images/preview.jpeg" alt="Wildlife Information System Preview" width="800">
</p>

---

## About The Project

Wildlife data around the world is currently scattered across spreadsheets, paper records, and isolated databases maintained by different universities, government departments, and research organizations. This fragmentation makes it difficult for researchers, veterinarians, and government agencies to access accurate, up-to-date information.

**Wildlife Information System (WIS)** solves this by providing a single, centralized database for wildlife data, accessible through a secure API — making wildlife research, monitoring, and conservation more efficient.

### Key Problems Solved
- **Data Isolation** — no more scattered, unshared records
- **No Standard Format** — unifies Excel, PDF, and paper-based records
- **Lack of Open Access** — provides authorized API access instead of manual requests
- **Missed Analysis Opportunities** — centralized data makes trends (disease, migration, population decline) easier to spot
- **Wasted Research Effort** — prevents duplicate data collection

---

## Features

- **Animal Profile Management** — create, update, and delete wildlife records with auto-generated unique IDs
- **Movement Tracking** — store GPS coordinates and timestamps, with CSV bulk upload support
- **DNA & Biological Records** — log lab results and species-confirmation data linked to each animal
- **Health & Veterinary Records** — track vaccinations, diagnoses, and treatments
- **RESTful API** — JWT-authenticated endpoints returning structured JSON data
- **Role-Based User Management** — Admin, Data Entry Operator, Researcher, Government Official, and Developer roles
- **Search & Filter** — paginated search by species, location, or date range
- **Data Export** — download search results as CSV or JSON

---

##  Tech Stack

| Component | Technology |
|---|---|
| Backend Framework | Node.js with Express.js |
| Database | PostgreSQL |
| API Type | RESTful (JSON) |
| Authentication | JWT (JSON Web Tokens) |
| Frontend | HTML, CSS, JavaScript |
| Version Control | Git / GitHub |
| Hosting (Future) | AWS / DigitalOcean |
| Data Export Formats | JSON, CSV |

---

##  Architecture

WIS follows a **3-Tier Architecture**:

1. **Presentation Layer** — HTML/CSS/JS web interface for data entry, search, and export
2. **Application Layer** — Node.js + Express.js API server handling business logic, authentication, and query processing
3. **Data Layer** — PostgreSQL database storing all wildlife records, users, and audit logs

---

##  API Reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/login` | User login and JWT token generation |
| `GET` | `/api/animals` | Get list of all animals (with filters) |
| `GET` | `/api/animals/:id` | Get a single animal by ID |
| `POST` | `/api/animals` | Add a new animal record |
| `PUT` | `/api/animals/:id` | Update an animal record |
| `DELETE` | `/api/animals/:id` | Delete an animal record |
| `GET` | `/api/animals/:id/movements` | Get movement history for an animal |
| `POST` | `/api/movements/upload` | Bulk upload CSV movement data |
| `GET` | `/api/animals/:id/health` | Get health records for an animal |
| `GET` | `/api/animals/:id/dna` | Get DNA records for an animal |
| `GET` | `/api/species` | Get list of all species |

All endpoints (except login) require a valid `Authorization: Bearer <token>` header.

---

##  User Roles

| Role | Access Level | Capabilities |
|---|---|---|
| **Admin** | Full | Manage users, full CRUD on all entities, system configuration |
| **Data Entry Operator** | Write + Read | Add/edit animal, movement, DNA, and health data |
| **Researcher** | Read + Export | Query and export data in CSV/JSON |
| **Government Official** | Read Only | View and search animal information |
| **Developer** | API Read | Access public endpoints via JWT-authenticated requests |

---

## 🚀 Getting Started

### Prerequisites
- [Node.js](https://nodejs.org/) v18+
- [PostgreSQL](https://www.postgresql.org/) 14+
- [Git](https://git-scm.com/)

### Installation

- will add The link of our Working Project. Soon



---



> The current version (v1.0) is a centralized data-storage and API system. It does not include live tracking maps, mobile apps, AI-based recognition, or multi-language support — these are planned for future releases.

---

##  Testing

Testing follows a **Black Box Testing** approach using **Postman** for API testing and browser DevTools for the web interface.

- **Unit Testing** — each API endpoint tested individually during development
- **Integration Testing** — performed after all endpoints are built
- **System Testing** — full application flow, from login to data export
- **Acceptance Testing** — final validation against requirements before submission

---

##  Authors

- Muhammad Ehtisham
- Fahad Khan
- Muhammad Abbas

---

##  License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

##  Contact

For questions or contributions, feel free to open an issue or reach out to the project maintainers.