# WEAVE Backend Code Structure Details

This document provides a detailed breakdown of the WEAVE backend architecture, explaining the purpose and responsibilities of each directory and file. The backend is primarily built with Python and FastAPI, designed for scalability and modularity to support the multi-agent orchestration system.

## Root Level (`weave-commerce-os/`)

*   **`backend/`**: Contains all server-side logic, API endpoints, agent implementations, and data integration services.
*   **`frontend/`**: Houses the Next.js application, including UI components, pages, and client-side logic.
*   **`docs/`**: Documentation folder, potentially containing more in-depth explanations, API specifications, and architectural insights.
*   **`.env.example`**: A template file for environment variables, guiding developers on necessary configurations.
*   **`docker-compose.yml`**: Defines the services, networks, and volumes for running the entire WEAVE application using Docker.
*   **`README.md`**: The main project README, as detailed in `GITHUB_README.md`.

## Backend Directory (`weave-commerce-os/backend/`)

This directory is the heart of WEAVE's intelligent operations.

*   **`agents/`**: This subdirectory contains the implementation of each of the 7 specialized AI agents. Each agent is designed to handle specific commerce-related tasks.
    *   **`discovery_agent.py`**: Manages multimodal product search (text, voice, image). It interacts with the `gemini_client` for natural language processing and the `memory` layer for catalog lookup.
    *   **`style_dna_agent.py`**: Builds and utilizes user style profiles (Style DNA). This agent performs complex reasoning using Gemini 2.0 Pro and stores/retrieves data from `memory/firestore_client` and `memory/vector_store`.
    *   **`rescue_agent.py`**: Implements logic for identifying and acting on cart abandonment scenarios. It triggers proactive messages via `integrations/whatsapp_webhook` and may interact with inventory systems.
    *   **`bridge_agent.py`**: Facilitates the digital-to-physical commerce experience, syncing online user data to in-store touchpoints (e.g., POS or associate tablets).
    *   **`family_agent.py`**: Manages collaborative shopping experiences, allowing multiple users to contribute to a shared cart or preference profile.
    *   **`proactive_agent.py`**: Handles trigger-based outreach and personalized notifications (e.g., new arrivals, sales, low stock alerts) to re-engage users.
    *   **`voice_agent.py`**: Dedicated to processing and responding to voice inputs, leveraging Gemini's multimodal audio capabilities and integrating with external voice recognition services if needed.

*   **`memory/`**: This module is responsible for WEAVE's multi-tiered Thread Memory™ system.
    *   **`redis_client.py`**: Manages connections and operations with Redis Stack, used for hot (short-term) memory, session management, and fast caching of conversational context.
    *   **`vector_store.py`**: Interfaces with Vertex AI Vector Search or a similar vector database. It handles the storage and retrieval of vector embeddings for contextual memory, enabling semantic search over past interactions.
    *   **`firestore_client.py`**: Manages interactions with Google Cloud Firestore, serving as the deep (long-term) memory for persistent user profiles, purchase history, Style DNA, and comprehensive interaction logs.

*   **`integrations/`**: This module handles all external API communications and third-party service integrations.
    *   **`gemini_client.py`**: The core client for interacting with the Google Gemini API. It abstracts model selection (Flash vs. Pro), content formatting for multimodal inputs, function tool declaration, and response parsing. It's crucial for all agent intelligence.
    *   **`whatsapp_webhook.py`**: Manages incoming messages from the WhatsApp Business API webhook and sends outgoing messages. It parses WhatsApp payloads and formats responses for the platform.
    *   *(Additional files for other integrations like Razorpay, Shopify, Store POS would reside here)*

*   **`main.py`**: The main entry point for the FastAPI application.
    *   It initializes the FastAPI app instance.
    *   Defines API routes (e.g., `/chat`, `/webhook/whatsapp`, `/health`).
    *   Coordinates the flow of requests: receives user input, passes it to the Gemini Orchestration Layer (via `gemini_client` and agent logic), retrieves responses, and returns them to the frontend or external channels.
    *   Sets up dependency injection and middleware.

*   **`requirements.txt`**: Lists all Python dependencies required for the backend, enabling easy environment setup.

This structured approach ensures that WEAVE's backend is maintainable, scalable, and allows for independent development and testing of each specialized agent and integration.
