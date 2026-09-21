### API Design

The recommendation system is exposed via a REST API built using FastAPI.

Key design choices:
- The model and embeddings are loaded once at application startup to minimize latency.
- The API is stateless and accepts JSON requests.
- Responses are returned in a structured JSON format suitable for integration.

Endpoints:
- **GET /health**  
  Used for service health monitoring.
- **POST /recommend**  
  Accepts a natural language query and returns top-k recommended assessments.

This design ensures the system is lightweight, fast, and production-ready.
assessments.json
This file acts as the single source of truth for all downstream tasks.

Phase 2 — Embedding Generation

Each assessment is converted into a dense semantic representation using:
sentence-transformers/all-MiniLM-L6-v2
Relevant textual fields (name, description, job levels, test type) are concatenated

Embeddings are precomputed and stored for fast retrieval:
embeddings.npy
This enables efficient semantic search without keyword matching.

Phase 3 — Semantic Retrieval

User queries are embedded using the same SentenceTransformer model

Cosine similarity is computed between query embeddings and assessment embeddings

Top-K most similar assessments are retrieved

This establishes a strong semantic baseline for recommendations.

Phase 4 — Ranking & Recommendation Logic

To improve recommendation quality beyond raw similarity:

Weighted scoring:

Semantic similarity

Job-level alignment

Diversity constraints:

Prevents recommending multiple assessments of the same test type

This results in recommendations that are:

Relevant

Diverse

Better aligned with hiring intent

Phase 5 — API Deployment (FastAPI)

The recommendation system is exposed via a REST API using FastAPI.

API Design Highlights

Model and embeddings are loaded once at startup

Stateless request handling

JSON-based input/output

Production-ready structure

Endpoints

GET /health
Health check endpoint

POST /recommend
Returns top-K recommended assessments for a given query

{
  "query": "branch manager role",
  "top_k": 3
}
{
  "query": "branch manager role",
  "results": [
    {
      "assessment_name": "Branch Manager - Short Form",
      "assessment_url": "https://www.shl.com/...",
      "score": 0.66
    }
  ]
}
Phase 6 — Evaluation
API Validation

The recommendation logic is tested directly by calling the recommend() function

This verifies correctness without relying on HTTP networking

Dataset-Based Evaluation

A provided dataset (Gen_AI Dataset.xlsx) containing real hiring queries is used

Queries from the Test-Set sheet are passed through the recommendation system

Top-K assessment URLs are generated per query

The final output is stored as:
submission.csv
This ensures reproducibility and alignment with evaluation requirements.

Key Design Decisions

SentenceTransformers over external APIs

Avoids rate limits and quota issues

Ensures reproducibility

Single embedding space

Guarantees consistent similarity scoring

Precomputed embeddings

Enables fast inference

Modular architecture

Each phase can be independently tested or extended


Assessment-Recommendation-System/
│
├── README.md
├── app.py
├── assessments.json
├── embeddings.npy
├── submission.csv
└── assessment.ipynb


pip install fastapi uvicorn sentence-transformers scikit-learn
uvicorn app:app --reload
Visit:

http://127.0.0.1:8000/health

http://127.0.0.1:8000/docs

Conclusion

This project demonstrates a complete, scalable, and practical recommendation system — from data collection to deployment — using modern NLP techniques and clean software engineering practices.

The system is robust, extensible, and suitable for real-world hiring workflows.

Author

Md.Hamdan Khan


