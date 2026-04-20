# Elevator Inspection — ServiceNow Application

## Overview

**Elevator Inspection** is a scoped ServiceNow application designed to manage elevator inspection orders, perform structured on-site inspections, generate PDF reports, and email results to customers. It is built for field inspectors (Fulfillers) and supports a full inspection workflow from order creation to report delivery.

---
## Process Flow

---

## Tables

| Table | Description |
|---|---|
| Order | Parent record representing a customer inspection order |
| Elevator Inspection | Child records — one per elevator inspected within an order |
| Company | External — Postman mock server used as external DB, retrieved via ServiceNow REST Message (GET) |


## Data Model Diagram

https://raw.githubusercontent.com/DJF144/Inspect2026/main/Data_Model_Diagram.png

---

## Roles

| Role | Permissions |
|---|---|
| Admin | Can read, write, and delete all orders regardless of assignment |
| Fulfiller (User) | Can read and delete only orders assigned to themselves |

---

## External Integration

Company names are retrieved from a **Postman mock server** acting as an external database. The integration uses a **ServiceNow REST Message (GET)** to fetch the company list and populate the Company Name field on the Order form dynamically at runtime.

---

## Application Structure

### 1. Dashboard


- **Create New Order** button
- **Dashboard** with five analytics tiles:
  - Total count of all orders → opens full order list
  - Count of **Open** orders → opens filtered list
  - Count of **Closed** orders → opens filtered list

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

---

### 4. Inspection Checklist

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

### 5. Image Upload

Inspectors can upload images directly on each elevator inspection record. Images are stored as attachments in `sys_attachment` and are embedded in the generated PDF report.

---

### 6. PDF Report Generation

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

### 7. Send Email to Customer

After generating the PDF, the inspector can send an email to the customer directly from the Order record.

