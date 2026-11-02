# 👕 LUXE

An online clothing store with an **AI shopping assistant** built in.

Browse Men / Women / Kids clothing, filter by price, add to a cart, and check
out with just an email. Or skip the filters entirely and *ask*: type
*"men's shirts under ₹2000"* into the chat widget and it fetches real products
from the catalog for you.

---

## This repo tells a story

LUXE is built the way a **Forward Deployed AI Engineer** takes an idea from
nothing to production: understand the client's existing system → prove the AI
idea in an isolated POC → harden it and integrate it, with evals and
observability. Each stage is a branch:

| Branch | Stage | What it is |
|--------|-------|------------|
| [`01-store-only`](../../tree/01-store-only) | **Discovery** — the client's existing app | The plain clothing store: FastAPI + MongoDB + a vanilla-JS storefront, cart, orders, accounts, admin. **No AI.** |
| [`02-chatbot-poc`](../../tree/02-chatbot-poc) | **POC** — prove it's possible | A standalone chatbot: connect to the product database, answer natural-language queries, wrapped in safety guardrails. One `POST /chat` + a tiny page. Nothing else. |
| `main` | **Integration** — production | `01` + `02`, merged. The POC's `chatbot/` package dropped into the store's backend, plus live evals and full tracing. **You are here.** |

The full narrative is in [`docs/STORY.md`](docs/STORY.md).

---

## Stack (this branch)

| | |
|---|---|
| **Storefront** | Plain JavaScript + CSS — served as-is, no build step |
| **Backend** | FastAPI (Python) |
| **Database** | MongoDB Atlas |
| **AI assistant** | Pydantic AI agent on Groq models, with input/output safety guardrails |
| **Evals** | Pydantic Evals — live evaluators on every real chat, plus an offline suite |
| **Observability** | Pydantic Logfire |
| **Hosting** | Google Cloud Run (single container), deployed by Google Cloud Build |

## Features

