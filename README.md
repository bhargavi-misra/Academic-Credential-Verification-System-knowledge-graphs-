# Academic Credential Verifier

This project verifies student academic credentials against a Neo4j graph database. You upload a certificate PDF, Gemini reads it and pulls out the key fields, and those fields get checked against what's stored in the database. At the end you get a verification report and a plain-English explanation of the result.

## Why Neo4j

I picked Neo4j over a regular SQL database because credential data is naturally a graph. A student connects to a university, a degree, and a graduation year. In a relational DB you'd be doing joins across multiple tables to answer one simple question. In Neo4j that same question is a single traversal:

```
(Student)-[:STUDIED_AT]->(University)
(Student)-[:HAS_DEGREE]->(Degree)
(Student)-[:GRADUATED_IN]->(Year)
```

It models the problem the way it actually exists, not the way a table structure forces it to exist.

## Tech Stack

| Tool | Purpose |
| --- | --- |
| Google Gemini 2.5 Flash | Extracts structured fields from raw certificate text, generates result explanation |
| Neo4j Aura | Graph database that stores student credential records |
| PyMuPDF (fitz) | Reads the uploaded PDF and pulls out the text |
| Python / Google Colab | Runs the whole pipeline |

## How It Works

1. You upload a certificate PDF. PyMuPDF extracts the text.
2. That text goes to Gemini with a prompt asking for name, university, degree, and year as JSON.
3. The JSON is parsed and each field is looked up in Neo4j using a parameterised Cypher query.
4. Fields are checked one by one. If anything doesn't match, the reason is recorded.
5. A report is printed and Gemini writes a short explanation of what happened and why.

## Setup

You need a free [Neo4j Aura](https://neo4j.com/cloud/platform/aura-graph-database/) account and a [Gemini API key](https://aistudio.google.com/) from Google AI Studio.

Open the notebook in Google Colab and run the cells from top to bottom.

**Install dependencies**

The first three cells handle this:

```
!pip install google-generativeai
!pip install neo4j
!pip install pymupdf
```

**Configure Gemini**

Enter your API key when prompted. It goes through `getpass` so it's not visible or stored.

**Connect to Neo4j**

You'll need the connection URI, username, and password from your Aura console. The notebook checks connectivity right after connecting so if anything is wrong it tells you immediately instead of failing later inside a query.

**Seed the database**

Run the seed cell once before testing. It adds two students:

| Student | University | Degree | Year | Expected Result |
| --- | --- | --- | --- | --- |
| Bhargavi | Manipal University Jaipur | B.Tech CSE | 2028 | VERIFIED |
| Rahul | VIT Vellore | B.Tech ECE | 2026 | NOT VERIFIED |

This gives you both outcomes to test without changing any code.

**Run it**

Upload a certificate PDF when prompted and the rest runs automatically.

## Verification Logic

The query finds the student by name and fetches their university, degree, and year from the graph. Each of those three fields is compared against the certificate. All three have to match for the status to come back as VERIFIED. If any field is wrong, the response includes which ones failed.

```
VERIFIED        all fields matched
NOT VERIFIED    one or more fields didn't match (university, degree, or year)
NOT VERIFIED    student name not found in the database
```

## Known Limitations

Name matching is exact and case-sensitive, so variations or initials will return "Student not found." The PDF extraction only works if the file has a real text layer. Scanned certificates that are just images won't parse correctly. The Gemini extraction also works best on clean, standard certificate formats.

## Files

```
Academic_Credential_Verifier.ipynb
README.md
```
