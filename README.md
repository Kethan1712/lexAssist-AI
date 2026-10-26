# LexAssist AI — Indian Legal Semantic Information Retrieval System

LexAssist AI is a Python-based legal semantic information retrieval system designed to retrieve relevant Indian statutes, judicial precedents, and evidence from uploaded legal documents using natural-language queries. It also integrates external legal research and Gemini-based response generation to provide conversational, evidence-grounded answers.

## Key Features

- **Semantic Legal Search:** Retrieve potentially relevant statutes and judicial precedents using natural-language queries.
- **Statute Retrieval:** Search a local dataset containing 936 statute records.
- **Precedent Retrieval:** Search a local dataset containing 3,183 judicial precedents.
- **FAISS Vector Search:** Perform similarity-based retrieval using normalized embeddings.
- **PDF Evidence Retrieval:** Extract and process legal documents into chunks to identify relevant passages.
- **External Legal Research:** Query the India Code JSON API when external research is needed.
- **Conversational Chatbot:** Support context-aware follow-up questions through conversation history.
- **Evidence-Grounded Responses:** Use Gemini to generate natural-language answers from retrieved evidence.

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Backend logic and retrieval pipeline |
| Sentence Transformers | Generate semantic embeddings |
| all-MiniLM-L6-v2 | Text embedding model |
| FAISS | Vector similarity search |
| Flask | Backend API and web application |
| Gemini | Natural-language response generation |
| HTML, CSS, JavaScript | Frontend interface |
| India Code JSON API | External Indian legal research |

## System Architecture

```text
User Question
     |
     v
Flask Chat API
     |
     v
Conversation Manager
     |
     v
Chatbot Router
     |
     +-------------------+
     |         |         |
     v         v         v
   Local    Uploaded   External
  Evidence  Document   Research
     |      Evidence      |
     |         |          v
     |         |      India Code API
     |         |          |
     +---------+----------+
               |
               v
        Retrieved Evidence
               |
               v
         Answer Generator
               |
               v
             Gemini
               |
               v
       Natural-Language Answer
```

**Core principle:** Retrieve evidence first, generate the answer second.

The router determines the intended evidence source. Local retrieval uses the existing legal datasets, uploaded-document retrieval searches relevant chunks from the current document, and external research queries the India Code API. Gemini receives the resulting evidence and generates the response.

## Semantic Retrieval Pipeline

LexAssist AI uses the `sentence-transformers/all-MiniLM-L6-v2` model to convert text into 384-dimensional embeddings.

The embeddings are searched using FAISS with `IndexFlatIP`. Because the embeddings are normalized, inner-product ranking corresponds to cosine-similarity ranking.

### Current Dataset Statistics

| Dataset | Records / Vectors |
|---|---:|
| Statute records | 936 |
| Judicial precedent records | 3,183 |
| Query vectors | 5,017 |
| Legal documents processed | 5 |
| Document chunks | 577 |
| Embedding dimensions | 384 |

Similarity scores indicate semantic similarity, not legal correctness, legal applicability, or confidence in an answer.

## Week 7 — Conversational Evidence Pipeline

The Week 7 development extends the existing retrieval foundation with the following components:

- `chatbot_router.py` — routes questions according to local provision matching and supported conversational context.
- `local_evidence_manager.py` — retrieves and resolves statute and precedent evidence.
- `uploaded_document_evidence.py` — retrieves relevant chunks from an uploaded document.
- `external_research.py` — retrieves external legal search results from the India Code JSON API.
- `answer_generator.py` — passes supplied evidence and the user's question to Gemini for response generation.
- `conversation_manager.py` — validates conversation history and prepares contextual information.
- `app.py` — integrates the chatbot components through Flask's `/api/chat` endpoint.
- `templates/index.html` — provides the web interface and sends conversation history with chat requests.

### External Research

The project uses the following India Code API search endpoint:

https://indiacode.ecourtsindia.com/api/v1/search

External search results may contain multiple provisions with the same section number. The system should preserve this ambiguity instead of automatically assuming which Act the user intended.

### Follow-Up Questions

The frontend maintains conversation history and sends it to the Flask chat endpoint. The backend validates that history and makes it available to the router and answer generator.

The current provision-reference follow-up resolver supports a narrow, deterministic case; it is not a general-purpose conversational reasoning engine.

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/Kethan1712/lexAssist-AI.git
cd lexAssist-AI
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

On macOS or Linux:

```bash
source venv/bin/activate
```

### 3. Install dependencies

If a `requirements.txt` file exists in the repository:

```bash
pip install -r requirements.txt
```

Otherwise, install the dependencies specified by the project's actual environment and dependency files.

### 4. Configure environment variables

Configure the Gemini API key using the environment-variable name expected by the current application code. Keep API keys and credentials private; never commit them to GitHub.

### 5. Run the application

```bash
python app.py
```

If the application starts successfully, open:

http://127.0.0.1:5000

**Note:** The application requires its expected legal datasets, FAISS indexes, dependencies, and API configuration to be available. Verify their paths and setup instructions against the current repository.

## Limitations

- Semantic similarity does not establish whether a statute applies to a particular case.
- External research depends on API availability and the returned search results.
- Follow-up resolution is limited to explicitly supported patterns.
- Generated responses may still contain errors; evidence-grounding instructions do not guarantee hallucination-free output.
- Missing or ambiguous source information should not be treated as verified legal facts.
- LexAssist AI is a research prototype and does not replace advice from a qualified legal professional.

## Future Improvements

- Broaden and evaluate follow-up question resolution.
- Improve source attribution and evidence traceability.
- Add systematic retrieval and answer-quality evaluation.
- Strengthen error handling and external API resilience.
- Expand supported legal document formats and testing coverage.

## Author

**Kethan Saradhi**

GitHub: https://github.com/Kethan1712

---

*LexAssist AI combines semantic legal retrieval with a conversational evidence pipeline to help users explore Indian legal information through natural-language questions.*
