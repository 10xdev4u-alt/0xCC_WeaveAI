# WEAVE Architecture: The Anatomy of a Sentient System

This document provides a narrative description of the WEAVE architecture. Our system is not a simple collection of services; it's a purposefully designed, multi-layered ecosystem built to support a continuous, intelligent conversation with the user.

---

### The Core Philosophy: "A Conversation, Not a Transaction"

The architecture is designed to fulfill one core promise: a user has a *single, unbroken conversation* with WEAVE, regardless of whether they are on WhatsApp, a web browser, or an in-store tablet. This is achieved through three foundational pillars: **Real-time Orchestration**, **Specialized Intelligence**, and **Persistent Memory**.

---

### The Journey of a Single Query

Let's trace a user's query—"Bhai, do you have that blue kurta I was looking at yesterday?"—as it travels through the WEAVE nervous system.

#### **1. Entry Point: The User Touchpoints**

The query originates from one of our channel-agnostic frontends:
*   **WhatsApp:** The most accessible entry point for millions.
*   **Web App (Next.js):** A rich, interactive visual interface.
*   **Store Kiosk:** A tablet that bridges the physical-digital divide.

Regardless of the source, the query is securely transmitted to our central nervous system: the Gemini Orchestration Layer.

#### **2. The Brain: Gemini Orchestration Layer**

This is the intelligent core where every request is processed in real-time.
*   **Powered by Gemini 2.0 Flash:** We use Flash for its sub-500ms latency, which is critical for a conversational flow that feels natural and human.
*   **Initial Analysis:** Gemini 2.0 Flash performs three tasks simultaneously:
    1.  **Language Detection:** Identifies the language as "Hinglish."
    2.  **Intent Recognition:** Understands the core intent is not just "search" but "retrieve a previously viewed item" (`lookup_history`).
    3.  **Entity Extraction:** Extracts key entities: `item: "kurta"`, `color: "blue"`, `timeframe: "yesterday"`.
*   **Agent Routing:** Based on the complex intent (`lookup_history` + `search`), the orchestrator knows this is a job for **The Seeker (Discovery Agent)**. The request, now enriched with structured data, is dispatched to the appropriate agent.

#### **3. The Specialists: The 7 AI Agents (The Weavers)**

The Seeker agent receives the routed request. Its job is not just to search, but to understand the user's *unspoken context*.

*   **Contextual Invocation:** Before acting, the Seeker queries the **Thread Memory™ Layer**.
*   **Memory Retrieval:** It fetches the user's profile, including their `Style DNA` (from The Stylist), and their recent interaction history from yesterday.
*   **Gemini-Powered Action:** The Seeker now has the full picture. It uses **Gemini's Function Calling** capability to execute a precise, memory-augmented query against our backend: `search_catalog(user_id, item="kurta", color="blue", viewed_since="24h_ago")`.

#### **4. The Soul: Thread Memory™ Layer**

This is what makes WEAVE sentient. It's a multi-tiered memory system that ensures no context is ever lost.
*   **🔥 Redis (Hot Memory):** Holds the immediate conversational context (the last 5-10 messages) for millisecond-speed retrieval during rapid back-and-forth exchanges.
*   **🧠 Vertex AI Vector Search (Semantic Memory):** Contains the user's **Style DNA** and vector embeddings of all past interactions. This allows the agent to know *which* blue kurta the user likely meant, based on their aesthetic preferences.
*   **🗄️ Firestore (Deep Memory):** The system of record. It stores the complete, immutable log of every interaction, purchase, and preference for deep historical analysis and long-term personalization.

#### **5. The Hands: Integrations & Tools**

The `search_catalog` function is one of many "tools" available to our agents. This layer connects WEAVE's intelligence to the real world.
*   **Internal Tools:** `search_catalog`, `check_inventory`, `get_user_profile`.
*   **External APIs:**
    *   **Shopify:** To get real-time product information.
    *   **Razorpay:** To initiate a payment.
    *   **Store POS:** To reserve an item for in-store trial.

#### **6. The Response: Completing the Loop**

*   **Action Result:** The `search_catalog` function returns the specific blue kurta the user viewed.
*   **Final Generation:** This result is passed back to the **Gemini Orchestration Layer**. Gemini 2.0 Flash synthesizes the final, natural language response: "Yes, PrinceTheProgrammer! You mean this one? I can have it held for you at the Jubilee Hills store if you'd like."
*   **Delivery:** The response is delivered back to the user on their original touchpoint (e.g., WhatsApp). The entire journey, from query to response, feels like a single, intelligent thought. The conversation continues, its memory fully intact.