# CareLink: Seizure Alert and Family Escalation Platform

<p align="center">
  <img src="https://img.shields.io/badge/Status-completed-brightgreen" />
  <img src="https://img.shields.io/badge/Type-academic%20project-blue" />
  <img src="https://img.shields.io/badge/Domain-Digital%20Health-purple" />
  <img src="https://img.shields.io/badge/Course-HCI%20544%20%40%20ASU-orange" />
  <img src="https://img.shields.io/badge/Stack-AWS_%7C_Apple_Watch_%7C_FHIR_%7C_Twilio-darkblue" />
</p>

---

Hi! 👋

This repository contains a complete **Digital Transformation Strategy** case study for **CareLink**, a real-time seizure detection and family escalation platform designed for vulnerable adolescents living away from home.

The project was created as part of **HCI 544: IT for Healthcare Innovation** at Arizona State University (Edson College of Nursing and Health Innovation) and covers problem framing, digital health solution design, wearable and cloud integration, IT governance, stakeholder analysis, risk management, KPI design, and a change management and implementation roadmap.

---

## TL;DR

Adolescents with epilepsy who live away from home face a critical safety gap: over 70% of seizures receive delayed first aid, and living alone increases the risk of SUDEP (Sudden Unexpected Death in Epilepsy) fivefold. **CareLink** is a cloud-based SaaS platform concept that connects Apple Watch seizure detection to a real-time, role-based escalation chain, alerting family first, nearby peers second, and clinicians last, with offline peer-to-peer fallback for low-connectivity environments.

---

## Table of Contents

