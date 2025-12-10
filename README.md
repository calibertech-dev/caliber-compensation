# Caliber Compensation

Caliber Compensation is the unified earnings, commission, and payout engine of the **Caliber Platform**.  
It provides a flexible, business-unit–aware model for defining compensation plans, assigning them to users, calculating earnings, and generating pay statements. The package integrates cleanly with Sales, Commerce, Project Management, Field Service, and any other Caliber modules through a simple parent-record linking model.

This package is designed to support everything from straightforward sales commissions to multi-tiered overrides, technician spiffs, job-based bonuses, and partner program payouts.

---

## Overview

Caliber Compensation defines three core layers:

1. **Plan Configuration**  
   How compensation is defined: rates, criteria, tiers, timeframes, and applicability.

2. **Earnings Ledger (Internal Payables)**  
   The system of record for *what was earned*, *why it was earned*, and *who earned it*.

3. **Payout Layer**  
   Pay statements, batches, and export-ready totals for downstream payroll or financial systems.

These layers allow compensation logic to remain modular, auditable, and extensible across all Caliber Platform packages.

---

## Core Objects

### **Compensation_Plan__c**
Defines a compensation structure active within a Business Unit.  
A plan may represent:

- A sales commission structure  
- A technician spiff program  
- A partner override agreement  
- A custom payout model

Key features:

- Effective start/end dates  
- Active/inactive status  
- Plan priority for resolving overlaps  
- BU-scoping for multi-entity organizations

Each plan contains one or more **Compensation_Rate__c** records that define how earnings are calculated.

---

### **Compensation_Rate__c**
Defines specific rules inside a plan — the “if this, then pay that” logic.

Examples:

- 10% of gross profit on crawlspace jobs  
- $50 flat bonus for each completed mold remediation  
- 3% of invoice revenue for residential sales  
- Tiered rates based on gross margin percentage  
- Additional overrides by work type or product

Fields support:

- Revenue-based, margin-based, or flat payouts  
- Product-level targeting  
- Work type targeting  
- Business-unit filtering  
- Effective dating and activation control

---

### **Compensation_Assignment__c**
Maps Users to Compensation Plans.

Assignments determine:

- Which plan applies  
- To which Business Unit  
- During what date range  
- Whether the plan is primary  
- How multiple plans interact (via plan priority)

This makes the engine fully multi-BU aware and capable of supporting technicians who work across divisions or variable sales org structures.

---

## Earnings Layer

### **Internal_Payable__c**
This is the heart of Caliber Compensation — each record represents a single earning event.

Examples:

- Sales commission earned when an invoice is posted  
- Technician spiff earned when a fulfillment job completes  
- Partner payout earned when a project milestone is reached  
- A bonus tied to a specific Sales Order line  
- Override earnings based on another user’s work

Each Internal Payable includes:

- `Payee__c` — the user earning the amount  
- `Amount__c` — the calculated compensation  
- Rate + Plan that produced it  
- Business Unit context  
- Earned, due, and paid dates  
- Status (Pending, Approved, Paid)  
- Flexible polymorphic parent fields:
  - `Parent_Record__c`
  - `Parent_Record_Id__c`
  - `Parent_Record_Type__c`
  - `Parent_Record_Name__c`

This model makes the package compatible with any Caliber object that may drive compensation:  
**Invoice, Sales Order, Project, Fulfillment Job, Work Order, Service Appointment**, and future modules.

**Internal_Payable__c is the system of record for “what was earned and why.”**

---

## Payout Layer

### **Pay_Statement__c**
A pay cycle or payroll document summarizing a user’s earnings.

Contains:

- Gross pay  
- Net pay  
- Total taxes  
- Total reimbursable items  
- Total deductions  
- Pay period start/end  
- Pay date  
- Payment method  
- Associated Business Unit  
- Optional external IDs for integration with payroll systems

Each Pay Statement rolls up multiple Pay Statement Lines.

---

### **Pay_Statement_Line__c**
Represents a specific line item on a pay statement.

Examples:

- Commission payout  
- Bonus  
- Override  
- Reimbursement  
- Deduction  
- Tax line

Supports:

- Line type  
- Line category  
- Quantity × rate  
- Tax treatment  
- Free-text description

---

### **Pay_Batch__c**
A container for processing and exporting multiple pay statements at once.

Tracks:

- Batch date  
- Status  
- Total gross  
- Total net  
- Statement count  
- Optional external batch identifiers for integrations

This object makes it easy to generate payroll exports or coordinate multi-BU payroll runs.

---

## Integration With Other Caliber Packages

Caliber Compensation is intentionally **decoupled** from originating activity.  
Any object can generate earnings through the polymorphic parent model.

Typical integrations:

### **Caliber Commerce**
- Commissions triggered by invoice posting  
- Sales Order–based incentives  
- Performance bonuses based on revenue or margin  
- Partner-program payouts

### **Caliber Project Management**
- Project milestones generating bonuses  
- Job profitability–based payouts  
- Phase completion incentives

### **Caliber Field Operations / Fulfillment**
- Technician spiffs  
- Completion bonuses tied to Work Orders, Fulfillment Jobs, or Service Appointments  
- Labor-based incentives

### **Caliber Partner Programs**
- Overrides  
- Referral commissions  
- Co-selling incentives

Because Internal Payables capture **both the earning event and its parent record**, the entire compensation chain remains transparent and auditable.

---

## Architectural Principles

Caliber Compensation follows several design rules:

- **Earnings are calculated once and snapshotted.**  
  Historical accuracy is preserved even if upstream objects later change.

- **Parent records own business metrics (revenue, margin, GP, etc.).**  
  Compensation reads these metrics but does not calculate or store them as source-of-truth.

- **Fully multi-entity aware.**  
  Business Unit context flows consistently through plans, assignments, and payables.

- **No hard dependencies on specific objects.**  
  The polymorphic parent model allows the engine to unify compensation logic across all Caliber packages.

- **Downstream-ready.**  
  Pay Statements and Pay Batches form a clean export model for payroll systems and financial tools.

---

## Typical Compensation Flow

1. **An event occurs**  
   (Invoice posted, job completed, project milestone closed, etc.)

2. **Applicable plans are determined**  
   Based on Business Unit, user, date, and plan/assignment rules.

3. **Matching compensation rates are evaluated**  
   Using product, work type, revenue, margin, or other criteria.

4. **Earnings are created**  
   One or more `Internal_Payable__c` records.

5. **Payables move through approval → payment**  
   Once approved, they feed into Pay Statements.

6. **Pay batches are generated**  
   Allowing grouped exports and payroll processing.

---

## Extensibility

The package is designed to grow with the Caliber Platform:

- Add new `Applies_To__c` categories  
- Introduce tiered or bracketed compensation structures  
- Add plugin-style custom calculators  
- Integrate with external payroll APIs  
- Support partner profit-sharing models  
- Implement multi-entity rollups and reporting engines

Every piece stays in metadata, Apex, and objects that play cleanly in unlocked packages.

---

## Summary

Caliber Compensation gives the Caliber Platform a powerful, flexible, consistent foundation for all forms of earnings and payouts — from simple commissions to complex multi-layered programs.

The package unifies:

- Plan management  
- Assignment routing  
- Rate evaluation  
- Earnings generation  
- Payout processing  

All while remaining fully Business-Unit aware, fully auditable, and compatible with every package across the Caliber ecosystem.

## License

This project is licensed under the terms described in the LICENSE.md
 file.

© 2025 Caliber Technologies LLC
For commercial or implementation inquiries, contact dev@calibertech.net
