# UC-001 — Submit Loan Application

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| `0.0.1` | 2026-10-15 | - Initial version based on business and risk stakeholder meeting notes. |
| `0.0.2` | 2026-10-15 | - Clarified that only the customer or spouse may own the vehicle. |
| `0.0.3` | 2026-10-15 | - Aligned partner access and operating assumptions with OB-001. |

## Overview

| Use Case Element | Description |
|---|---|
| **ID** | UC-001 |
| **Name** | Submit Loan Application |
| **Primary Actor** | Partner System |
| **Secondary Actors** | Loan Submission API, Risk Scoring Process |
| **Description** | A legally contracted partner submits a car-collateral loan application. The API validates and records the application, acknowledges receipt, and initiates risk scoring. |
| **Trigger** | The partner submits a loan application through the Loan Submission API. |
| **Preconditions** | 1. The partner has a valid legal contract with the loan company and valid API credentials provisioned after the agreement was signed.<br>2. The partner has collected the application information and supporting documents required for submission and risk scoring. |
| **Postconditions** | 1. The application is recorded and its receipt is acknowledged to the partner within 2 seconds.<br>2. Risk scoring is initiated and produces a recommendation category asynchronously.<br>3. No final approval or rejection decision is made by this use case. |

## Normal Flow

| Step | Actor | Action |
|---|---|---|
| 1 | Partner System | Prepares the customer, spouse (if applicable), collateral, loan, and risk-scoring information. |
| 2 | Partner System | Submits the application through the Loan Submission API. |
| 3 | Loan Submission API | Authenticates the partner using its API credentials. |
| 4 | Loan Submission API | Confirms that the authenticated partner is legally contracted to submit applications. |
| 5 | Loan Submission API | Validates mandatory customer data, age, and government-issued ID validity. |
| 6 | Loan Submission API | Validates the required spouse data when the customer is married. |
| 7 | Loan Submission API | Validates collateral and loan values against business rules and master data, including the ownership holder. |
| 8 | Loan Submission API | Confirms that the application location is within the defined coverage area. |
| 9 | Loan Submission API | Confirms that the license plate is not tied to an active loan. |
| 10 | Loan Submission API | Confirms that any resubmission satisfies the applicable waiting or settlement rule. |
| 11 | Loan Submission API | Records the application. |
| 12 | Loan Submission API | Initiates risk scoring without waiting for the risk result. |
| 13 | Loan Submission API | Acknowledges receipt to the partner within 2 seconds. |
| 14 | Risk Scoring Process | Applies the current proprietary scoring method and assigns a recommendation category. |

## Exceptions

| Exception | Condition | System Response |
|---|---|---|
| E-001: Partner Unauthorized | The credentials are invalid, or the authenticated partner does not have a valid legal contract. | Reject the submission and do not record the application. |
| E-002: Invalid or Incomplete Application Data | Required customer, spouse, collateral, loan, or risk-scoring data is missing or invalid, including an underage applicant or expired government-issued ID. | Reject the submission, identify the invalid or missing data, and do not record the application. |
| E-003: Value Not in Master Data | A required collateral, loan, or tenure value is outside the business-maintained master data. | Reject the submission and do not record the application. |
| E-004: Location Outside Coverage | The application originates from a location outside the defined coverage areas. | Reject the submission and do not record the application. |
| E-005: License Plate Has an Active Loan | The submitted license plate is currently tied to an active loan. | Reject the submission and do not record the application. |
| E-006: Rejected Application Resubmitted Too Soon | The partner resubmits the same rejected application before 30 days have elapsed. | Reject the submission and do not record the application. |
| E-007: Existing Loan Not Settled | An application is submitted for collateral whose previously approved loan has not been fully settled. | Reject the submission and do not record the application. |
| E-008: Invalid Vehicle Ownership Holder | The vehicle is owned by someone other than the customer or spouse. | Reject the submission and do not record the application. |

## Priority

High — partner loan submissions support the objective of sourcing 30,000 applications through external channels within 12 months and contribute to scaling partner operations.

## Business Rules

- Only legally contracted partners may submit applications.
- The system edge for loan submission is the partner system; customers do not submit directly and do not need a platform login or end-user account.
- Initial partner API credentials are provisioned only after the legal agreement is signed. Partners may rotate or regenerate credentials when needed.
- Partner onboarding and integration are supported through comprehensive self-service documentation and a sandbox, targeting partner readiness within 5 days with minimal staff assistance.
- The platform targets 20 active partners within 12 months without increasing operational headcount.
- Customer mandatory data includes a non-expired government-issued ID photo, national ID number, full legal name, selfie photo, phone number, gender, date and place of birth, marital status, occupation, work duration, monthly income, dependents, and domicile address. Email is optional.
- The customer must be at least 18 years old. If married, spouse national ID number, full legal name, date of birth, and phone number are required; spouse must be at least 18 years old. Spouse email and government-issued ID photo are optional.
- Accepted marital statuses are Single, Married, Divorced, and Widowed. Accepted occupations are Employee, Entrepreneur, and Civil Servant.
- Required car collateral data includes vehicle brand, model, license plate, ownership holder, and manufacturing year. These values must conform to business master data. The vehicle may be owned only by the customer or the customer's spouse; ownership by another party is not valid.
- Loan amount must be between 500 and 200,000, inclusive, and both the amount and tenure must be selected from business master data.
- The application location must be within a business-defined coverage area.
- A license plate is valid for submission only when it is not tied to an active loan.
- After rejection, the partner must wait 30 days before resubmitting the same application. After approval, the existing loan must be fully settled before a new application for the same collateral.
- Each application must undergo risk scoring. Required risk-scoring data includes customer government ID number, customer monthly household income, dependents, customer phone number, vehicle brand, model, manufacturing year, ownership holder, and the existence and validity (including expiry) of ownership/legal documents. Spouse ID and monthly household income are required when applicable; spouse phone is optional; passport number is optional.
- Risk scoring assigns one of VERY_LOW, LOW, HIGH, or VERY_HIGH. These categories are recommendations only and do not automatically approve or reject an application.
- Final approval or rejection is decided by a regional risk committee in a separate process, outside this use case.

## Assumptions

- The API can initiate risk scoring asynchronously while meeting the 2-second receipt-acknowledgement objective; the integration and operational behavior for scoring failures are not specified in the notes.
- Risk scoring requires spouse monthly household income when applicable and ownership/legal document validity data, but those fields are not explicitly included in the business stakeholder's mandatory submission fields. Their submission requirements need confirmation.

## Related Artifacts

| ID | Type | Description |
|----|------|-------------|
| [FLW-001](../flowcharts/FLW-001-submit-loan-application.md) | Flowchart | Happy-flow diagram for submitting an application and initiating risk scoring. |
| [OB-001](../objectives-and-boundaries/OB-001-loan-submission.md) | Objectives & Boundaries | Loan submission objectives and platform scope boundaries. |
| [Business stakeholder meeting notes](../meeting-notes/2027-01-02-meeting-notes-business-stakeholder.txt) | Meeting Notes | Submission, data, validation, resubmission, and partner requirements. |
| [Risk stakeholder meeting notes](../meeting-notes/2027-01-03-meeting-notes-risk-stakeholder.txt) | Meeting Notes | Risk-scoring categories, inputs, and decision boundaries. |
