# Business Requirements Document (BRD)

## TrustIntern — AI-Powered Internship Authenticity & Scam Intelligence Platform

### 1. Project Overview

TrustIntern is a software-based intelligent platform designed to help students identify potentially fraudulent internships and job opportunities. The platform analyzes internship advertisements, company information, recruiter identity, websites, documents, communication content, and potentially AI-generated media using OSINT and multiple AI agents.

The system provides an explainable risk score rather than simply declaring an opportunity genuine or fraudulent.

### 2. Problem Statement

Students increasingly receive internship opportunities through email, WhatsApp, LinkedIn, Telegram, social media, job portals, and college groups. Scammers can create convincing company profiles, offer letters, websites, recruiter identities, images, voice messages, and videos, making fraudulent opportunities difficult to identify.

Existing solutions often focus on only one aspect, such as URL checking or fake-job classification. There is a need for a unified system that combines multiple independent verification signals before assessing internship credibility.

### 3. Proposed Solution

Develop a Multi-Agent OSINT-based Internship Verification Platform that automatically investigates an internship opportunity and generates an explainable Trust/Risk Score.

**Core workflow:**

Internship Information → Data Extraction → Multi-Agent Investigation → Evidence Correlation → Risk Scoring → Explainable Verification Report

**Proposed agents:**
- Company Verification Agent
- Recruiter Verification Agent
- Website/Domain Agent
- OSINT Agent
- Document Analysis Agent
- Communication Analysis Agent
- AI-Content Analysis Agent

### 4. Objectives

1. Detect potentially fraudulent internship opportunities.
2. Verify the authenticity of companies and recruiters.
3. Perform automated OSINT-based investigation.
4. Identify suspicious websites, domains, and contact information.
5. Analyze internship documents for inconsistencies.
6. Identify potential AI-generated or manipulated media.
7. Combine evidence from multiple sources.
8. Generate an understandable risk score.
9. Provide evidence supporting the risk assessment.
10. Help students make safer decisions before sharing information or paying money.

### 5. Target Users

- Students
- Fresh Graduates
- Colleges
- Placement Cells
- Career Platforms
- Parents/Guardians

### 6. Functional Requirements

#### FR1 — Internship Submission
Users should be able to submit:
- Internship URL
- Company name
- Recruiter name
- Recruiter email
- Phone number
- Internship/job description
- Offer letter
- Images
- Audio/video
- Social-media profile

#### FR2 — Company Verification
The system should check:
- Company existence
- Official website
- Domain information
- Contact information
- Business identity
- Consistency between submitted and official information

#### FR3 — Recruiter Verification
The system should analyze:
- Recruiter identity
- Professional profile
- Email domain
- Organization association
- Publicly available information
- Identity inconsistencies

#### FR4 — OSINT Investigation
The system should gather publicly available evidence related to:
- Company
- Recruiter
- Website
- Phone number
- Email
- Internship advertisement
- Reported scam indicators

#### FR5 — Document Analysis
The system should analyze uploaded documents for:
- Suspicious wording
- Inconsistent company details
- Fake contact information
- Unusual payment requests
- Template anomalies
- Contradictory information

#### FR6 — AI-Generated Content Analysis
Where technically supported, the system should examine submitted images, audio, and video for indicators associated with synthetic or manipulated content. Results should be presented as indicators/probabilities, not absolute proof.

#### FR7 — Multi-Agent Evidence Fusion
Evidence collected by different agents should be combined into a unified assessment.

#### FR8 — Risk Scoring
The system should generate a score such as:
- 0–30: Low Risk
- 31–60: Needs Verification
- 61–100: High Risk

Exact thresholds should be calibrated using validation data.

#### FR9 — Explainable Report
The user should receive:
- Risk Score
- Evidence
- Suspicious Indicators
- Verification Recommendations

### 7. Non-Functional Requirements

**Security**
- Protect submitted user information.
- Encrypt sensitive data.
- Avoid unnecessary collection of personal information.

**Performance**
- Investigation should complete within a reasonable response time.
- Agents should operate concurrently where possible.

**Scalability**
- Architecture should support increasing numbers of users and investigations.

**Explainability**
- Every major risk conclusion should have supporting evidence.
- Verified evidence should be distinguished from AI-generated inference.

**Reliability**
- The system should distinguish between verified evidence and AI-generated inference.

### 8. Proposed Technology Stack

**Frontend**
- React / Next.js
- HTML/CSS/JavaScript

**Backend**
- Python
- FastAPI

**AI/ML**
- LLM
- NLP
- Classification models
- Anomaly detection
- Multimodal AI models

**Multi-Agent Framework**
- LangGraph or equivalent agent orchestration

**Data**
- PostgreSQL
- Vector database

**OSINT**
- Public web sources
- Domain information
- Public company information
- Search APIs where permitted

**Security**
- Authentication
- Encryption
- Secure API architecture

### 9. High-Level Architecture

```text
USER
  ↓
Internship Submission
  ↓
Data Extraction Layer
  ↓
┌─────────────────────────────────────┐
│        MULTI-AGENT ENGINE           │
│                                     │
│ Company Agent       Recruiter Agent │
│ OSINT Agent         Domain Agent    │
│ Document Agent      AI-Content Agent│
└──────────────────┬──────────────────┘
                   ↓
          Evidence Correlation
                   ↓
            Risk Scoring Engine
                   ↓
        Explainable Risk Report
                   ↓
          Verification Guidance
```

### 10. Key Innovation

The primary innovation is combining multi-agent OSINT investigation, identity verification, document analysis, and AI-generated-content analysis into an explainable internship trust assessment system.

Rather than attempting to determine whether a person or opportunity is definitively fraudulent, the system evaluates how strongly available evidence supports or contradicts the claimed identity and internship.

### 11. Expected Output

**Example Internship Verification Report**

- Company: XYZ Technologies
- Recruiter: ABC
- Risk Score: 78/100 — High Risk

**Potential indicators:**
- Recruiter identity could not be independently verified.
- Submitted email does not match the organization's verified domain.
- Internship requires an upfront payment.
- Website information is inconsistent.
- Multiple independent sources contain complaints.
- Submitted media shows potential synthetic-content indicators.

**Guidance:** Verify the organization through independent official channels before making payments or sharing sensitive personal documents.

### 12. Success Metrics

The project can be evaluated using:
- Scam detection accuracy
- False-positive rate
- False-negative rate
- Company verification accuracy
- Recruiter verification accuracy
- OSINT evidence retrieval accuracy
- AI-content classification performance
- Average investigation time
- Explainability/user understanding

A useful prototype benchmark is comparing manual verification time against AI-assisted verification time. Actual performance values should be established through testing rather than assumed beforehand.

### 13. Recommended Project Titles

**Full title:** TrustIntern: A Multi-Agent OSINT and Multimodal AI Framework for Internship Fraud Detection and Authenticity Verification

**Short title:** TrustIntern — AI-Powered Internship Scam Intelligence
