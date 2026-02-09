# WEAVE Backend Architecture: A Blueprint for AI-Native Systems

This document outlines the engineering philosophy and structure of the WEAVE backend. Our approach is rooted in principles of modularity, scalability, and testability, ensuring we can build, maintain, and evolve a complex, AI-driven system with clarity and confidence.

---

## Engineering Philosophy: The "Clean Core" Principle

The backend is designed around a **"Clean Core"** principle. The core business logic and AI orchestration are completely decoupled from the delivery mechanisms (like FastAPI) and external services (like Redis or Firestore). This separation of concerns is critical for long-term maintainability and agility.

*   **Core Logic (`agents/`, `memory/`):** This is the "brain." It knows nothing about HTTP requests or specific database technologies. It deals only in pure Python objects and business rules.
*   **Infrastructure (`integrations/`, `main.py`):** This is the "scaffolding." It handles web traffic, talks to databases, and manages external API calls. It adapts data from the outside world into a format the core can understand.

---

## Directory Breakdown: From Entrypoint to Core

### `main.py`: The Central Nervous System

This is the primary entry point for the FastAPI application, but its role is intentionally limited. It acts as a lightweight controller, responsible for:

1.  **Receiving Requests:** Defining API endpoints (`/chat`, `/webhook/whatsapp`) and parsing incoming data.
2.  **Delegating to the Core:** Immediately passing the request data to the appropriate agent or service in the core application. It does *not* contain any business logic itself.
3.  **Returning Responses:** Taking the pure Python response from the core and serializing it into a JSON response for the client.

**Why this design?** It makes our core logic completely independent of the web framework. We could switch from FastAPI to another framework with minimal changes to the actual business intelligence.

### `agents/`: The Specialized Cognitive Modules

This directory is the heart of WEAVE's intelligence. Each file represents a "specialized cognitive module," an independent domain expert.

*   **`discovery_agent.py`, `style_dna_agent.py`, etc.:**
    *   **Defined Inputs/Outputs:** Each agent has a clear "contract"—it accepts specific data structures and returns a predictable result or action.
    *   **Dependency Inversion:** Agents do not directly instantiate their dependencies (like memory clients or Gemini clients). Instead, these are *injected* into them. This makes testing trivial; we can inject a "mock" memory client to test an agent in complete isolation.
    *   **Focused Responsibility:** The `StyleDNAAgent` only knows about style. The `RescueAgent` only knows about cart recovery. This prevents a "god object" monolith and allows our team to work on different agents in parallel.

### `memory/`: The Abstracted Memory Layer

This module provides an abstraction over our multi-tiered memory system. The agents do not know if they are talking to Redis, Firestore, or a local dictionary.

*   **`thread_memory_manager.py`:** Provides a high-level interface like `retrieve_context(user_id)` or `update_history(user_id, interaction)`.
*   **`redis_client.py`, `firestore_client.py`, `vector_store.py`:** These are the *implementations* of that interface. The `thread_memory_manager` decides whether to fetch data from the hot cache (Redis) or deep history (Firestore), but the agent requesting the data is unaware of this complexity.

**Why this design?** This abstraction allows us to swap out our entire database backend without changing a single line of agent code. We can start with a simple in-memory cache for prototyping and scale to a full Redis/Firestore implementation for production.

### `integrations/`: The Bridge to the Outside World

This module contains the "glue" that connects WEAVE to external services.

*   **`gemini_client.py`:** A dedicated, robust client for all Gemini API interactions. It handles authentication, error handling, retries, and the complexities of formatting prompts for multimodal inputs and function calling. It exposes a clean, internal API like `gemini_client.generate_content(...)` to the rest of the application.
*   **`whatsapp_webhook.py`:** Manages the specific data formats and authentication requirements of the WhatsApp Business API. It translates incoming WhatsApp webhooks into a clean, internal `UserInput` object that the rest of the system can understand.
*   **`[service]_client.py`:** Every external service (Razorpay, Shopify, etc.) gets its own dedicated client.

**Why this design?** It isolates the messiness of external APIs. If WhatsApp changes its API format, we only have to update `whatsapp_webhook.py`; the core agent logic remains untouched.

### `core/` (Hypothetical but important)

While not explicitly listed in the initial structure, a mature version of this architecture would include a `core/` directory.
*   **`core/schemas.py` or `core/models.py`:** This would define the pure Python data structures that are passed between all layers of the application (e.g., `User`, `Product`, `Interaction`). This ensures data consistency and provides a single source of truth for our application's data model.

This disciplined, layered architecture is the key to building a system as ambitious as WEAVE. It ensures that as the intelligence of our agents grows, the complexity of the system remains manageable, testable, and ready for scale.