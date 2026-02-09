# Web Application Layout

Web applications vary significantly in design, purpose, and infrastructure setup. Understanding their structure, components, and deployment models is crucial for penetration testing.

## Overview

Web application layouts consist of three main categories:

| Category | Description |
|----------|-------------|
| **Web Application Infrastructure** | Structure of required components (e.g., database) for proper functioning |
| **Web Application Components** | All components the application interacts with (UI/UX, Client, Server) |
| **Web Application Architecture** | Relationships between various web application components |

## Infrastructure Models

### Client-Server

The standard model where a server hosts the application and distributes it to clients via HTTP requests.

- Front end: Interpreted/executed client-side (browser)
- Back end: Compiled/interpreted/executed server-side

### One Server

Entire application(s) and database hosted on a single server.

**Risks:** All-or-nothing vulnerability exposure; single point of failure.

### Many Servers - One Database

Database separated on dedicated server; multiple web application servers access it.

**Benefits:** Segmentation reduces impact of individual server compromise.

### Many Servers - Many Databases

Each web application has its own database (or database server).

**Benefits:** Strong segmentation, redundancy, best security posture.

## Web Application Components

- Client
- Server / Webserver
- Web Application Logic
- Database
- Services (Microservices)
- 3rd Party Integrations
- Functions (Serverless)

## Three-Tier Architecture

| Layer | Description |
|-------|-------------|
| **Presentation Layer** | UI components (HTML, JavaScript, CSS) |
| **Application Layer** | Request processing, authorization, validation |
| **Data Layer** | Data storage and access |

## Microservices

Independent, single-task components that communicate statelessly. Benefits:

- Agility
- Flexible scaling
- Easy deployment
- Reusable code
- Resilience

## Serverless Architecture

Cloud-provided platforms (AWS, GCP, Azure) handle infrastructure management, allowing deployment in stateless containers (Docker).

## Security Implications

Vulnerabilities often stem from **design flaws** rather than code errors—particularly inadequate access control (RBAC) or improper segmentation. Security must be integrated throughout the development lifecycle.
