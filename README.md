# 📘 Project README – AI PDF Chatbot with Qdrant + Gemini + n8n

This project contains **two workflows** built in **n8n**, enabling:

1. **PDF Uploading & Vector Storage (Workflow 1)**
2. **AI Chatbot with RAG + Memory + Streaming (Workflow 2)**

Together, they form a complete document‑aware chatbot system using **Google Gemini**, **LangChain**, and **Qdrant Vector DB**.

---

# 🚀 Overview

This system lets users:

* Upload PDF files via a public form.
* Automatically extract text → chunk → embed → store vectors in Qdrant.
* Query the documents using an AI chatbot.
* Receive answers from Gemini using RAG (Retrieval‑Augmented Generation).

---

# 📂 Workflow 1 – PDF Upload & Vectorization

Workflow name: **Upload & Index PDF Document**

### 🧩 Purpose

Handles document ingestion and prepares the knowledge base by:

1. Accepting PDF upload via form trigger.
2. Loading PDF content.
3. Splitting text into optimized chunks.
4. Generating embeddings with Google Gemini.
5. Storing embeddings + metadata into Qdrant collection (`chatbot_docs`).

### ⚙️ Workflow Components

#### **1. Form Trigger (n8n-nodes-base.formTrigger)**

* Lets the user upload a PDF.
* Accepts only `.pdf`
* Required field.

#### **2. Default Data Loader (PDF Loader)**

* Extracts text from PDF binary file.
* Converts into raw text data.

#### **3. Recursive Text Splitter**

* Splits text into chunks.
* Chunk overlap: **200 characters**.

#### **4. Gemini Embeddings Node**

* Generates vector embeddings using **Google Gemini**.

#### **5. Qdrant Vector Store (Insert Mode)**

* Saves chunked documents + embeddings in `chatbot_docs` collection.

### 📊 Data Flow

Form → Data Loader → Text Splitter → Gemini Embeddings → Qdrant Store

---

# 💬 Workflow 2 – AI Chatbot with RAG Retrieval

Workflow name: **AI Chatbot (Streaming + RAG)**

### 🧩 Purpose

Provides an intelligent chatbot that:

* Receives user messages via webhook.
* Uses Gemini Chat model to respond.
* Retrieves relevant context from Qdrant.
* Maintains short-term memory.
* Streams responses in real‑time.

### ⚙️ Workflow Components

#### **1. Chat Trigger (Webhook)**

* Public webhook for receiving chat messages.
* Streaming mode enabled.

#### **2. Google Gemini Chat Model**

* LLM used for generating answers.
* Temperature: **0.3** (balanced factual responses).
* Top‑K: **4**.

#### **3. Memory Buffer Window**

* Stores recent conversation history.
* Provides better contextual responses.

#### **4. Qdrant Vector Store (RAG Tool Mode)**

* Retrieves relevant document chunks.
* Used by the agent as a knowledge tool.

#### **5. Gemini Embeddings**

* Required for compatible vector retrieval in Qdrant.

#### **6. AI Agent Node**

* Combines:

  * LLM
  * Memory
  * Vector tool
  * Streaming responses
* Orchestrates the full conversation.

### 📊 Data Flow

Webhook → AI Agent → (Gemini LLM + Memory + Qdrant Retrieval) → Streaming Response

---

# 🗄️ Qdrant Collection Used

**Collection Name:** `chatbot_docs`

Both workflows rely on this shared vector store:

* Workflow 1 inserts documents.
* Workflow 2 retrieves documents.

<!-- Ensure the collection exists or allow n8n to auto‑create it. -->

---

# 🔧 Requirements

* n8n v1.50+
* Qdrant Cloud or local Qdrant instance
* Google Gemini API Key (Google Palm/Gemini)

---

# 📡 Deployment Notes

* Workflow 2 exposes a **webhook** → can be integrated with a frontend chat UI.
* Workflow 1 exposes a **form** → can be embedded for client‑side document uploads.
* Make sure both workflows share the **same Qdrant credentials**.

---

# 🧪 Testing Instructions

### To test Workflow 1:

1. Open the Form Trigger URL.
2. Upload a PDF.
3. In Qdrant dashboard, confirm new vectors were added.

### To test Workflow 2:

1. Send a POST request to the webhook with `userMessage`.
2. Ask a question related to the uploaded PDF.
3. Validate that:

   * AI retrieves relevant chunks.
   * Gemini answers using document context.

---

# 📘 Summary

This two‑workflow system forms a **complete RAG chatbot pipeline**:

* **Workflow 1:** PDF → Text → Chunk → Embed → Vector Store
* **Workflow 2:** User Query → Retrieval → Gemini Response → Streaming Chatbot

It's clean, scalable, and easily extendable for multi‑document or multi‑user knowledge bases.

