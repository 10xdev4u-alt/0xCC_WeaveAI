<div align="center">
  <img src="pics/logo.png" alt="WEAVE Logo" width="150">
  
  # 🧵 WEAVE
  ### India's First Gemini-Native Commerce Intelligence OS
  
  [![Gemini 3 Hackathon](https://img.shields.io/badge/Gemini%203-Hackathon%20Submission-6366F1?style=for-the-badge&logo=google-gemini)](https://devpost.com)
  [![Built with Gemini 2.0](https://img.shields.io/badge/Powered%20by-Gemini%202.0-8B5CF6?style=for-the-badge&logo=google-gemini)](https://ai.google.dev)
  [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
  ![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)
  ![FastAPI](https://img.shields.io/badge/FastAPI-0.110.0-009688?style=for-the-badge&logo=fastapi)
  ![Next.js](https://img.shields.io/badge/Next.js-14+-black?style=for-the-badge&logo=next.js&logoColor=white)
  ![Google Cloud](https://img.shields.io/badge/Google%20Cloud-Platform-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
  ![Poetry](https://img.shields.io/badge/Poetry-Python%20PM-60A5FA?style=for-the-badge&logo=poetry&logoColor=white)

  **We're not just building a platform; we're weaving a soul into the fabric of digital commerce.**
  
  [🚀 LIVE DEMO](https://weaveaix.web.app/) • [🎬 Video Walkthrough](https://vimeo.com/1163333819?share=copy&fl=sv&fe=ci#t=0) • [🏆 Devpost Project](https://devpost.com) • [📚 Full Docs](docs/01_introduction.md)
</div>

---

## 🚨 The ₹52,000 Crore Problem: Commerce Has Lost Its Soul

Indian e-commerce is a transactional engine, not a human experience. This disconnect creates a massive, bleeding wound in the market:
- **Channel Amnesia:** 72% of conversations and carts are abandoned, forgotten by systems that don't remember.
- **Language Barriers:** 240 million potential customers are excluded, their voices unheard.
- **Sizing Chaos:** 35% of all fashion apparel is returned, a logistical and environmental nightmare.
- **Isolated Shopping:** Carts are built for individuals, ignoring the 73% of purchases influenced by family.

## 💡 The Solution: WEAVE - A Sentient Commerce OS

**WEAVE** is a multi-agent, event-driven orchestration system, built natively on Gemini 2.0, that gives commerce a persistent memory, a multilingual voice, and an intelligent heart. It's a single AI brain that unifies WhatsApp, Web, and Physical Stores into one seamless conversation.

---
## 🏗️ Enterprise-Grade Architecture

WEAVE is designed for high-performance, scalability, and resilience, featuring a multi-layered architecture that separates concerns and optimizes data flow.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              USER LAYER                                     │
│ ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐                   │
│ │ WhatsApp │   │ Web App  │   │  Store   │   │  Voice   │                   │
│ │ Business │   │ (Next.js)│   │  Kiosk   │   │   IVR    │                   │
│ └────┬─────┘   └────┬─────┘   └────┬─────┘   └────┬─────┘                   │
└──────┼───────────────┼───────────────┼───────────────┼──────────────────────┘
       └───────────────┴───────────────┴───────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           API GATEWAY LAYER                                 │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │                  API Gateway (Cloud Run) + LB + WAF                     │ │
│ │         • Rate Limiting  • Auth  • Request Routing  • DDoS Protection   │ │
│ └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        ORCHESTRATION LAYER                                  │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │                         GEMINI 2.0 FLASH                                │ │
│ │      (Intent Classification, Context Injection, Agent Routing)          │ │
│ └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AGENT LAYER                                       │
│    [Discovery] [Style DNA] [Rescue] [Bridge] [Family] [Proactive] [Voice]   │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          MEMORY LAYER (Thread Memory™)                      │
│ ┌─────────────────┐   ┌───────────────────┐   ┌─────────────────┐           │
│ │   HOT (<5ms)    │   │  SEMANTIC (<50ms) │   │   DEEP (<100ms) │           │
│ │   (Redis)       │   │  (Vertex AI)      │   │   (Firestore)   │           │
│ └─────────────────┘   └───────────────────┘   └─────────────────┘           │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         EVENT BUS LAYER                                     │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │                           Cloud Pub/Sub                                 │ │
│ │ (user.message.received, cart.abandoned, payment.completed, etc.)        │ │
│ └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        INTEGRATION LAYER                                    │
│       [WhatsApp API] [Razorpay] [Shopify] [Store POS] [Google APIs]         │
└─────────────────────────────────────────────────────────────────────────────┘
```
---

## 🧠 Why Gemini 2.0 is Our Unfair Advantage

WEAVE's architecture is built to leverage the unique, critical capabilities of Gemini 2.0. No other model offers the combination of features necessary to solve the "Context Amnesia" problem at scale.

| Capability               | Why Critical for WEAVE                                | Alternative?                                      |
|--------------------------|-------------------------------------------------------|---------------------------------------------------|
| **2M Token Context**     | Remember months of conversations across channels.     | ❌ **None.** This is a generational leap.           |
| **Native Multimodal**    | User sends voice + photo in a single API call.        | ❌ Requires complex, high-latency, separate pipelines. |
| **Multilingual Reasoning**| Understand intent in "Hinglish," not just translate.  | ❌ Translation APIs lose critical context and nuance. |
| **<500ms Latency (Flash)** | Instant, human-like responses on chat.                | ✓ Flash model is optimized for this core use case.     |
| **Native Function Calling**| A single message can trigger inventory + payment.     | ❌ Requires brittle, custom orchestration logic.      |
| **Advanced Reasoning (Pro)**| Powers the deep analysis for our Style DNA agent.   | ✓ Pro model provides the necessary cognitive depth.  |

---
## 🐍 Core Orchestration Logic

Our system is built on a clean, testable, and scalable Python backend, with a central orchestrator coordinating agents and memory.

### The Gemini Orchestrator
```python
class GeminiOrchestrator:
    """
    Central AI brain that coordinates all WEAVE operations.
    
    Responsibilities:
    - Intent classification from multimodal input
    - Context assembly from 3-tier memory
    - Agent selection and routing
    - Response generation in user's language
    - Function call orchestration
    """
    async def process(self, user_input: UserInput) -> Response:
        # 1. Detect language and intent with Gemini
        intent = await self.classify_intent(user_input)
        
        # 2. Build context from Thread Memory™
        context = await self.memory.build_context(user_id=user_input.user_id, intent=intent)
        
        # 3. Route to the appropriate specialized agent
        agent = self.agents.select(intent)
        
        # 4. Generate a response using the agent's logic and Gemini's capabilities
        response = await agent.execute(user_input=user_input, context=context)
        
        # 5. Persist the new interaction back into memory
        await self.memory.update(user_input, response)
        
        return response
```

### The Agent Base Class
All our agents inherit from a base class that provides common functionality for interacting with Gemini.
```python
class BaseAgent:
    """
    Base class for all WEAVE agents. Each agent has:
    - A specialized system prompt defining its personality and goals.
    - Access to a specific set of functions (tools).
    - Custom logic for handling its domain.
    """
    def __init__(self, name: str, system_prompt: str, functions: List[str]):
        self.name = name
        self.system_prompt = system_prompt
        self.functions = functions
        self.gemini_client = WeaveGeminiClient()

    async def execute(self, user_input: UserInput, context: Context) -> Response:
        # Build a rich, contextual prompt for Gemini
        prompt = self.build_prompt(user_input, context)
        
        # Call Gemini with the agent's specific system prompt and tools
        response = await self.gemini_client.generate(
            model="gemini-2.0-flash",
            system_prompt=self.system_prompt,
            prompt=prompt,
            functions=self.functions
        )
        
        # Process response, handle function calls, and format output
        return self.format_response(response)
```

---

## 🚀 Getting Started

### Prerequisites
- Python 3.11+ & Poetry
- Node.js 18+ & PNPM
- Google Cloud Account & Gemini API Key
- Docker for local services (Redis)

### Installation & Setup

1.  **Clone the repository:**
    ```bash
    git clone https://codeberg.org/princetheprogrammerbtw/WeaveAI.git
    cd WeaveAI
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

4.  **Launch Frontend (Next.js):**
    ```bash
    cd ../frontend
    pnpm install
    pnpm dev
    ```

---

## 📚 Full Documentation

This project is documented extensively. For deep dives into our architecture, agent design, memory systems, and API specifications, please explore the `/docs` directory or start with the [Introduction](docs/01_introduction.md).

---

## 👥 The Team: Team WEAVE

Meet the architects, builders, and visionaries behind WEAVE.

| Member                   | Role                     |
|--------------------------|--------------------------|
| **princetheprogrammerbtw** | 👑 R&D Lead & Lead Dev   |
| **Harish K**               | 🛠️ Developer & Admin     |
| **Jai Ganesh**             | 🧠 AI/ML Engineer        |
| **Bivin Kanth**            | 🎨 UI/UX Developer       |
| **Mukilash V K**           | 🔧 Backend Engineer      |
| **Rithic Hitesh**          | 📊 Integration & QA      |

<div align="center">
  Built with ❤️ for the Gemini 3 Hackathon
</div>
