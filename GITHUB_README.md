<div align="center">
  <img src="https://raw.githubusercontent.com/Moolsh/Moolsh/main/logo.png" alt="WEAVE Logo" width="150">
  
  # 🧵 WEAVE
  ### India's First Gemini-Native Commerce Intelligence OS
  
  [![Gemini 3 Hackathon](https://img.shields.io/badge/Gemini%203-Hackathon%20Submission-6366F1?style=for-the-badge&logo=google-gemini)](https://devpost.com)
  [![Built with Gemini 2.0](https://img.shields.io/badge/Powered%20by-Gemini%202.0-8B5CF6?style=for-the-badge&logo=google-gemini)](https://ai.google.dev)
  [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)

  **We're not just building a platform; we're weaving a soul into the fabric of digital commerce.**
  
  [Live Demo](https://weave-demo.vercel.app) • [Video Walkthrough](https://youtube.com) • [Devpost Project](https://devpost.com)
</div>

---

## 🚨 The ₹52,000 Crore Problem: Commerce Has Lost Its Soul

Indian e-commerce is a transactional engine, not a human experience. This disconnect creates a massive, bleeding wound in the market:
- **Channel Amnesia:** 72% of conversations and carts are abandoned, forgotten by systems that don't remember.
- **Language Barriers:** 240 million potential customers are excluded, their voices unheard.
- **Sizing Chaos:** 35% of all fashion apparel is returned, a logistical and environmental nightmare.
- **Isolated Shopping:** Carts are built for individuals, ignoring the 73% of purchases influenced by family.

## 💡 The Solution: WEAVE - A Sentient Commerce OS

**WEAVE** is a multi-agent orchestration system, built natively on Gemini 2.0, that gives commerce a persistent memory, a multilingual voice, and an intelligent heart. It's a single AI brain that unifies WhatsApp, Web, and Physical Stores into one seamless conversation.

<div align="center">
  <img src="docs/screenshots/hero.png" width="80%">
</div>

---

## 🧠 The Weavers: Our 7 Gemini-Powered AI Agents

WEAVE's intelligence is distributed across seven specialized agents, orchestrated by Gemini 2.0 Flash in real-time.

| Agent | Codename | Core Function |
|-------|----------|---------------|
| **Discovery Agent** | `The Seeker` | Goes beyond keyword search to understand intent, context, and emotion. Finds what you mean, not just what you type. |
| **Style DNA Agent** | `The Stylist` | Creates a 512-dimensional "style fingerprint" for each user, powered by Gemini 2.0 Pro's deep reasoning. It predicts taste and perfect sizing. |
| **Rescue Agent** | `The Guardian` | Monitors conversations for signs of abandonment and intelligently intervenes with context-aware messages to recover the sale. |
| **Bridge Agent** | `The Courier` | Seamlessly transports a user's digital session (cart, Style DNA, chat history) to an in-store tablet for a true omnichannel experience. |
| **Family Sync Agent** | `The Elder` | Facilitates collaborative shopping by creating shared carts and preference profiles for group decision-making. |
| **Proactive Agent** | `The Herald` | An event-driven agent that re-engages users with hyper-personalized triggers—like a back-in-stock notification for an item they viewed weeks ago. |
| **Voice Agent** | `The Linguist` | Leverages Gemini's multimodal capabilities to understand and converse fluently in 12 Indian languages, including idiomatic Hinglish. |

---

## 🚀 The Gemini Native Architecture

WEAVE was not built *with* AI; it was built *for* AI. Gemini is not a feature; it is the foundation.

```mermaid
graph TD
    subgraph User Touchpoints
        A[📱 WhatsApp]
        B[💻 Web App]
        C[🏪 Store Kiosk]
    end

    subgraph Gemini Orchestration Layer
        D(Gemini 2.0 Flash);
        D -- "Intent & Routing" --> E;
        D -- "Real-time Response" --> A & B & C;
    end

    subgraph 7 Specialized Agents
        E{The Weavers};
        E -- "Complex Reasoning" --> F(Gemini 2.0 Pro for Style DNA);
    end

    subgraph Thread Memory™ Layer
        G[🔥 Redis - Hot Context];
        H[🧠 Vertex AI Vectors - Semantic Memory];
        I[🗄️ Firestore - Deep History];
    end
    
    subgraph Integrations & Tools
        J[Shopify API]
        K[Razorpay API]
        L[Store POS API]
    end

    A & B & C --> D;
    E --> G & H & I;
    E -- "Function Calling" --> J & K & L;
```

### Code Example: The Heart of the `Discovery Agent`

This demonstrates how we combine context from our **Thread Memory™** layer with a user's query and Gemini's native function calling to create a truly intelligent response.

```python
# backend/agents/discovery_agent.py (Illustrative)
from google import genai
from ..integrations import gemini_client, available_tools
from ..memory import thread_memory_manager

async def handle_query(user_id: str, user_input: dict):
    """
    Handles a new user query using the Discovery Agent.
    """
    # 1. Retrieve deep context from our Thread Memory™ layer
    user_context = await thread_memory_manager.retrieve_context(user_id)
    
    # 2. Formulate a rich prompt for Gemini
    prompt = f"""
    You are WEAVE's 'Seeker' agent. A user's context is provided below.
    Your goal is to understand their true intent and find the perfect product.
    
    ## User Context:
    - Style DNA (Vector Profile): {user_context.style_dna_summary}
    - Language: {user_context.language}
    - Recent History: {user_context.recent_interactions}
    
    ## User's Live Query:
    - Message: "{user_input.get('message')}"
    """
    
    # 3. Generate content using Gemini, providing our toolkit
    response = gemini_client.generate_content(
        model="gemini-2.0-flash",
        prompt=prompt,
        image=user_input.get('image'), # Natively handle multimodal input
        tools=available_tools # Expose our backend functions to Gemini
    )
    
    # 4. Process Gemini's response (could be text or a function call)
    # ... logic to execute tool calls or format response ...
    
    return processed_response
```

---

## 🛠️ Getting Started

### Prerequisites
- Python 3.11+ & Poetry
- Node.js 18+ & PNPM
- Google Cloud Account & Gemini API Key
- Docker for local services (Redis)

### Installation & Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/[your-username]/weave-commerce-os.git
    cd weave-commerce-os
    ```

2.  **Configure Environment:**
    ```bash
    cp .env.example .env
    # Edit .env with your API keys (GEMINI_API_KEY, etc.)
    ```

3.  **Launch Backend (FastAPI):**
    ```bash
    cd backend
    poetry install
    poetry run uvicorn main:app --reload
    ```
    The backend will be live at `http://localhost:8000`.

4.  **Launch Frontend (Next.js):**
    ```bash
    cd ../frontend
    pnpm install
    pnpm dev
    ```
    The frontend will be live at `http://localhost:3000`.

---

## 📊 The WEAVE Effect: Revolutionizing Retail Metrics

| Metric             | Before WEAVE | After WEAVE (Projected) | Improvement |
|--------------------|--------------|-------------------------|-------------|
| Cart Abandonment   | 72%          | 38%                     | **↓47%**    |
| Returns (Sizing)   | 35%          | 22%                     | **↓37%**    |
| Avg. Order Value   | ₹2,800       | ₹3,640                  | **↑30%**    |
| Tier 2/3 Engagement| 18%          | 38%                     | **↑111%**   |

**This translates to a projected 45,000% ROI for our enterprise partners.**

---

## 👥 The Team: Team WEAVE

Meet the architects, builders, and visionaries behind WEAVE.

| Member                   | Role                     | Specialty                  |
|--------------------------|--------------------------|----------------------------|
| **princetheprogrammerbtw** | 👑 R&D Lead & Lead Dev   | Architecture, Innovation   |
| **Harish K**               | 🛠️ Developer & Admin     | Project Management, Dev Ops|
| **Jai Ganesh**             | 🧠 AI/ML Engineer        | Gemini Integration, Agents |
| **Bivin Kanth**            | 🎨 UI/UX Developer       | Design Systems, Frontend   |
| **Mukilash V K**           | 🔧 Backend Engineer      | APIs, Memory Systems       |
| **Rithic Hitesh**          | 📊 Integration & QA      | Testing, Documentation     |

<div align="center">
  **Built with ❤️ for the Gemini 3 Hackathon**
</div>