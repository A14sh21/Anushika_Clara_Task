# Clara: Automated Client Versioning Pipeline

## Project Overview

This repository contains a robust, two-stage automation pipeline engineered for a residential services firm. The system automates the extraction and structuring of operational logic from unstructured client transcripts into version-controlled JSON assets.

The architecture is designed to handle initial onboarding and subsequent configuration updates, ensuring an accurate audit trail and strict version control between system iterations.

### Core Deliverables

* **Pipeline A (Stage 2 - Demo Setup)**: Processes initial demo transcripts to generate `v1` configuration assets.
* **Pipeline B (Stage 3 - Onboarding Update)**: Processes secondary onboarding transcripts, resolves conflicts against the `v1` baseline, and generates `v2` configuration assets along with a detailed changelog.
* **Version Control**: Maintains strict separation between `v1` and `v2` directories and generates a logic-driven diff file documenting operational changes.

---

## System Architecture and Data Flow

The solution utilizes a highly decoupled, zero-cost technology stack to prioritize scalability, idempotency, and return on investment.

* **Workflow Orchestration**: Make.com
* **Large Language Model (LLM)**: Groq API utilizing the `llama-3.1-8b-instant` model for high-speed, structured JSON extraction.
* **Data Storage and Versioning**: Dropbox
* **State Management**: Google Sheets (Clara Task Tracker)

---

## Technical Implementation and Engineering Standards

### 1. Data Integrity and Validation

Processing unstructured transcript data often introduces JSON syntax errors due to hidden control characters (such as carriage returns and tabs).

* **Implementation**: This pipeline mitigates this by utilizing Make.com's strict Data Structure configurations. By mapping data to a predefined JSON schema prior to the HTTP request, the system automatically escapes reserved characters, guaranteeing valid JSON payloads for the LLM.
* **Input Constraint**: To ensure maximum reliability during edge-case testing, the input transcription text files (`.txt`) must be formatted as a single, continuous paragraph with no line breaks or carriage returns.

### 2. Prompt Architecture and Hygiene

The generated `agent_spec.json` files adhere to strict prompt engineering guidelines required for enterprise voice agents:

* **Greeting**: Persona-driven and professionally aligned with the client's brand identity.
* **Purpose**: Clear, singular operational objective (e.g., standard scheduling vs. emergency dispatch).
* **Transfer Logic**: Explicit rules defining when and how to escalate the call to a human operator.
* **Fallback Mechanisms**: Robust error handling to gracefully manage out-of-scope user queries.

### 3. Idempotency and State Management

The workflows are designed to be idempotent. Executing the pipelines multiple times will not result in data corruption. State is strictly managed via the Google Sheets tracker, requiring explicit status updates to trigger the respective downstream processes.

---

## Setup and Execution Guide

### Prerequisites

1. An active Make.com account.
2. A Groq API key.
3. Connected Dropbox and Google Sheets integrations.

### Deployment Steps

1. **Import Scenarios**: Navigate to Make.com, create a new scenario, click the `...` menu, and import `pipeline_a_blueprint.json` and `pipeline_b_blueprint.json` from the `/workflows` directory.
2. **Authenticate Modules**: Update all Dropbox, Google Sheets, and HTTP (Groq) modules with your personal authentication credentials or API keys.
3. **Prepare Storage**: Ensure the `/Clara_Assignment/` folder structure is initialized in your connected Dropbox account.

### Execution Protocol

1. **Trigger Pipeline A**: Open the Google Sheet and change a client's status. Execute Pipeline A manually or via polling. Verify the `v1` assets are generated in Dropbox.
2. **Trigger Pipeline B**: Update the same client's status to `Onboarding Complete`. Execute Pipeline B. Verify the `v2` assets and `changes.md` file are generated.

---

## Future Improvements and Scalability

* **Database Migration**: Transition the state management layer from Google Sheets to a robust relational database (such as PostgreSQL or Supabase) to handle concurrent triggers and ensure strict ACID compliance.
* **Automated Quality Assurance**: Implement a secondary LLM verification node within the workflow to automatically validate the generated JSON against the source transcript, flagging hallucinations before writing to the storage bucket.
