Here's a single architecture capture document that consolidates today's decisions into one repository-ready specification.

# PALM Cloud Architecture Capture

**Status:** Draft v0.1

## Purpose

This document captures the current architectural direction for the PALM cloud platform and client integration. It records the agreed solution, the alternatives considered, the assumptions made and the remaining open decisions.

This document establishes the architectural baseline for future design and implementation work.

---

# Scope

Included:

* Google cloud platform
* Client application hosting
* API architecture
* Relational database platform
* Authentication
* ESP32/Espruino integration
* Zebra printer integration
* Cost assumptions

Excluded:

* Detailed database schema
* User interface design
* API specifications
* ESP32 firmware implementation
* Label template design

---

# Architectural Principles

The architecture is guided by the following principles:

* Prefer the simplest architecture that satisfies current requirements.
* Use managed cloud services wherever they reduce operational complexity.
* Preserve a clear separation between presentation, business logic and data.
* Edge devices perform business operations rather than direct database access.
* Standardise where practical to reduce complexity.
* Allow incremental evolution without requiring major redesign.

---

# Agreed Google Services

The current architectural baseline uses the following Google services.

| Service                 | Purpose                                             |
| ----------------------- | --------------------------------------------------- |
| Firebase Hosting        | Host the Angular Progressive Web Application        |
| Firebase Authentication | User authentication and identity management         |
| Firebase SQL Connect    | Secure managed data access layer                    |
| Cloud SQL (PostgreSQL)  | Primary relational database                         |
| Cloud Run               | Device API and future server-side business services |

This provides a managed platform while avoiding unnecessary infrastructure.

---

# Overall Architecture

```text
                     Angular PWA
                (Windows / Ubuntu)

                        │

                Firebase Hosting

                        │

                Cloud Run Services
            (Device & Business APIs)

                        │

             Firebase SQL Connect

                        │

             Cloud SQL PostgreSQL



ESP32 Edge Clients
        │
 HTTPS JSON
        │
 Cloud Run Device API



Desktop Clients
        │
 PALM Local Agent
        │
 Zebra USB Printer



ESP32 Stations
        │
 Network Zebra Printer
```

---

# Alternatives Considered

## Firestore

Considered because it offers an attractive free tier.

Rejected because PALM has a naturally relational data model with:

* assets
* relationships
* identifiers
* events
* locations

Using a document database would move relationship management into application code.

---

## Self-hosted PostgreSQL

Considered to minimise cloud cost.

Rejected for the initial implementation because it increases operational effort and reduces availability.

May be revisited in the future if self-hosting becomes desirable.

---

## Custom REST API

A fully custom REST API hosted on Cloud Run was considered.

Current direction is to use Firebase SQL Connect for managed database access while introducing Cloud Run for business-oriented operations and embedded devices.

---

## Multiple Native Desktop Applications

Separate Windows and Linux desktop applications were considered.

Rejected in favour of a single Angular Progressive Web Application.

---

# Free / Low-Cost Assumptions

The architecture assumes hobby-scale usage.

Objectives:

* minimise recurring cost
* minimise administration
* avoid unnecessary infrastructure

Assumptions:

* Firebase free services are used wherever practical.
* SQL Connect prototype environment is suitable for initial development.
* Cloud SQL represents the primary ongoing infrastructure cost.
* Cloud Run usage is expected to remain small during early development.

The architecture should remain suitable for long-term low-volume operation.

---

# Angular Hosting

The primary user interface is a single Angular Progressive Web Application.

Responsibilities:

* user interaction
* workflow guidance
* asset management
* reporting
* administration

The Angular application is hosted using Firebase Hosting.

Benefits include:

* single code base
* Windows and Ubuntu support
* installation as a Progressive Web App
* automatic deployment
* simple update model

Angular remains independent of the hosting provider and may be deployed elsewhere if required.

---

# API Hosting

Cloud Run provides PALM server-side services.

Responsibilities include:

* embedded device integration
* business operations
* validation
* server-side processing
* future integrations

Cloud Run exposes business-oriented endpoints rather than database CRUD interfaces.

Examples:

* AddPlantToPot
* MoveAsset
* RecordWatering
* PrintReplacementLabel

---

# Relational Database Choice

PALM adopts PostgreSQL as the authoritative data store.

Reasons:

* rich relational model
* strong integrity constraints
* mature SQL support
* excellent long-term scalability

Firebase SQL Connect provides the managed access layer between applications and PostgreSQL.

The database remains the authoritative source of PALM data.

---

# Authentication

Human users authenticate using Firebase Authentication.

Embedded devices are authenticated separately.

ESP32 devices are expected to use PALM device credentials issued specifically for edge devices.

The Device API validates device identity before accepting business operations.

This avoids exposing privileged cloud credentials to embedded systems.

---

# ESP32 / Espruino Integration

ESP32 devices are treated as specialised operational terminals.

Responsibilities include:

* barcode scanning
* user interaction
* workflow execution
* HTTPS communication
* optional local printing

ESP32 clients do not communicate directly with the database.

Instead they submit business operations to the Device API.

Example:

```
AddPlantToPot
```

The Device API validates the request and performs the required database transaction.

---

# Zebra Printing Boundary

PALM standardises on Zebra printers.

Printing responsibilities are divided as follows.

## Cloud

Responsible for:

* deciding whether printing is required
* selecting label templates
* generating label content

## Desktop

Desktop systems print through the PALM Local Agent.

```
Angular

↓

PALM Local Agent

↓

USB Zebra Printer
```

## ESP32

Network-connected ESP32 systems may print directly using ZPL.

```
ESP32

↓

TCP/IP

↓

Network Zebra Printer
```

The cloud remains responsible for label content.

The printing device remains responsible for physical output.

---

# Unresolved Decisions

The following architectural decisions remain open.

## Local Agent

Determine whether all desktop printing should pass through the PALM Local Agent or whether Zebra Browser Print should be supported.

---

## Offline Operation

Define the offline strategy for:

* Angular client
* ESP32 clients

including queueing and synchronisation.

---

## Device Authentication

Select the preferred authentication mechanism for ESP32 devices.

Possible approaches include:

* device secrets
* signed tokens
* certificate-based authentication

---

## Label Generation

Determine whether ZPL is:

* generated entirely by the cloud,
* generated by the ESP32,
* or shared between both.

Current preference is cloud-generated templates.

---

## API Organisation

Determine whether Device API endpoints should be grouped by:

* business operation
* bounded context
* service

---

## Business Workflow Engine

Determine the scope of XState within the Angular application and define the equivalent lightweight FSM approach for Espruino.

---

# Summary

The agreed architecture provides:

* a single Angular Progressive Web Application
* managed Google cloud infrastructure
* PostgreSQL as the relational data platform
* Cloud Run as the business integration layer
* ESP32 Espruino edge devices using business operations
* Zebra printer standardisation
* a clear path for incremental evolution while maintaining a simple and low-cost architecture.
