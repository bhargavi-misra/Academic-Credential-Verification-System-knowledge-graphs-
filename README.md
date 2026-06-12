# Academic Credential Verification System using LLMs and Knowledge Graphs

## Overview

The Academic Credential Verification System is an AI-powered solution that verifies academic credentials by combining Large Language Models (LLMs) and Knowledge Graphs.

The system extracts information from academic certificates in PDF format, converts unstructured text into structured data using Google's Gemini LLM, and verifies the extracted credentials against a Neo4j Knowledge Graph representing institutional records.

This project demonstrates how LLMs and graph databases can be integrated to automate document verification and generate explainable verification reports.

---

## Problem Statement

Academic credential verification is often a manual and time-consuming process. Institutions and organizations must verify information such as:

* Student Name
* University Name
* Degree
* Graduation Year

Traditional verification methods require human intervention and are prone to delays.

This project automates the verification process using AI and graph-based reasoning.

---

## Objectives

* Extract academic information from PDF certificates.
* Convert unstructured certificate text into structured entities.
* Store and manage academic records using a Knowledge Graph.
* Verify extracted credentials against graph-based institutional records.
* Generate verification reports and explanations.

---

## System Architecture

```text
PDF Certificate
       │
       ▼
PyMuPDF Text Extraction
       │
       ▼
Gemini LLM
       │
       ▼
Structured JSON
       │
       ▼
Neo4j AuraDB Knowledge Graph
       │
       ▼
Credential Verification Engine
       │
       ▼
Verification Report
       │
       ▼
AI-Generated Explanation
```

---

## Technologies Used

### Programming Language

* Python

### Large Language Model

* Google Gemini

### Graph Database

* Neo4j AuraDB

### PDF Processing

* PyMuPDF

### Development Environment

* Google Colab

### Libraries

* neo4j
* google-generativeai
* pymupdf
* json
* getpass

---

## Knowledge Graph Design

### Nodes

* Student
* University
* Degree
* Year

### Relationships

* STUDIED_AT
* HAS_DEGREE
* GRADUATED_IN

Example:

```text
Bhargavi
   │
STUDIED_AT
   │
Manipal University Jaipur

Bhargavi
   │
HAS_DEGREE
   │
B.Tech CSE

Bhargavi
   │
GRADUATED_IN
   │
2028
```

---

## Workflow

### Step 1: Upload Certificate

The user uploads an academic certificate in PDF format.

### Step 2: Extract Text

PyMuPDF extracts textual content from the uploaded PDF.

### Step 3: Information Extraction

Gemini extracts:

* Student Name
* University
* Degree
* Graduation Year

and returns structured JSON.

Example:

```json
{
  "name": "Bhargavi",
  "university": "Manipal University Jaipur",
  "degree": "B.Tech CSE",
  "year": 2028
}
```

### Step 4: Knowledge Graph Verification

The extracted information is verified against records stored in Neo4j AuraDB.

### Step 5: Report Generation

The system generates:

* Verification Status
* Failure Reasons (if any)
* Explanation

---

### Verified Credential

```text
Academic Credential Verification Report

Student: Bhargavi
University: Manipal University Jaipur
Degree: B.Tech CSE
Year: 2028

Status: VERIFIED
Reason: All fields matched
```

### Unverified Credential

```text
Academic Credential Verification Report

Student: Bhargavi
University: Manipal University Jaipur
Degree: B.Tech CSE
Year: 2027

Status: NOT VERIFIED
Reason: Year mismatch
```

---

## Features

* PDF certificate processing
* LLM-based entity extraction
* Knowledge graph-based verification
* Detailed verification reports
* Explainable AI responses
* Secure runtime credential handling

---

## Project Outcome

This project successfully demonstrates how Large Language Models and Knowledge Graphs can be combined to create an intelligent academic credential verification system capable of extracting, validating, and explaining academic records automatically.
