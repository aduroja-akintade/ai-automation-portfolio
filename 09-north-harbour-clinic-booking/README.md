# North Harbour Clinic | Appointment Booking & Reminder Automation

**Status:** Tested, fictional healthcare portfolio implementation. Not deployed for a real clinic.  
**Role:** Workflow architecture, n8n integration engineering, database reliability, testing and technical documentation.  
**Stack:** n8n, PostgreSQL, Google Calendar, Gmail, n8n Data Tables, JavaScript validation.  
**Scope:** Appointment request validation, conflict-safe reservations, confirmation/rejection emails, 24-hour and 2-hour reminders, duplicate-notification protection, recovery audit.

> **Healthcare demonstration disclaimer:** North Harbour Clinic is a fictional scenario. All public examples use synthetic data. This is not an operational patient-booking service, a medical device or a production healthcare deployment.

## Problem

Simple form-to-calendar automations can generate multiple bookings for the same slot when two requests arrive close together. Reminder jobs can also send duplicate messages after re-runs or partial failures. The project explores the reliability controls needed before such workflows could be considered for a real scheduling environment.

## Solution and architecture

The implementation separates the booking transaction, scheduled notifications and recovery diagnostics.

### 1. Booking workflow (17 nodes in the audited version)

```text
Appointment request form
    |
    v
Store / validate request (future date, weekday, clinic hours, Europe/Helsinki)
    |
    v
PostgreSQL atomic slot reservation + unique slot guard
    |
    v
Google Calendar availability check
    | available                   | unavailable
    v                             v
Create private demo event     Release provisional reservation
    |                             |
    v                             v
Update confirmed status       Mark rejected / unavailable
    |                             |
    +----------> Gmail confirmation or rejection
```

The **PostgreSQL reservation constraint**, not Google Calendar alone, is the concurrency safeguard. A half-hour slot is reserved atomically before creating a calendar event. Business rules reject past dates, weekends and requests outside configured clinic hours.

### 2. Reminder workflow (16 nodes in the audited version)

```text
Scheduled check (every 30 minutes)
    |
    v
Read confirmed appointments
    |
    +--> 24-hour reminder due? ---+
    |                             |
    +--> 2-hour reminder due? -----+--> PostgreSQL unique delivery claim
                                          |
                                          v
                                      Gmail send
                                          |
                                          v
                                Record accepted send + reminder flag
```

A unique delivery claim prevents the same reminder from being sent again on ordinary reruns. An uncertain delivery state is held for investigation rather than blindly retried.

### 3. Recovery audit

A manually invoked diagnostic workflow checks for unfinished slot reservations and notification records requiring review. This is an operational safeguard, not an automatic guarantee of successful recovery from every failure.

## Recorded test evidence

| Evidence | Observation | What it demonstrates |
|---|---|---|
| Executions **#400** and **#401** | One booking was accepted; a second request for the same slot was rejected | Basic collision handling |
| Concurrent requests **#409** and **#410** | Only one request acquired the reservation, with exactly one Google Calendar event observed | Atomic duplicate-slot protection under a tested contention scenario |
| Reminder execution **#404** | Due 24-hour and 2-hour reminders were sent | Scheduled notification paths |
| Reminder rerun **#406** | Duplicate claims were rejected after resetting test flags | Database-backed duplicate-notification suppression |
| Recovery audit **#408** | No open issues were found in the tested audit run | Audit query executed successfully |
| Later booking executions **#419** and **#420** | Successful booking and rejected same-slot request, plus Calendar verification, shown in an OBS walkthrough | Additional recorded regression evidence |

**Evidence scope:** These results document specific controlled tests, not a blanket guarantee of correctness across all failures, concurrent workloads or environments. In particular, injected Gmail/Calendar outage and partial-transaction cases were not exhaustively validated.

## Privacy and safety design

- Fictional identities and demonstration data, no real patient records published.
- Private Google Calendar events with non-identifying booking references.
- No passwords, credentials, tokens, internal n8n execution URLs or private calendar IDs exposed in the public case study.
- Reduced execution-data retention in the demonstration environment.
- Production use would require a healthcare-specific privacy, information-security, access-control, audit, failure-recovery and regulatory review.

## Current limitations and next engineering steps

1. Full fault-injection coverage for Google Calendar/Gmail timeout, rate-limit, retry and ambiguous send outcomes.
2. Authenticated public-facing intake, abuse controls and production monitoring/alerting.
3. Timezone and daylight-saving transition regression tests, plus load/concurrency tests.
4. Deployment runbooks, retention policies and assessment of any sensitive personal-data processing.
5. **Optional voice receptionist:** A separate voice/API concept was drafted. It has **not** been validated as a working production voice assistant and is not part of this verified demo.

## Watch the North Harbour Clinic demonstration

**[▶ Watch the 3:29 clinic engineering demonstration](https://github.com/aduroja-akintade/ai-automation-portfolio/blob/main/09-north-harbour-clinic-booking/north-harbour-clinic-demo.mp4)**

This edited, narrated clip uses authentic OBS recordings and shows the n8n booking workflow, tested acceptance/rejection paths and the Google Calendar verification. It was extracted from the original 8:26 combined Axyon portfolio video and optimized for web playback (15 fps, H.264/AAC). The longer source recording and high-quality version are preserved separately.

This is a **fictional/synthetic healthcare scenario**, not a client deployment or a production healthcare service. The demo does not cover every reminder and fault-injection test; those are described in the evidence table above.

Other original project demonstrations:

- [Finn's Realty AI Assistant, 4:58 narrated walkthrough](https://drive.google.com/file/d/1E4ATMOcDavrt4feBnmGlrTIrxZqJT2-d/view)
- [Cupid Errands email-routing walkthrough](https://www.tella.tv/video/ai-email-routing-system-dql7)

## Portfolio navigation

[AI Automation Portfolio index](../README.md) · [GitHub profile](https://github.com/aduroja-akintade)

*Prepared as a factual technical case study of a synthetic implementation, not a claim of client engagement or production clinical service.*
