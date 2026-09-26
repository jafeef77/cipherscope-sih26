# CIPHERSCOPE

### AI-Powered IPsec VPN Protocol Analyzer and Security Assessment Framework

**Smart India Hackathon 2026 — Problem Statement 26160**
**Team: Pride7Troopers**

---

## 1. Overview

CIPHERSCOPE is a software-based security assessment framework for analyzing **IPsec VPN communication and configuration** without requiring access to decrypted application payloads.

The project combines deterministic protocol analysis with encrypted-traffic metadata analysis to identify security configuration issues, characterize VPN traffic, and generate a structured security assessment.

The system is being developed as a **controlled prototype using reproducible IPsec test environments and packet captures**.

---

## 2. What CIPHERSCOPE Actually Analyzes

The project focuses on two complementary areas:

### Control Plane — IKE

The IKE analysis component examines information available during VPN negotiation, including:

* Security Association proposals
* Encryption algorithms
* Integrity algorithms
* Diffie-Hellman parameters
* PFS-related configuration
* IKE negotiation information

This provides deterministic information about the negotiated security configuration.

### Data Plane — ESP

ESP protects the application payload, so CIPHERSCOPE does not depend on decrypting the payload.

Instead, the project extracts observable flow-level characteristics such as:

* Packet sizes
* Packet counts
* Timing characteristics
* Flow duration
* Direction
* Traffic volume

These features are used for traffic characterization and experimental ML-based classification.

---

## 3. Analysis Pipeline

```text
             IPsec VPN Testbed
                    │
                    ▼
             Packet Capture
             PCAP / Live Flow
                    │
                    ▼
             Feature Extraction
              ┌─────┴─────┐
              ▼           ▼
          IKE Parser   ESP Metadata
              │           │
              └─────┬─────┘
                    ▼
              Analysis Engine
              ┌─────┴─────┐
              ▼           ▼
        Rule Evaluation   ML Analysis
              │           │
              └─────┬─────┘
                    ▼
             Security Assessment
                    │
                    ▼
              Dashboard / Report
```

---

## 4. Design Approach

CIPHERSCOPE intentionally separates **deterministic security analysis** from **probabilistic traffic analysis**.

### Deterministic Layer

Used where protocol information can be directly extracted and validated.

Examples:

* IKE parameters
* Security Association information
* Cryptographic configuration
* Standards-based checks

### ML-Assisted Layer

Used where the information is inferred from encrypted traffic characteristics.

Examples:

* Traffic class estimation
* Flow characterization
* Metadata-based classification

This separation prevents an ML prediction from being treated as a direct protocol fact.

---

## 5. Prototype Scope

The first prototype focuses on a controlled environment rather than attempting to support every commercial VPN implementation.

### Current development scope

* IKEv2-based IPsec
* strongSwan / Libreswan test environment
* PCAP-based analysis
* ESP metadata extraction
* Rule-based security assessment
* Experimental ML pipeline
* Web-based visualization

### Out of scope for the initial prototype

* Decrypting application payloads
* Full support for every VPN vendor
* Replacing an enterprise VPN monitoring platform
* Claiming attack detection solely from encrypted traffic statistics

---

## 6. Experimental Test Environment

To make the analysis reproducible, the project uses a controlled IPsec testbed.

```text
┌─────────────┐       IPsec        ┌─────────────┐
│    Client   │════════════════════│   Gateway   │
│             │                    │ strongSwan  │
└─────────────┘                    └──────┬──────┘
                                          │
                                   Packet Capture
                                          │
                                          ▼
                                    CIPHERSCOPE
```

Traffic can be generated under controlled conditions and captured for analysis.

This allows different VPN configurations and traffic patterns to be tested without relying entirely on uncontrolled internet traffic.

---

## 7. Dataset Strategy

A major development challenge is the absence of a single public dataset covering all combinations of IPsec configuration and encrypted traffic behaviour required by this project.

Therefore, the prototype explores **controlled dataset generation**.

Example dimensions include:

```text
VPN Configuration
      │
      ├── Cipher
      ├── Integrity
      ├── DH Group
      ├── PFS
      └── Tunnel Parameters
             │
             ▼
       Controlled Traffic
             │
             ├── Web
             ├── VoIP
             ├── Video
             └── Other flows
             │
             ▼
       Extracted Features
             │
             ▼
       Labelled Dataset
```

The generated dataset will be evaluated before being used for model performance claims.

---

## 8. Security Assessment Model

