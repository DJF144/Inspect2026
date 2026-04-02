# ServiceNow Phone Inspection App

## Overview
This app simulates a real-world **Phone inspection workflow** using **ServiceNow**.  
It supports structured inspection data collection, automated PDF reporting, dashboards, and API integration.  

---

## Features
- Configurable forms and fields for different inspection types  
- API integrations for external system data  
- Automated PDF generation of inspection reports  
- Dashboards for operational insights  
- ATF automated testing for reliability  
---

## Architecture / How It Works
- **Tables / Data Model:** Core tables (order and device) 
- **Configurable Forms & Fields:** Field sets and UI policies allow flexibility per use case  
- **Flows / Script Includes:** Centralized logic for scoring, status handling, and automation  
- **Templates:** Base app can be cloned and customized for new inspections  
- **Dashboards & Reports:** Standardized KPIs applied to any inspection type  

---

## Installation
1. Import the application into your ServiceNow instance  
2. Configure any required API connections for external data  
3. Customize forms and fields for your inspection type  
4. Run ATF automated tests to validate functionality  

---

## Usage / Demo
1. Open a new inspection form  
2. Fill out fields (e.g., camera, battery, frame, touch responsiveness)  
3. Submit the inspection  
4. Generate the PDF report  
5. View dashboard metrics and insights  
 