- **Catalog** — three categories, price-range filter, per-product sizes & colours.
- **Cart & checkout** — add to cart, "Buy All Now"; orders are keyed by email
  (a real one if you signed in, an auto-generated guest one if you didn't).
- **Accounts** — email + password *or* Sign in with Google; edit profile,
  upload an avatar, view order history, delete the account.
- **AI assistant** — natural-language product search on every page, wrapped in
  guardrails that block prompt-injection and unsafe content.
- **Admin** — add / edit / delete products, plus a bulk importer that takes an
  Excel sheet and a zip of images.

## Quick start

```bash
# 1. Configuration
cp .env.example .env          # then fill in MONGO_URI and GROQ_API_KEY (minimum)

# 2. Environment
uv venv clothenv              # (or: python -m venv clothenv)
source clothenv/Scripts/activate       # Windows;  clothenv/bin/activate on macOS/Linux
uv pip install -r requirements.txt     # (or: pip install -r requirements.txt)

# 3. Run — one process, storefront + API on http://localhost:8000
python main.py
```

Open <http://localhost:8000>. The interactive API reference is at
<http://localhost:8000/docs>.

## Documentation

Everything — the FDE story, architecture, each component in plain language, the
deployment pipeline, a glossary — is in **[`docs/`](./docs/README.md)**.

| | |
|---|---|
| [Story](docs/STORY.md) | How the three branches fit together |
| [Overview](docs/01-overview.md) | What it is, the full stack, why each piece |
| [Architecture](docs/02-architecture.md) | How it fits together + request walkthroughs |
| [Frontend](docs/03-frontend.md) · [Backend](docs/04-backend.md) · [Database](docs/05-database.md) | Each layer in detail |
| [AI Assistant](docs/06-ai-assistant.md) | The chatbot, its pipeline, and its guardrails |
| [Deployment](docs/07-deployment.md) · [`commands.md`](commands.md) | Google Cloud, and the exact setup commands |
| [Glossary](docs/08-glossary.md) | Every term, one sentence each |

## License

MIT.



ecom-store-gcp-main/
│
├── Frontend/
│   ├── index.html
│   └── src/
│       ├── components/
│       │   ├── Chatbot.js              # AI shopping chat widget
│       │   ├── Header.js               # Store header
│       │   └── Hero.js                 # Homepage hero section
│       │
│       ├── pages/
│       │   ├── Home.js                 # Store homepage
│       │   ├── Admin.js                # Admin dashboard
│       │   ├── Cart.js                 # Shopping cart
│       │   ├── ProductDetail.js        # Product details
│       │   ├── Profile.js              # Customer profile
│       │   ├── admin/                  # Product management pages
│       │   └── profile/                # Authentication and profile pages
│       │
│       ├── services/
│       │   ├── api/
│       │   │   ├── chat.js             # Chatbot API integration
│       │   │   ├── products.js         # Product API calls
│       │   │   ├── cart.js             # Cart API calls
│       │   │   ├── orders.js           # Order API calls
│       │   │   ├── auth.js             # Authentication API calls
│       │   │   ├── http.js             # HTTP client
│       │   │   └── common.js           # Shared API utilities
│       │   ├── api_v2.js
│       │   ├── cartActions.js
│       │   ├── cartCount.js
│       │   ├── nav.js
│       │   └── state.js
│       │
│       ├── utils/
│       │   ├── escapeHtml.js
│       │   └── placeholder.js
│       │
│       ├── main.js                     # Frontend entry point
│       └── style.css                   # Application styling
│
├── backend/
│   ├── chatbot/
│   │   ├── agent.py                    # Pydantic AI agent + Groq LLM
│   │   │                               # search_products tool
│   │   ├── pipeline.py                 # Complete chatbot execution flow
│   │   ├── guardrails.py               # Input/output safety checks
│   │   ├── online_evals.py             # Background LLM evaluations
│   │   └── __init__.py
│   │
│   ├── evals/
│   │   ├── chatbot_evals.py            # Offline chatbot evaluation tests
│   │   └── __init__.py
│   │
│   ├── routes/
│   │   ├── chatbot.py                  # POST /chat endpoint
│   │   ├── products.py                 # Product APIs
│   │   ├── products_bulk.py            # Bulk product operations
│   │   ├── cart.py                     # Cart APIs
│   │   ├── orders.py                   # Order APIs
│   │   ├── auth.py                     # Authentication APIs
│   │   ├── google_auth.py              # Google Sign-In
│   │   └── profile.py                  # Profile APIs
│   │
│   ├── utils/
│   │   ├── llm_gateway.py              # Optional Portkey gateway
│   │   ├── mongo.py                    # MongoDB query helper functions
│   │   ├── images.py                   # Product image processing
│   │   ├── passwords.py                # Password hashing utilities
│   │   └── users.py                    # User-related utilities
│   │
│   ├── database.py                     # MongoDB connection + collections
│   ├── config.py                       # Centralized environment settings
│   ├── auth.py                         # Authentication logic
│   └── models.py                       # Application data models
│
├── docs/
│   ├── 01-overview.md                  # Project overview
│   ├── 02-architecture.md              # System architecture
│   ├── 03-frontend.md                  # Frontend documentation
│   ├── 04-backend.md                   # Backend documentation
│   ├── 05-database.md                  # Database documentation
│   ├── 06-ai-assistant.md              # AI assistant documentation
│   ├── 07-deployment.md                # Deployment documentation
│   ├── 08-glossary.md                  # Technical glossary
│   └── STORY.md                        # Project story
│
├── demo_data/
│   ├── Imgs.zip                        # Product images
│   └── bulk_upload.xlsx                # Product bulk-upload data
│
├── Client reports/
│   ├── LUXE-AI-Assistant-Presentation.pptx
│   └── LUXE-AI-Assistant-Technical-Design.docx
│
├── Dockerfile                          # Application containerization
├── cloudbuild.yaml                     # GCP build, push and deployment
├── main.py                             # FastAPI application entry point
├── requirements.txt                    # Python dependencies
├── .env.example                        # Environment variable template
├── .dockerignore
├── .gitignore
├── .gitattributes
├── INTEGRATION.md                      # Store integration instructions
├── README.md                           # Project documentation
└── commands.md                         # Useful project commands
