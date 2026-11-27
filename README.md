# AI-Driven Placement Preparation with Personalized Career Guidance

A full-stack web platform that helps engineering students prepare for campus placements through **resume analysis, job matching, skill-gap detection, AI career guidance, aptitude practice and progress tracking**.

> Final-year B.E. (CSE) project — Government College of Engineering, Bargur (Anna University, Chennai), October 2026.

## Features

| Module | What it does |
|---|---|
| Authentication | Register/login, Google Sign-In (OAuth 2.0), JWT sessions (1 h), BCrypt hashing, 30-min password-reset tokens |
| Resume Upload | PDF up to 10 MB, text extraction with Apache Tika (PDFBox fallback) |
| ATS Analysis | Explainable score: skill match 35% + SBERT similarity 30% + sections 20% + quality 15% |
| Job Matching | Hybrid ranking (70% keyword + 30% semantic), curated jobs + Adzuna API, expiry handling, de-duplication |
| Skill-Gap Analysis | Missing skills → concrete learning recommendations |
| AI Career Guidance | Local Qwen2.5 1.5B (Ollama) with 23 anti-hallucination rules; 8-section report, optional e-mail |
| Aptitude Assessment | Personalised MCQs (5/10), 30 s timer, score history |
| Performance Tracking | Dashboard with readiness score and achievements |

## Architecture

```
React (Vite)  ──REST/JWT──▶  Spring Boot  ──HTTP──▶  FastAPI AI service
 :5173                       (MySQL, JPA)             SBERT + Ollama (Qwen2.5)
                                  │
                                  └──▶ Adzuna API, Brevo e-mail, Google OAuth
```


## Tech Stack

- **Frontend:** React, Vite, react-markdown, lucide-react
- **Backend:** Java 17, Spring Boot (Web MVC, WebFlux, Data JPA, Security, OAuth2 Client), JJWT, Apache Tika, PDFBox, MySQL
- **AI service:** Python, FastAPI, Uvicorn, sentence-transformers (`all-MiniLM-L6-v2`), Ollama (`qwen2.5:1.5b`)
- **External:** Adzuna Job Search API, Brevo e-mail API, Google OAuth 2.0

## Repository Structure

```
├── backend/
│   ├── placement-preparation/   # Spring Boot REST API
│   └── ai-service/              # FastAPI (SBERT + LLM)
├── frontend/                    # React SPA
├── docs/                        # Report, presentation, screenshots, architecture
├── uploads/                     # Runtime resume storage (git-ignored)
├── .gitignore
├── LICENSE
└── README.md
```

## Getting Started

### Prerequisites
Java 17, Node.js 18+, Python 3.10+, MySQL 8, [Ollama](https://ollama.com), Git.

### 1. Database
```sql
CREATE DATABASE placement_preparation;
```

### 2. AI service
```bash
cd backend/ai-service
python -m venv venv && source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
ollama pull qwen2.5:1.5b
uvicorn main:app --reload --port 8000
```

### 3. Backend
```bash
cd backend/placement-preparation
cp src/main/resources/application.properties.example src/main/resources/application.properties
# edit application.properties with your own values
./mvnw spring-boot:run
```

### 4. Frontend
```bash
cd frontend
cp .env.example .env
npm install
npm run dev        # http://localhost:5173
```

## Key REST Endpoints

| Endpoint | Method | Purpose |
|---|---|---|
| `/api/students/register`, `/login` | POST | Create account / get JWT |
| `/api/students/forgot-password`, `/reset-password` | POST | Password recovery |
| `/api/resumes/upload`, `/status` | POST, GET | Upload resume / check status |
| `/api/ats/analyze`, `/history` | POST, GET | ATS analysis / history |
| `/api/ats/skill-gap` | GET | Skill-gap recommendations |
| `/api/job-matching/matches` | GET | Ranked job matches |
| `/api/adzuna/import` | POST | Import jobs from Adzuna |
| `/api/career-guidance/ai`, `/history` | GET | AI guidance / history |
| `/api/aptitude/questions`, `/results`, `/history` | GET, POST | Aptitude tests |
| `/api/feedback`, `/history` | POST, GET | Student feedback |

## Scoring Formulas

```
ATS (job-specific) = 0.35·Skill + 0.30·Semantic + 0.20·Sections + 0.15·Quality
ATS (resume-only)  = 0.60·Sections + 0.40·Quality
Job match          = 0.70·Keyword + 0.30·Semantic
Job match (target) = 0.30·JobMatch + 0.70·RoleRelevance
Skill gap          = Required \ Current
Readiness          = (ResumeUploaded + ATS + CareerGuidance + AptitudeAccuracy) / 4
```

## Screenshots

Add images to `screenshots/` and reference them here:

| Login | Dashboard |
|---|---|
| <img width="1535" height="710" alt="Login" src="https://github.com/user-attachments/assets/b4dc655f-b2cd-44bb-8658-6782ff398843" />
| <img width="1591" height="758" alt="Dashboard" src="https://github.com/user-attachments/assets/70c97b66-08c0-4af7-8a8e-3c7788d1e739" />
 |
| **ATS Analysis** | **Job Matching** |
| <img width="1591" height="763" alt="ATS Analysis" src="https://github.com/user-attachments/assets/0405f324-ad84-4424-8fce-e607e4e2633d" />
| <img width="1591" height="776" alt="Job Matching" src="https://github.com/user-attachments/assets/0adc88ca-051a-4ed7-8e85-b2de8e7037c6" />
 |

## Documentation

- 📄 [Project Report]
- 📊 [Presentation]

## Limitations

- ATS score is an indicator, not any employer's official ATS score.
- Weights (35/30/20/15, 70/30) are design choices, not tuned on labelled placement data.
- Only text-based PDFs are supported (no OCR yet).
- Does not guarantee placement; intended for decision support and learning.

## Future Work

DOCX/OCR support, resume templates, adaptive aptitude questions, larger LLMs, refresh tokens, rate limiting, automated tests and deployment configs.

## Team

| Name | Register No. |
|---|---|
| Kaviyarasan R | 2303610710421024 |
| Sunil Singh J | 2303610710421052 |
| Vadivelan A | 2303610710421056 |

**Guide:** Dr. R. Subathra · **Supervisor:** Mr. B. Purushothaman · **HOD:** Dr. J. Nafeesa Begum
Department of CSE, Government College of Engineering, Bargur.

## License

MIT — see [LICENSE](LICENSE).
