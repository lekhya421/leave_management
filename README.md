# Leave Management App (Salesforce LWC + Apex)

Leave Management is a Salesforce app built with Lightning Web Components (LWC), Apex, and custom objects.
It lets employees submit leave requests, track balances/history, and lets managers approve or reject pending requests.

## Features

- Employee leave application form with validation
- Leave history dashboard with withdraw action
- Leave balance dashboard by leave type
- Manager approval panel for pending requests
- Apex business rules for balances and status transitions
- Trigger-based defaulting and day calculation on leave requests
- Unit test class for Apex controller logic

## Tech Stack

- Salesforce DX project structure
- Lightning Web Components (LWC)
- Apex classes and trigger handler
- Custom objects:
	- `Leave_Request__c`
	- `Leave_Balance__c`
- Jest for LWC unit tests (`@salesforce/sfdx-lwc-jest`)
- ESLint + Prettier + Husky

## Project Structure

```text
force-app/main/default/
	classes/
		LeaveManagementController.cls
		LeaveRequestTriggerHandler.cls
		LeaveManagementControllerTest.cls
	triggers/
		LeaveRequestTrigger.trigger
	lwc/
		leaveManagementApp/
		leaveApplicationForm/
		leaveHistoryDashboard/
		managerApprovalPanel/
	objects/
		Leave_Request__c/
		Leave_Balance__c/
```

## Core Components

- `leaveManagementApp`: parent shell component that coordinates child component refresh after new requests
- `leaveApplicationForm`: employee-facing form to submit leave
- `leaveHistoryDashboard`: shows employee requests and balances, supports withdrawal
- `managerApprovalPanel`: shows manager pending approvals and action buttons

## Backend Logic

### Apex Controller

`LeaveManagementController.cls` handles:

- Fetching employee balances and requests
- Submitting leave applications
- Fetching manager pending approvals
- Approving/rejecting requests with balance updates
- Withdrawing requests with balance restoration for approved leave
- Sending manager email notifications on new requests

### Trigger

`LeaveRequestTrigger.trigger` + `LeaveRequestTriggerHandler.cls` handle:

- Auto-calculating `Number_of_Days__c` from start/end dates
- Defaulting employee and status on insert
- Auto-populating manager from employee record

## Prerequisites

- Node.js 18+
- Salesforce CLI (`sf`)
- A Salesforce org (scratch org, sandbox, or dev org)
- Salesforce VS Code extensions (recommended)

## Setup

1. Install dependencies:

```bash
npm install
```

2. Authorize or create an org:

```bash
sf org login web -a LeaveMgmtDev
```

3. Set default org:

```bash
sf config set target-org=LeaveMgmtDev
```

4. Deploy metadata:

```bash
sf project deploy start
```

## Run Tests and Quality Checks

- Lint:

```bash
npm run lint
```

- LWC unit tests:

```bash
npm run test:unit
```

- LWC test coverage:

```bash
npm run test:unit:coverage
```

- Prettier check:

```bash
npm run prettier:verify
```

## Run Apex Tests

```bash
sf apex run test --tests LeaveManagementControllerTest --result-format human --wait 10
```

## Usage Flow

1. Employee submits a leave request from `leaveApplicationForm`.
2. Request is created with `Pending` status.
3. Manager sees it in `managerApprovalPanel`.
4. On approval/rejection, status updates and balances are adjusted.
5. Employee can review requests and balances in `leaveHistoryDashboard`.

## Notes and Assumptions

- Default paid leave allocation is currently 20 days per leave type.
- `Unpaid` leave does not consume balance.
- Manager notifications rely on manager email being populated.
- Leave day calculation currently counts calendar days (inclusive).

## Useful Links

- Salesforce DX: https://developer.salesforce.com/tools/vscode/
- Salesforce CLI: https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/
- LWC Developer Guide: https://developer.salesforce.com/docs/component-library/documentation/en/lwc
