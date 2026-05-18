# 🤖 Expert AI Technical Advisor & Sales Automation System

> **Reference Architecture:** A sophisticated, multi-agent AI system designed for technical sales of complex industrial machinery. Features a high-fidelity RAG engine, automated GDPR compliance, multilingual support (9 languages), and an asynchronous lead-capture pipeline.

![Status](https://img.shields.io/badge/Status-Reference_Architecture-blue)
![Tech Stack](https://img.shields.io/badge/n8n-Self_Hosted-red)
![AI Model](https://img.shields.io/badge/Model-GPT--Lastest-green)
![Languages](https://img.shields.io/badge/Languages-9_Supported-orange)

---

## ⚠️ Disclaimer
This repository contains a sanitized reference implementation inspired by production systems architected for heavy machinery distributors. **All proprietary brand data, internal API credentials, and client-specific business logic have been removed.** This project demonstrates architectural patterns and prompt engineering for enterprise AI deployments.

---

## 💡 System Capabilities

This system acts as a **Virtual Technical Consultant** for complex, high-ticket equipment. It follows a "Diagnose before Propose" methodology, ensuring that recommendations are technically viable and compliant with engineering constraints.

### Core Features:
*   **Multi-Agent Architecture:** Three specialized agents (Technical Advisor, Web Search Fallback, GDPR Compliance) working in parallel for accuracy and legal safety.
*   **Multilingual Intelligence (9 Languages):** Automatically detects and adapts to user's language (Polish, English, Ukrainian, German, French, Spanish, Italian, Lithuanian, Czech) using character-pattern recognition.
*   **Autonomous GDPR Compliance:** Context-aware agent automatically inserts localized legal disclaimers when personal data is requested.
*   **Technical Diagnosis Engine:** The agent identifies customer requirements (capacity, power, operational scale) before searching the Vector Store for specific equipment models.
*   **Engineering "Sanity Check":** Built-in logic to prevent configuration errors (e.g., ensuring an attachment is compatible with the base machine's power output).
*   **Brand Exclusion Logic:** Intelligent query processing that filters out competitor mentions when searching the internal knowledge base for compatible solutions.
*   **Asynchronous Lead Capture:** A dedicated "Sentiment Agent" parses conversation history in the background to extract lead data (Name, Phone, Intent) only when purchase signals are detected.
*   **Security Layer:** Anti-prompt-injection filters and instruction leak prevention to block reconnaissance attempts.

---

## 🛠️ System Architecture

The system uses a **Decoupled "Fire-and-Forget" Architecture** to ensure zero latency for the end user.

### Logic Flow

```mermaid
graph TD
    User([Web User]) -->|Technical Query| Frontend[1. Advisor Agent - GPT-5.1]
    
    subgraph "Reasoning & Tools"
    Frontend <-->|RAG| VectorDB[(Technical Manuals)]
    Frontend <-->|Fallback| WebSearch[Web Search Agent]
    Frontend <-->|Auto-Trigger| GDPR[GDPR Compliance Agent]
    end

    Frontend -->|Instant Response| User
    
    %% Async Trigger
    Frontend -.->|Async Execute| Backend[2. Analytics Workflow]
    
    subgraph "Background Processing"
    Backend -->|Lead Parsing| LeadAgent((Sentiment Agent - GPT-5.1))
    LeadAgent -->|JSON Object| Qualifier{Is Valid Lead?}
    Qualifier -->|Yes| CRM[(CRM / Google Sheets)]
    Qualifier -->|No| Logs[(Conversation Logs)]
    end
```

---

## 📂 Repository Structure

*   **/workflows_sanitized/**
    *   `1_chat_advisor_frontend.json`: The core multi-agent system with RAG, GDPR automation, and multilingual support.
    *   `2_background_analytics.json`: The asynchronous "Fire-and-Forget" backend for lead extraction, sentiment analysis, and CRM synchronization.
    *   `3_automated_reporting.json`: A scheduled business intelligence (BI) workflow that generates weekly performance audits and lead quality summaries for management.
*   **/diagrams/**: Technical architecture diagrams and logic flow schematics (Mermaid & screenshots).
*   **/mock_data/**: Sanitized technical specifications (CSV/PDF) and sample lead log templates for testing.

---
---

## 🧠 Engineering Challenges & Solutions

### 1. Zero-Latency Asynchronous Analytics (The "Decoupling" Pattern)
**Problem:** Real-time sentiment analysis and lead data extraction traditionally add 5-10 seconds to the response time, significantly degrading the user experience.  
**Solution:** I implemented a **Decoupled Architecture** using n8n's sub-workflow execution with the `waitForSubWorkflow: false` (Fire & Forget) configuration. The Advisor Agent responds to the user in under 3 seconds, while a background "Sentiment Agent" (GPT-5.1) handles the complex data parsing and CRM logging independently.

### 2. Autonomous GDPR Compliance Without Manual Intervention
**Problem:** Manual insertion of data processing notices is error-prone and breaks conversation flow.  
**Solution:** Developed a **Context-Aware GDPR Agent** that monitors conversation history and auto-injects localized legal disclaimers (in the user's detected language) when personal data requests are identified. This ensures 100% compliance without human oversight.

### 3. Multilingual Adaptation Without External Translation APIs
**Problem:** Translation APIs add latency, cost, and privacy concerns (data sent to third parties).  
**Solution:** Engineered **Character-Pattern Language Detection** using Unicode regex. The system analyzes special characters in user input (ą, ę, ł for Polish; ґ, є, ї for Ukrainian; ä, ö, ü for German, etc.) to identify language and switches response style contextually. Supports 9 languages with zero external API calls.

### 4. Engineering Constraint Validation & Hallucination Control
**Problem:** Generic LLMs often agree to technically impossible configurations (e.g., suggesting a high-capacity attachment for a low-power base machine).  
**Solution:** Developed a **Strict Logic Layer** within the System Prompt. The agent is forced to perform a "Technical Sanity Check" by cross-referencing user requirements with the verified technical specs in the Vector Store. If a mismatch is detected, the agent is programmed to politely correct the user rather than providing a hallucinated solution.

### 5. Intent-Triggered Lead Conversion
**Problem:** Static "contact us" forms are often ignored, while aggressive bots asking for data too early increase bounce rates.  
**Solution:** Engineered a **Contextual Conversion Trigger**. The AI remains in "Advisory Mode" until it detects specific purchase intent signals (e.g., pricing inquiries, requests for financing, or compatibility questions). At that precise moment, the agent triggers a lead-capture module, providing a seamless transition from information gathering to a sales opportunity.

### 6. Background Data Structuring for Sales Prioritization
**Problem:** Raw chat logs are time-consuming for sales managers to review and audit.  
**Solution:** The background Analytics Workflow uses **JSON Output Parsing** (GPT-5.1 with strict schema enforcement) to distill entire conversations into four actionable fields: `sentiment` (HOT/COLD/SERWIS), `phone`, `name`, and `interest`. This allows for automated lead scoring and ensures that the sales team prioritizes the highest-value inquiries first.

---

## 🤝 Transparency: Human vs. AI Role

This project was built using an **AI-Augmented Systems Engineering** approach, focusing on high-level architecture and precise prompt engineering.

*   **My Role (Architect):** Designed the multi-agent workflow architecture (Advisor, Web Search, GDPR agents) and async decoupling pattern, defined the technical constraint validation rules and brand exclusion logic, engineered the GDPR compliance triggers and multilingual detection strategy (character-pattern regex), configured the VPS environment, Docker containers, and RAG (Vector Store) hierarchy, developed the security layer for instruction leak prevention.
*   **AI Role:** Assisted in generating specific JavaScript for JSON data transformation and date parsing, provided troubleshooting support for API-specific error handling (e.g., handling rate limits and token optimization), suggested HTML email template formatting for automated reporting.

---

## 🚀 Deployment Requirements

*   **Orchestrator:** n8n (Self-hosted on Docker)
*   **Inference Engines:** OpenAI GPT-5.1
*   **Knowledge Base:** OpenAI File Search (RAG with Vector Store)
*   **Data Destination:** Google Sheets API (as a Lightweight CRM)
*   **External Tools:** Authorized Web Search Tool (for dynamic updates), Calculator (specification validation)

---

**Last Updated:** May 2026  
**Repository Version:** 2.0 (Multi-Agent Architecture with GDPR Automation & Multilingual Support)
