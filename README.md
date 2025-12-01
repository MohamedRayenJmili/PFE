# Smart Attendance & Leave Management Platform

A full-stack attendance and leave management solution combining **Power Apps**, **SQL Server**, **Python OCR**, and **Power BI** to digitize badge-based attendance, enforce HR rules, and provide real-time monitoring dashboards.

> This project was developed as part of an engineering final-year project (PFE).  
> It demonstrates end-to-end integration across data capture, business logic, and analytics.

---

## 🔎 Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Features](#features)
4. [Tech Stack](#tech-stack)
5. [Screenshots](#screenshots)
6. [Repository Structure](#repository-structure)
7. [Getting Started](#getting-started)
    - [1. Database](#1-database)
    - [2. Power Apps](#2-power-apps)
    - [3. OCR Service (Python)](#3-ocr-service-python)
    - [4. Power BI](#4-power-bi)
8. [Usage](#usage)
    - [Admin / HR Workflow](#admin--hr-workflow)
    - [Employee Workflow](#employee-workflow)
9. [Data Model](#data-model)
10. [Limitations & Future Work](#limitations--future-work)
11. [License](#license)
12. [Contact](#contact)

---

## 🧩 Overview

The goal of this project is to provide a **robust, auditable, and extensible** solution for managing employee attendance and leave:

- Replace manual paper-based attendance with **semi-automated OCR capture** of CIN (national ID) from badges.
- Provide **role-based Power Apps interfaces** (Admin, HR, Manager, Employee) for daily HR operations.
- Centralize all business rules and data in **SQL Server** for reliability and auditability.
- Deliver **Power BI dashboards** for attendance KPIs, leave metrics, and data quality monitoring.
- Prepare the architecture for **future MLOps** evolution (telemetry, preset registry, retraining, etc.).

---

## 🏗 Architecture

The solution is composed of four main layers:

1. **Data Layer – SQL Server**
   - Core tables: `Employees`, `Attendance`, `LeaveRequests`, `LeaveBalances`, `Notifications`, `OCR_Telemetry`, `HR_Corrections`, `AuditLog`.
   - Enforces business rules (constraints, referential integrity) and stores all operational data.

2. **Application Layer – Power Apps**
   - Canvas app for:
     - Employee CRUD (with profile picture in Base64).
     - Attendance review & validation.
     - Leave request submission & approval.
     - Profile view and role-based navigation.

3. **Capture & OCR Layer – Python**
   - OpenCV + Tesseract pipeline:
     - Webcam capture.
     - Card detection + perspective transform.
     - ROI extraction for CIN.
     - OCR with confidence scoring & normalization.
   - Writes into `Attendance` and `OCR_Telemetry` (or flags rows as `NeedsReview`).

4. **Analytics Layer – Power BI**
   - Import mode from SQL Server.
   - Star-ish schema with `DimDate`, `Employees` (dimension) and `Attendance`, `LeaveRequests`, `OCR_Telemetry` (facts).
   - KPI dashboards for:
     - Attendance rate, delays, absences.
     - Leave utilization and pending requests.
     - OCR quality and data quality (needs review, corrections).

A high-level MLOps-style diagram is included in the report to illustrate how telemetry and HR corrections could feed future model/preset improvements.

---

## ✨ Features

### Power Apps

- **Authentication & Role-Based Navigation**
  - Roles: `Admin`, `HR`, `Manager`, `Employee`.
  - Dynamic navigation menu depending on role.
- **Employee Management**
  - Add / edit employees.
  - Store CIN, department, job title, role, status.
  - Upload profile pictures (stored as Base64 in SQL).
- **Attendance Management**
  - Global view for Admin/HR.
  - Filtered view per employee when logged as Employee.
  - `NeedsReview` flag to identify uncertain OCR entries.
- **Leave Management**
  - Employee leave request form (Annual / Sick / Mission / Unpaid / Permission).
  - Automatic calculation of leave days and minutes (permissions).
  - Balance tracking via `LeaveBalances` (total, used, remaining).
- **Profile Screen**
  - Role badge with color coding.
  - Profile picture rendering from Base64.
  - Quick access to personal attendance and leave info.

### OCR (Python)

- Webcam-based capture with live preview.
- Robust card contour detection (Canny + adaptive thresholding).
- Perspective correction to standardize the card image.
- ROI cropping + OCR for CIN using Tesseract (numeric whitelist).
- Confidence-based decision:
  - High confidence + known CIN → insert `Attendance` row.
  - Low confidence or unknown CIN → mark `NeedsReview` and log in `OCR_Telemetry`.

### Power BI

- **Overview**
  - Active employees.
  - Attendance rate %.
  - Needs review %.
  - Total approved leave days.
  - Pending leave requests.
- **Attendance Monitoring**
  - Daily attendance rate.
  - Breakdown of Present / Late / Absent.
  - Analysis by department and period.
- **Leaves**
  - Leave requests by status and type.
  - Leave utilization vs allocated balances.
- **Employee Statistics**
  - Workforce by department, role, status.
  - Tenure bands and CIN coverage.
- **Exceptions & Data Quality**
  - Rows flagged `NeedsReview`.
  - HR corrections dashboard.
  - OCR telemetry (confidence, decisions, failures).

---

## 🧰 Tech Stack

- **Frontend / Apps**
  - Microsoft Power Apps (Canvas app)

- **Backend / Data**
  - Microsoft SQL Server (on-prem / local instance)
  - T-SQL stored procedures & constraints

- **OCR & Image Processing**
  - Python 3.x
  - OpenCV
  - Tesseract OCR
  - PyODBC

- **Analytics**
  - Microsoft Power BI Desktop
