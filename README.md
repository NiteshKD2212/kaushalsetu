# KaushalSetu AI — Explainable Vocational Career Recommender & Conversational Counsellor

KaushalSetu AI is an explainable vocational career guidance, recommendation, and conversational counselling engine designed for learners and families exploring accredited Indian vocational qualifications (NCVET / NQR).

It combines:
1. **Lexical & Dense Semantic Information Retrieval** over National Qualifications Register (NQR) occupational standards.
2. **Rule-Based Multi-Clause Eligibility Verification** evaluating formal education and work experience prerequisites.
3. **Evidence-Based Grounded Ranking** incorporating provider verification, active labor market listings, and institutional transparency.
4. **Bilingual Grounded Explanations (English & Hindi)** with explicit disclosures of missing or unlinked information.
5. **Grounded Conversational Career Counsellor (`POST /api/counsellor/chat`)** for natural dialogue, trade comparisons, and evidence lookups without ungrounded hallucinations.

---

## 1. System Architecture

```text
├── backend/
│   ├── config.py                 # Paths, educational ladder, CORS, default weights
│   ├── data_loader.py            # Singleton cached loader for catalogue and models
│   ├── eligibility.py           # Multi-clause admission, duration, budget & location rules
│   ├── recommendation_service.py # Hybrid scoring engine & bilingual explanation generator
│   ├── counsellor.py             # Grounded conversational counsellor & intent engine
│   ├── schemas.py                # Pydantic v2 validation models
│   ├── main.py                   # FastAPI REST API endpoints
│   └── tests/
│       ├── test_api.py           # 17 automated REST API unit tests
│       └── test_evaluation_suite.py # 12 mandatory scenario tests + benchmark runner
├── frontend/
│   ├── index.html                # Responsive web interface with live health badge & chat panel
│   ├── styles.css                # Accessible UI design with color-coded status badges
│   └── app.js                    # Vanilla JS client connected to recommendations & counsellor
├── outputs/
│   ├── audit/
│   │   ├── audit_report.md
│   │   ├── provenance_checklist.md
│   │   ├── end_to_end_test_report.md
│   │   ├── next_phase_report.md
│   │   ├── synthetic_demo_profiles.json
│   │   ├── demo_results.json
│   │   ├── step5_verification_results.json
│   │   └── evaluation_benchmark_results.json
│   ├── models/                   # Serialized TF-IDF vectorizer and dense embeddings
│   └── recommendations/          # Enriched career catalogue (107 qualifications)
├── scripts/
│   ├── verify_step5_complete.py  # 9-point end-to-end HTTP verification test runner
│   └── run_demo_profiles.py      # Automated runner testing 8 synthetic profiles against live API
├── requirements.txt              # Production Python dependencies
└── kaushalsetu_validation_and_integration.ipynb # Master validation notebook
```

---

## 2. Installation & Setup

### Prerequisites
- Python 3.10+ (tested on Python 3.14.6 64-bit on Windows)
- Modern web browser (Chrome, Edge, Firefox)

### Step 1: Navigate to the repository root
```bash
cd c:\Users\etish\.antigravity-ide\kushalsetu_combined_dataset
```

### Step 2: Install dependencies
```bash
pip install -r requirements.txt
```

---

## 3. Environment Variables (Optional LLM Integration)

The application is **100% functional out-of-the-box** without any external API keys, utilizing the deterministic Grounded Knowledge-Base Counsellor.

To optionally connect a live LLM generative provider:

### Windows PowerShell:
```powershell
$env:GEMINI_API_KEY="your-gemini-api-key"
# or
$env:OPENAI_API_KEY="your-openai-api-key"
```

### Linux / macOS Bash:
```bash
export GEMINI_API_KEY="your-gemini-api-key"
# or
export OPENAI_API_KEY="your-openai-api-key"
```

*Note: If no API key is set, the system transparently operates in `grounded_knowledge_base` mode, answering directly from verified NCVET and SIDH datasets.*

---

## 4. Starting the Application

### Terminal 1: Start the FastAPI Backend (Port 8000)
```bash
python -X utf8 -m uvicorn backend.main:app --host 127.0.0.1 --port 8000
```
- API Base URL: `http://127.0.0.1:8000`
- Interactive Swagger UI: `http://127.0.0.1:8000/docs`
- Health Endpoint: `http://127.0.0.1:8000/health`

