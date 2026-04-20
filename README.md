# Elevator Inspection — ServiceNow Application

## Overview

**Elevator Inspection** is a scoped ServiceNow application designed to manage elevator inspection orders, perform structured on-site inspections, generate PDF reports, and email results to customers. It is built for field inspectors (Fulfillers) and supports a full inspection workflow from order creation to report delivery.

---
## Process Flow

https://raw.githubusercontent.com/DJF144/Inspect2026/main/Process_Flow.png

---

## Tables

| Table | Description |
|---|---|
| Order | Parent record representing a customer inspection order |
| Elevator Inspection | Child records — one per elevator inspection within an order |
| Company | External — Postman mock server used as external DB, retrieved via ServiceNow REST Message (GET) |


## Data Model Diagram

https://raw.githubusercontent.com/DJF144/Inspect2026/main/Data_Model_Diagram.png

---

## Roles

| Role | Permissions |
|---|---|
| Admin | Can create, read, write, and delete all orders regardless of assignment |
| Fulfiller (User) | Can create, read, write, and delete all orders assigned to the user |

---

## External Integration

Company names are retrieved from a **Postman mock server** acting as an external database. The integration uses a **ServiceNow REST Message (GET)** to fetch the company list and populate the Company Name field on the Order form dynamically at runtime.

---

## Application Structure

### 1. Dashboard


- **Create New Order** button
- **Dashboard** with five analytics tiles:
  - **My Tasks** → opens all tasks for the user and state is open
  - **Critical Tasks** → opens all tasks for the user and priority is critical
  - **Closed Completed Tasks** → opens all tasks for the user and state is Closed Complete
  - **Closed Incomplete Tasks** → opens all tasks for the user and state is open Closed Incomplete
  - **All Tasks** → opens all tasks for the user

---
### 2. Order Management

#### Order Form Fields

| Field | Type | Notes |
|---|---|---|
| Order Number | Text | Auto-generated unique ID — read only |
| Company Name | Choice | Fetched from external DB via ServiceNow REST Message GET |
| Customer Name | Text | Free text |
| Customer Email | Email | Used for report delivery |
| Assigned To | Reference (User) | Auto-populated with the logged-in user — hidden on form |
| State | Choice | Open / Closed |

An Order is the parent of Elevator Inspections and can have multiple inspections assigned.

---

### 3. Inspection Checklist

Each elevator inspection includes a structured checklist grouped into four sections. Each checklist item has a result choice: **Passed**, **Failed**, or **N/A**. Each section includes a **Notes** field (String) for free-text observations.


#### Section 1 — General Operation

| Field | Type |
|---|---|
| Elevator starts and stops smoothly | Passed / Failed / N/A |
| No unusual noises or vibrations | Passed / Failed / N/A |
| Doors open and close properly | Passed / Failed / N/A |
| Floor leveling is accurate | Passed / Failed / N/A |
| General Operation Notes | String |

#### Section 2 — Safety Features

| Field | Type |
|---|---|
| Emergency stop button works | Passed / Failed / N/A |
| Alarm bell functions | Passed / Failed / N/A |
| Emergency phone / intercom works | Passed / Failed / N/A |
| Overload warning system operational | Passed / Failed / N/A |
| Safety brakes tested | Passed / Failed / N/A |
| Ventilation fan operational | Passed / Failed / N/A |
| Safety Features Notes | String |

#### Section 3 — Doors & Entrances

| Field | Type |
|---|---|
| Door sensors (reopen function) working | Passed / Failed / N/A |
| No door obstructions or damage | Passed / Failed / N/A |
| Hall call buttons functioning | Passed / Failed / N/A |
| Doors & Entrances Notes | String |

#### Section 4 — Cab Interior

| Field | Type |
|---|---|
| Lighting works properly | Passed / Failed / N/A |
| Mirrors and panels not damaged | Passed / Failed / N/A |
| Handrails secure (if present) | Passed / Failed / N/A |
| Cab Interior Notes | String |

---

### 4. Image Upload

Inspectors can upload images directly on each elevator inspection record. Images can be selected from the device's photo library or taken on the spot using the tablet camera. Images are stored as attachments in sys_attachment and are embedded in the generated PDF report

---

### 5. PDF Report Generation

A **Generate PDF** UI Action is available on the Order record.

**Behavior:**

- Triggered via a Script Include: `OrderPDFGenerator`
- The script:
  1. Fetches the Order record and all linked Elevator Inspection records
  2. For each inspection, retrieves the latest uploaded image from `sys_attachment` and converts it to Base64
  3. Builds a structured HTML report with all inspection data, color-coded results, and embedded images
  4. Converts the HTML to PDF using `sn_pdfgeneratorutils.PDFGenerationAPI.convertToPDF()`
  5. Attaches the generated PDF to the Order record

---

### 6. Send Email to Customer

After generating the PDF, the email is sent automatically via a ServiceNow Notification. When the Order state is set to Closed Complete, the notification is triggered and sends the PDF report to both the customer and the inspector.


## Required Plugins

| Plugin | ID | Purpose |
|---|---|---|
| PDF Generation Utilities | `com.sn_pdfgeneratorutils` | Required to generate and attach the PDF report to the Order record |

