# Incident Response Procedure

## Purpose

This document defines the incident response procedure, incident management workflow, incident severity classification, communication protocols, and escalation procedures for handling incidents under the Service Level Agreement (SLA).

## Contents

1. [Overview](#1-overview)
   - [Scope](#scope)
2. [Incident Management Procedure](#2-incident-management-procedure)
   - [Incident Detection](#21-incident-detection)
   - [Incident Logging](#22-incident-logging)
   - [Incident Analysis & Diagnosis](#23-incident-analysis--diagnosis)
   - [Incident Resolution & Recovery](#24-incident-resolution--recovery)
   - [Incident Closure](#25-incident-closure)
   - [Incident Automation Example Setup](#26-incident-automation-example-setup)
3. [Incident Severity Classification](#3-incident-severity-classification)
   - [Response Time Requirement](#31-response-time-requirement)
4. [Communication Protocols](#4-communication-protocols)
   - [Internal Communication](#41-internal-communication)
   - [Customer Communication](#42-customer-communication)
5. [Escalation Procedure](#5-escalation-procedure)
6. [Post-Incident Review](#6-post-incident-review)
7. [Conclusion](#7-conclusion)

## 1. Overview

This document defines the incident response procedure, incident management workflow, incident severity classification, communication protocols, and escalation procedures for handling incidents under the Service Level Agreement (SLA).

### Scope

- **SLA L1**: Initial incident triage and response.
- **SLA L2**: Managed by the SLA team.
- **SLA L3**: Handled by the development on-call team.

## 2. Incident Management Procedure

### 2.1 Incident Detection

Incidents can be detected via:
- Automated monitoring tools and alerts.
- Customer reports through the support portal.
- Internal system checks by operations teams.

### 2.2 Incident Logging

All incidents must be logged in the incident management system, including:
- Date and time of detection.
- Reporter details.
- System or service affected.
- Description of the issue.
- Initial severity classification.

### 2.3 Incident Analysis & Diagnosis

- The L1 team performs initial triage, gathers logs, and assesses impact.
- If resolution is not possible at L1, the issue is escalated to L2.
- The SLA team at L2 further investigates and attempts resolution.
- If the issue requires code changes or deeper debugging, L3 (development on-call team) is engaged.

### 2.4 Incident Resolution & Recovery

- Once the root cause is identified, corrective actions are taken.
- Temporary workarounds may be provided while working on a permanent fix.
- The resolution is tested and verified before closing the incident.

### 2.5 Incident Closure

- The resolution details are documented.
- The customer or affected parties are notified.
- A post-mortem may be conducted for major incidents.

### 2.6 Incident Automation Example Setup

- When an incident is detected, **PagerDuty** automatically creates an incident.
- This triggers the creation of a **Jira ticket on the SLA team's board**.
- The SLA team assesses the issue and, if necessary, escalates it by creating an **L3 support ticket on the development team’s board**.
- Both Jira tickets are tracked until resolution.
- When the incident is resolved, PagerDuty is updated, and the ticket status in Jira is changed accordingly.

## 3. Incident Severity Classification

| Severity Level | Description | Response Time | Handling Team |
| -------------- | ----------- | ------------- | ------------- |
| **Critical (P1)** | Service outage, security breach, or severe performance degradation affecting all users. | 30 minutes | L3 (Development On-Call) |
| **High (P2)** | Major functionality is impacted with no workaround available. | 30 minutes | L3 (Development On-Call) |
| **Medium (P3)** | Partial service degradation, performance issues, or minor functionality breakage. | 30 minutes | L2 (SLA Team) |
| **Low (P4)** | Cosmetic issues, minor bugs, or general inquiries. | 30 minutes | L1 Support |

### 3.1 Response Time Requirement

All incidents, regardless of severity, require an initial response within **30 minutes** of detection.

## 4. Communication Protocols

### 4.1 Internal Communication

- **Slack / Teams Channels** for real-time coordination.
- **Incident Management System** for logging and tracking incidents.
- **Email Notifications** for status updates.
- **Phone communication** if key person contacts are provided
- **Additional lines of communication** Based on project specific setup

### 4.2 Customer Communication

- Updates provided based on SLA commitments.
- Email or service status page used for major outages.
- RCA (Root Cause Analysis) reports shared for P1 and P2 incidents.

## 5. Escalation Procedure

1. **L1 Triage:** Determines if the issue can be resolved or needs escalation.
2. **Escalation to L2:** If L1 cannot resolve the issue within the given response time, L2 takes over.
3. **Escalation to L3:** If L2 determines that code changes or in-depth debugging is needed, the incident is escalated to L3 (development on-call team).
4. **Management Notification:** If the incident remains unresolved beyond SLA limits, management is notified for further escalation.

## 6. Post-Incident Review

- Major incidents (P1/P2) require a post-mortem.
- Root causes and corrective actions are documented.
- Preventive measures are identified and implemented.