- [Problem Statement](#-problem-statement)
- [Solution Overview](#-solution-overview)
- [User Persona](#-user-persona)
- [Architecture](#-architecture)
- [IT Asset List](#-it-asset-list)
- [Value Framework](#-value-framework)
- [IT Governance](#-it-governance)
- [KPIs and Performance Management](#-kpis-and-performance-management)
- [Risk Management](#-risk-management)
- [Stakeholder and Change Management](#-stakeholder-and-change-management)
- [Documents in This Repo](#-documents-in-this-repo)
- [References](#-references)

---

## Problem Statement

> *"Adolescents with epilepsy may have seizures in away-from-home settings where trained responders or clear protocols are not present, delaying first aid, increasing injury risk, and limiting timely communication to families and clinicians."*

**Key statistics driving urgency:**

| Metric | Figure | Source |
|---|---|---|
| People with epilepsy worldwide | 50 million+ | WHO, 2023 |
| U.S. children/adolescents with annual seizures | ~336,000 | Zack and Kobau, 2017 |
| Cases where first aid is delayed more than 2 min | over 70% | Literature |
| Increased SUDEP risk from living alone | 5x | Sveinsson et al., 2020 |
| SUDEP cases preventable if not unattended | ~69% | Sveinsson et al., 2020 |
| Annual U.S. epilepsy-related ED visits | 1 million+ | CDC, 2020 |
| Annual cost of epilepsy in the U.S. | $15.5 billion+ | CDC, 2024 |

**How Might We (HMW):** How might we use information technology to reliably recognize and escalate possible seizure events for adolescents in away-from-home settings so nearby designated adults can respond quickly, and families and clinicians receive timely, consent-based updates, while protecting privacy and minimizing false alarms and stigma?

---

## Solution Overview

**CareLink** is a cloud-based SaaS platform integrating wearable detection with multi-party escalation workflows.

### Core Flow

```
Apple Watch (Motion + Heart Rate)
        |
        v  [Multi-sensor fusion + personalized thresholds]
  Seizure Detected
        |
        |--- Online ---> Encrypted HTTPS ---> AWS API Gateway ---> Business Logic
        |                                           |
        |                              +-----------+-----------+
        |                              v           v           v
        |                          Family    Hostel Staff  Clinician/EHR
        |                       (SMS/Push)  (SMS/Push)   (FHIR, optional)
        |
        +--- Offline ---> Local Device Alarm + P2P Alert to nearby staff phones
                          (Store-and-forward when connectivity returns)
```

### Key Differentiators vs. Competitors

| Feature | Embrace2 | Apple Watch (EpiWatch) | NightWatch | **CareLink** |
|---|:---:|:---:|:---:|:---:|
| Multi-party role-based escalation | No | No | No | Yes |
| Offline P2P fallback | No | No | No | Yes |
| Away-from-home / hostel support | No | No | No | Yes |
| EHR/FHIR integration | No | Limited | No | Yes |
| SaaS per-patient licensing model | No | No | No | Yes |
| Consent-based family-first alerting | No | Limited | No | Yes |

---

## User Persona

<p align="center">
  <img
    src="https://github.com/AppyDange/carelink-case-study/blob/main/HCI%20544%20User%20Persona%20CareLink.png?raw=true"
    alt="CareLink User Persona"
    width="100%"
  />
</p>

---

## Report:

<p align="center">
  <img
    src="https://github.com/AppyDange/carelink-case-study/blob/main/Screen%20Recording%202025-11-21%20at%209.42.30%20PM.gif?raw=true"
    alt="CareLink Digital Transformation Strategy walkthrough"
    width="60%"
  />
</p>

---

## Architecture

CareLink follows a **cloud-native SaaS architecture** deployed on AWS, built for HIPAA compliance, high availability, and offline resilience.

### Components

**On-Device (Apple Watch)**
- Motion sensor (accelerometer/gyroscope) and heart-rate sensor
- Multi-sensor fusion algorithm with personalized detection thresholds
- 5 to 30 second cancel window to reduce false alarms
- Local haptic alert and loud on-device alarm in offline mode
- Store-and-forward queue for low-connectivity environments

**iPhone Gateway App**
- BLE link to Apple Watch
- Adds GPS and device battery context
- End-to-end encryption (TLS 1.3 in transit)
- Peer-to-peer Multipeer Connectivity alerts to nearby staff phones
- Sends encrypted HTTPS alerts to cloud backend

**Cloud Backend (AWS)**
- API Gateway to Lambda (Business Logic)
- AWS SNS/SES for fan-out alerts (push/SMS/email) to family, staff, and clinicians
- Twilio for voice call escalation
- KMS for encryption key management (AES-256 at rest)
- RBAC for role-based access control
- Audit Trail for full logging of all PHI access
- AWS HealthLake (FHIR) for consent-based EHR data sharing

**Secure Web Portal**
- React frontend with OAuth2/JWT authentication
- Read-only event timeline and seizure logs
- Report export with clinician/EHR consent controls
- RBAC-enforced with full audit logging

---

## IT Asset List

### Applications

| Application | Purpose | Stack / Vendor |
|---|---|---|
| Apple Watch App | On-device seizure detection; multi-sensor fusion; local alarm | watchOS, Swift, CoreMotion, HealthKit |
| iPhone Gateway App | BLE bridge; encrypt and forward; P2P offline alerts | iOS, Swift, APNs, Multipeer Connectivity |
| Cloud Alert and Escalation Service | Ingest events; consent lookup; fan-out alerts with retries and ACKs | AWS: API Gateway, Lambda, SNS/SES |
| Secure Web Portal | Read-only logs; clinician/EHR export with consent; RBAC | React, OAuth2/JWT |
| Communications Provider | SMS, voice, and email delivery | Twilio and AWS SNS/SES |
| EHR/FHIR Integration | Consent-based data share to clinician/EHR | AWS HealthLake, FHIR endpoints |

### Infrastructure

| Asset | Purpose | Owner |
|---|---|---|
| Apple Watch | Sensor, detection, and local alarm | Patient |
| iPhone | Gateway; HTTPS; store-and-forward; P2P fallback | Patient |
| Staff Smartphones | Receive P2P alerts and acknowledge | Staff |
| Cloud Services (AWS) | Alert orchestration, RBAC, encryption, and audit | Service provider |
| Portal Devices (desktop/iPad) | View logs and reports | Guardians and Clinicians |
| MDM (Jamf/Intune) | Enroll and manage staff devices; OS posture; lost-mode | IT |

---

## Value Framework

CareLink delivers value across the **IHI Quadruple Aim**:

| Aim | CareLink Impact |
|---|---|
| Improve Population Health | Faster seizure intervention reduces injuries, hospitalizations, and SUDEP risk |
| Enhance Patient/Family Experience | Real-time alerts and peace of mind for families; reduces anxiety |
| Reduce Per Capita Cost | Prevents costly ED visits; uncontrolled epilepsy costs 2x vs. stable patients |
| Improve Clinician Well-being | Structured EHR-integrated data replaces fragmented manual reports |

**Business model innovation:** Per-patient SaaS licensing with EHR/EMS integrations transforms a one-time wearable sale into a recurring, data-driven safety service.

---

## IT Governance

CareLink follows a **COBIT 2019-aligned governance model** integrated with HIPAA Security Rule and NIST Cybersecurity Framework standards.

**Governance structure (EDM: Evaluate, Direct, Monitor):**
- Governance Board: Product Manager, Chief Compliance Officer, and CISO
- All releases pass three stages: feasibility approval, compliance validation, and post-deployment evaluation

**Five governance pillars:**

1. **Policy and Compliance Oversight:** HIPAA/HITECH/FDA alignment; Business Associate Agreements (BAAs) with all data partners
2. **Security and Access Management:** MFA, RBAC, AES-256 at rest, TLS 1.3 in transit; NIST-aligned quarterly vulnerability scans
3. **Data Governance and Quality Control:** Dedicated Data Steward; HL7 FHIR interoperability; audit log integrity
4. **Change and Incident Management:** Jira-tracked change control; formal Incident Response Plan (IRP) with defined escalation paths
5. **Performance and Accountability:** Governance dashboards; monthly board reviews; target of 99.9% or higher uptime SLA

---

## KPIs and Performance Management

| KPI | Measurement | Target |
|---|---|---|
| Seizure Detection Accuracy | Correctly detected events / total confirmed seizures; clinician trust survey | 95% or higher accuracy; 0.1 or fewer false alarms per day within 6 months |
| Average Alert Response Time | Time in seconds from detection to first human acknowledgment | Median 2 minutes or under within 6 months |
| User Adoption and Engagement | Percentage of registered users wearing device daily and acknowledging alerts | 85% or higher daily wearable usage; 90% or higher satisfaction within 3 months |

---

## Risk Management

| Risk Area | Mitigation Strategy |
|---|---|
| Data Privacy / HIPAA Compliance | AES-256 at rest, TLS 1.3 in transit; BAAs with all partners; quarterly HIPAA audits |
| AI Detection Accuracy / False Alarms | Continuous model validation; diverse training data; human-in-the-loop review; quarterly retraining |
| System Downtime / Cloud Outage | Multi-region AWS with automated failover; 99.9% uptime SLA; Twilio SMS and P2P offline fallback |
| EHR Interoperability Failure | HL7 FHIR data exchange; sandbox API testing before go-live; CI/CD latency monitoring |
| User Non-Adherence / Low Adoption | In-app reminders; adherence metric dashboards; family notifications for inactive devices; monthly surveys |
| Security Breaches / Cyberattacks | Network firewalls, IDS, and endpoint protection; monthly vulnerability scans; annual pen tests; 24/7 monitoring |
| Governance Drift | Quarterly governance reviews; centralized risk register; continuous audit model per Jenkins (2021) |

---

## Stakeholder and Change Management

### Stakeholder Map

| Stakeholder | Primary Incentive | Key Risk / Resistance |
|---|---|---|
| Patients and Families | Faster emergency response and peace of mind | Data privacy concerns; loss of control over health information |
| Clinicians / Neurologists | Real-time seizure data; reduced manual reporting | Alert fatigue; accuracy doubts; clinical autonomy |
| Hostel Administration / Responders | Clear protocols and compliance documentation | Fear of false alarms; unfamiliar workflows |
| Hospital IT / Informatics | EHR automation and data accuracy | Integration conflicts; external API security risk |
| Compliance and Legal Teams | Proactive HIPAA compliance; reduced audit risk | Complex ongoing oversight burden |

### Change Management Approach

CareLink applies both **Prosci ADKAR** and **Kotter's 8-Step** models across the rollout:

1. **Awareness:** Webinars for clinicians, hostel staff, and families explaining benefits and privacy protections
2. **Knowledge and Training:** Simulation sessions and digital user guides for alert workflows
3. **Ability:** Pilot in one hostel and affiliated clinic before scaling
4. **Reinforcement:** Monthly surveys, focus groups, and open feedback forums
5. **Transparency:** Public privacy policies, consent dashboards, and data-sharing controls

---

## Documents in This Repo

| File | Description |
|---|---|
| [`CareLink Digital Transformation Strategy.pdf`](https://github.com/AppyDange/carelink-case-study/blob/main/CareLink%20Digital%20Transformation%20Strategy.docx.pdf) | Full digital transformation strategy covering SWOT, IT governance, KPIs, risk management, stakeholder analysis, and companion strategies (HCI 544) |


---

## Vision and Mission

**Vision:** Every individual with epilepsy living away from direct supervision is safeguarded by trusted, real-time seizure monitoring and rapid response technology.

**Mission:** To empower families, caregivers, and clinicians with reliable, consent-based monitoring and escalation tools that reduce seizure-related risks, improve care coordination, and provide peace of mind.

---

## References

Full citations are available in the documents above. Key sources include:

- WHO. (2023). *Epilepsy: Key facts.* https://www.who.int/news-room/fact-sheets/detail/epilepsy
- CDC. (2024). *Epilepsy fast facts.* https://www.cdc.gov/epilepsy/
- Shah, S., et al. (2024). Apple Watch seizure monitoring study. *Epilepsy and Behavior, 158*, 109908.
- Sveinsson, O., et al. (2020). SUDEP incidence cohort study. *Neurology, 94*(4), e419-e427.
- Regalia, G., et al. (2019). Empatica Embrace2 detection validation. *Epilepsy Research, 153*, 79-82.
- ISACA. (2019). *COBIT 2019 framework.*
- NIST. (2018). *Cybersecurity Framework v1.1.*
- Glaser, J. (2021). *Healthcare IT governance and strategy.* Healthcare Management Press.

---

## Author

**Apurva Annasaheb Dange**
Arizona State University, HCI 544: IT for Healthcare Innovation and Digital Transformation Strategy
> Edson College of Nursing and Health Innovation

---

*CareLink is an academic product design and IT strategy case study developed for coursework at Arizona State University. It has not been commercially deployed.*