The security assessment is designed around two sources of evidence:

**Protocol evidence**

→ Extracted directly from IKE/security parameters.

**Traffic evidence**

→ Derived from ESP flow metadata.

The final assessment keeps these evidence types distinguishable rather than combining them into a single unexplained prediction.

---

## 9. Validation Strategy

The prototype will be evaluated using controlled configurations rather than relying only on screenshots or demonstration values.

Validation will include:

* Known IPsec configurations
* Controlled cryptographic variations
* Multiple traffic patterns
* PCAP replay
* Parser output verification
* Rule-engine verification
* ML evaluation using held-out data

For ML experiments, metrics such as:

* Accuracy
* Precision
* Recall
* F1-score
* Confusion matrix

will be reported only after actual testing.

---

## 10. Technology Stack

| Layer           | Technologies                   |
| --------------- | ------------------------------ |
| VPN Testbed     | strongSwan, Libreswan, Docker  |
| Packet Capture  | tcpdump, tshark                |
| Packet Analysis | Scapy, PyShark                 |
| Backend         | Python, FastAPI                |
| ML              | scikit-learn, XGBoost, PyTorch |
| Frontend        | Web-based dashboard            |
| Reporting       | ReportLab                      |

---

## 11. Repository Structure

```text
CIPHERSCOPE/
│
├── backend/
│   ├── ingestion/
│   ├── parser/
│   ├── features/
│   ├── rules/
│   └── risk/
│
├── ml/
│   ├── preprocessing/
│   ├── dataset/
│   └── models/
│
├── testbed/
│   └── docker/
│
├── samples/
│   └── pcaps/
│
├── dashboard/
│
├── reports/
│
├── docs/
│   ├── architecture/
│   └── screenshots/
│
├── requirements.txt
└── README.md
```

---

## 12. Development Status

| Component              | Status                      |
| ---------------------- | --------------------------- |
| System Architecture    | Defined                     |
| Dashboard Prototype    | Available                   |
| IPsec Testbed          | Under Development           |
| Packet Ingestion       | Under Development           |
| IKEv2 Analysis         | Under Development           |
| ESP Feature Extraction | Under Development           |
| Security Rules         | Under Development           |
| Dataset Generation     | Planned / Under Development |
| ML Classification      | Experimental                |
| Risk Assessment        | Under Development           |
| Report Generation      | Planned                     |

The repository will be updated as individual components become reproducible and testable.

---

## 13. Current Limitations

CIPHERSCOPE is a research prototype and has several known limitations.

### Encrypted Traffic

Packet size and timing features can provide useful information, but they do not expose the actual encrypted application content.

### Network Variability

Latency, congestion and routing conditions can change traffic characteristics and affect classification.

### Vendor Differences

Different IPsec implementations may expose vendor-specific behaviour. Initial development therefore focuses on a controlled test environment.

### Dataset Availability

The quality of ML results depends on the diversity and correctness of the generated and collected training data.

---

## 14. Standards Considered

The security analysis is aligned with:

* **RFC 7296** — Internet Key Exchange Protocol Version 2
* **RFC 4301** — Security Architecture for the Internet Protocol
* **RFC 4303** — IP Encapsulating Security Payload
* **NIST SP 800-77 Rev. 1** — Guide to IPsec VPNs

---

## 15. Project Objective

The objective is not simply to classify encrypted traffic.

CIPHERSCOPE aims to provide a practical workflow where:

```text
VPN Configuration
       +
Encrypted Traffic Metadata
       +
Security Rules
       +
ML-Assisted Analysis
       ↓
Structured Security Assessment
```

This provides the foundation for investigating IPsec VPN security through both **protocol-level evidence and encrypted-traffic behaviour**.

---

## 16. Prototype Demonstration

The demonstration will follow a reproducible sequence:

```text
1. Start IPsec testbed
        ↓
2. Establish VPN tunnel
        ↓
3. Generate controlled traffic
        ↓
4. Capture packets
        ↓
5. Extract IKE / ESP features
        ↓
6. Run security analysis
        ↓
7. Display findings
        ↓
8. Generate assessment output
```

The repository will contain sample inputs and outputs as they become available.

---

## 17. Team

### Pride7Troopers

**Smart India Hackathon 2026**

**Problem Statement ID:** 26160

---

## 18. Project Status

> **CIPHERSCOPE — Prototype Development**

This repository documents the ongoing development of the proposed framework. Experimental results and performance metrics will be added after reproducible testing.

---
