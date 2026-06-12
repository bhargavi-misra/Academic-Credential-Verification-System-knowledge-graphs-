# Academic Credential Verifier

A notebook-based pipeline that verifies student academic credentials against a Neo4j graph database. Certificates are uploaded as PDFs, parsed using PyMuPDF, and structured using Gemini AI — the extracted fields are then matched against the database and a verification report is generated with an AI-written explanation.

---

## Why a Graph Database

Academic credentials are inherently relational — a student is connected to a university, a degree, and a graduation year. A graph database models these relationships directly as nodes and edges, making traversal queries natural and efficient. Neo4j was chosen over a relational database because credential verification is fundamentally a graph traversal problem, not a row lookup.

```
(Student)-[:STUDIED_AT]->(University)
(Student)-[:HAS_DEGREE]->(Degree)
(Student)-[:GRADUATED_IN]->(Year)
```

---

## Tech Stack

| Tool | Purpose |
|---|---|
| Google Gemini 2.5 Flash | PDF text → structured JSON extraction + result explanation |
| Neo4j Aura | Graph database storing student credential records |
| PyMuPDF (fitz) | PDF parsing and text extraction |
| Python (Google Colab) | Orchestration |

---

## How It Works

1. A certificate PDF is uploaded and its text is extracted with PyMuPDF.
2. Gemini reads the raw text and extracts four fields — name, university, degree, year — returning them as JSON.
3. The extracted fields are looked up in Neo4j using a parameterised Cypher query.
4. Each field is checked independently. Any mismatch is recorded and returned as a reason.
5. A structured report is printed and Gemini generates a plain-language explanation of the outcome.

---

## Setup

### Prerequisites

- Google Colab (recommended) or a local Jupyter environment
- A [Neo4j Aura](https://neo4j.com/cloud/platform/aura-graph-database/) free-tier account
- A [Google AI Studio](https://aistudio.google.com/) API key for Gemini

### 1. Open the notebook in Colab

Upload `Academic_Credential_Verifier.ipynb` to Google Colab and run cells top to bottom.

### 2. Install dependencies

The first three cells install all required packages:

```
!pip install google-generativeai
!pip install neo4j
!pip install pymupdf
```

### 3. Configure Gemini

Enter your Gemini API key when prompted. The key is entered securely via `getpass` and is not stored.

### 4. Connect to Neo4j

You will need three values from your Neo4j Aura console — the connection URI, username, and password. Enter them when prompted. The notebook calls `verify_connectivity()` immediately after connecting, so any credential errors surface right away.

### 5. Seed sample data

Run the seed cell once to populate the database with two test students:

| Student | University | Degree | Year | Expected result |
|---|---|---|---|---|
| Bhargavi | Manipal University Jaipur | B.Tech CSE | 2028 | VERIFIED |
| Rahul | VIT Vellore | B.Tech ECE | 2026 | NOT VERIFIED (if mismatched fields are submitted) |

This lets you test both the verified and not-verified paths without modifying any code.

### 6. Run verification

Upload a certificate PDF when prompted. The notebook will extract credentials, verify them, print a report, and generate an AI explanation — all in sequence.

---

## Verification Logic

The Cypher query matches a student by name and retrieves their university, degree, and year from the graph. Each field is then compared independently against what was extracted from the certificate. All three must match for the credential to be marked `VERIFIED`.

```
Status: VERIFIED       → All fields matched
Status: NOT VERIFIED   → One or more of: University mismatch, Degree mismatch, Year mismatch
Status: NOT VERIFIED   → Student not found
```

---

## Project Structure

```
Academic_Credential_Verifier.ipynb   # Main notebook
README.md                            # This file
```

---

## Limitations

- Name matching is exact and case-sensitive. Nicknames or name variations will return "Student not found."
- The pipeline handles single-page and multi-page PDFs but will not work on scanned image-only PDFs (no text layer).
- The Gemini extraction step depends on the certificate being clearly formatted. Heavily stylised certificates may produce imperfect JSON.