### Terminal 2: Start the Frontend HTTP Server (Port 3000)
```bash
python -m http.server 3000 --directory frontend --bind 127.0.0.1
```
- Frontend Web App: `http://127.0.0.1:3000/`

---

## 5. Running Automated Verification Suites

### Run Backend API Unit Tests (17 Tests)
```bash
python -X utf8 -m unittest backend.tests.test_api
```

### Run Independent 12-Scenario Evaluation Suite & IR Benchmark
```bash
python -X utf8 backend/tests/test_evaluation_suite.py
```

### Run 9-Point Complete Application HTTP Verification Script
With the FastAPI backend running on port 8000:
```bash
python -X utf8 scripts/verify_step5_complete.py
```

### Run Synthetic Demonstration Test Matrix (8 Profiles)
```bash
python -X utf8 scripts/run_demo_profiles.py
```

---

## 6. API Endpoints

### 1. `GET /health`
Returns backend health status, loaded catalogue count, and provider missingness rate.

### 2. `GET /api/careers`
Browses accredited qualifications with pagination and filtering:
- Query Parameters: `page` (default 1), `page_size` (default 20, max 100), `nsqf_level`, `sector`.

### 3. `GET /api/careers/{qualification_id:path}`
Retrieves full curriculum, competencies, entry prerequisites, and provider evidence for a single qualification ID.

### 4. `POST /api/recommendations`
Generates personalized, explainable recommendations based on learner profile:
```json
{
  "interests": ["welding", "fabrication"],
  "preferred_activities": ["manual metal arc welding"],
  "existing_qualification": "10th",
  "prior_experience_years": 4.0,
  "current_district": "Kanpur Nagar",
  "current_state": "Uttar Pradesh",
  "willingness_to_relocate": "within_district",
  "max_duration_hours": 800.0,
  "max_budget_inr": null,
  "language_preference": "English",
  "exclude_ineligible": false,
  "exclude_unknown_location": false,
  "exclude_unknown_fees": false,
  "top_k": 5
}
```

### 5. `POST /api/counsellor/chat`
Conversational vocational guidance endpoint grounded strictly in verified NCVET and SIDH datasets:
```json
{
  "message": "Compare Stainless Steel Fabricator vs CNC Milling Operator",
  "language": "English",
  "selected_qualification_id": null
}
```
Response:
```json
{
  "answer": "...",
  "retrieved_evidence_summary": ["..."],
  "identified_intent": "comparison",
  "mode": "grounded_knowledge_base",
  "verified_facts_present": true,
  "official_sources_recommended": ["https://nqr.gov.in"],
  "disclaimers": ["..."]
}
```

---

## 7. Troubleshooting & Common Issues

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| `UnicodeEncodeError: 'charmap' codec can't encode character '\u20b9'` | Windows PowerShell defaults to `cp1252` encoding. | Run all Python commands with `-X utf8` (e.g. `python -X utf8 ...`). |
| `[Errno 10048] address ('127.0.0.1', 8000) already in use` | A previous Uvicorn process is still listening on port 8000. | In PowerShell run: `netstat -ano \| findstr :8000`, then terminate with: `taskkill /F /PID <PID>`. |
| Browser shows `Backend Offline` | The backend server is not running on port 8000. | Ensure Uvicorn is active on `http://127.0.0.1:8000`. Click the "Retry" button on the UI. |
| All recommendations filtered out | Strict filter combination disqualified all courses. | Uncheck "Exclude options where entry prerequisites are not met" or increase "Max Duration (Hours)". |

---

## 8. Responsible AI Guarantees & Provenance Disclosures

- **Zero Salary Hallucination:** The system never fabricates course placement rates or starting wages. Regional trade survey medians from Ministry/DGT reports are explicitly labeled as contextual benchmarks.
- **Provider Sparsity Disclosure:** In this sample, 92.52% of qualifications lack linked local providers. The system preserves them in the catalogue while transparently noting that local availability requires checking the Skill India Digital Hub (SIDH) portal.
- **Strict Gating:** Admission eligibility is evaluated via multi-clause rule parsing; missing learner credentials result in `UNKNOWN`, never assumed `ELIGIBLE`.
- **Counsellor Boundedness:** The conversational counsellor never makes autonomous eligibility decisions or guarantees employment; it strictly quotes retrieved NCVET standards.
