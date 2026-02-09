# WEAVE Architecture Diagram Description

This document details the architectural components and data flow within the WEAVE Commerce Intelligence OS. The system is designed as a multi-layered, agent-based orchestration platform, leveraging Gemini 2.0 for intelligent commerce interactions across various user touchpoints.

## 1. User Touchpoints

These are the primary interfaces through which end-users interact with the WEAVE system. The architecture is designed to be channel-agnostic, providing a consistent experience across diverse platforms.

*   **WhatsApp:** Integrates with the WhatsApp Business API to enable conversational commerce, proactive outreach, and customer support directly within the user's preferred messaging app.
*   **Web App:** A Next.js-based frontend providing a rich, interactive web experience for browsing products, managing carts, and engaging with the AI agents.
*   **Store Kiosk:** Physical in-store touchpoints (e.g., tablets or smart displays) that bridge the digital and physical shopping experiences, powered by the same backend intelligence.

## 2. Gemini Orchestration Layer

This is the intelligent core of WEAVE, powered by Google's Gemini 2.0 Flash model. It acts as a central router and natural language understanding engine, directing user requests to the appropriate specialized agents.

*   **Gemini 2.0 Flash:** Chosen for its real-time performance (<500ms response times), it handles:
    *   **Intent Detection:** Analyzing user input to understand their goal (e.g., "browse products," "check order status," "get style advice").
    *   **Language Understanding:** Processing natural language queries, including Hinglish and various Indian languages, to extract key entities and context.
    *   **Agent Routing:** Based on the detected intent, it intelligently routes the request to one of the 7 specialized AI agents.
    *   **Response Generation:** Synthesizing natural, context-aware, and personalized responses back to the user, often incorporating outputs from the agents.

## 3. Seven Specialized Agents

WEAVE employs a fleet of highly specialized AI agents, each designed to handle a specific aspect of the commerce journey. These agents embody the core business logic and AI capabilities of the platform.

*   **Discovery Agent:** Facilitates multimodal product search (text, voice, image), helping users find relevant products efficiently.
*   **Style DNA Agent:** Analyzes user preferences, past purchases, and expressed interests to build a 512-dimensional style profile, providing personalized recommendations and accurate sizing predictions. This agent heavily utilizes Gemini 2.0 Pro for complex reasoning.
*   **Rescue Agent:** Monitors user behavior for signs of cart abandonment and proactively intervenes with personalized offers, reminders, or assistance to recover lost sales.
*   **Bridge Agent:** Seamlessly connects online browsing with in-store experiences, enabling features like in-store pickup, associate briefing, and personalized recommendations during physical visits.
*   **Family Sync Agent:** Supports collaborative, multi-player shopping experiences, allowing families or groups to share carts and preferences.
*   **Proactive Agent:** Triggers personalized outreach based on various events (e.g., new arrivals, sales, low stock alerts for desired items), driving re-engagement and sales.
*   **Voice Agent:** Provides robust support for 12 Indian languages, enabling voice-based interactions and ensuring inclusivity for a diverse user base.

## 4. Thread Memory™ Layer

This multi-tiered memory system ensures cross-session memory persistence and contextual understanding for every user interaction, solving the "Context Amnesia" problem.

*   **Redis (Hot Memory):** Utilized for fast, in-memory caching of short-term conversational context, user session data, and frequently accessed information to ensure real-time responsiveness.
*   **Vertex AI Vector Search (Contextual Memory):** Stores embeddings of past conversations, style DNA profiles, and product interactions. Enables semantic search and retrieval of highly relevant context for agents.
*   **Firestore (Deep Memory):** Serves as the persistent long-term storage for comprehensive user profiles, purchase history, style DNA, preferences, and agent interaction logs. Provides a durable, scalable record of every customer journey.

## 5. Integrations

WEAVE connects with various external systems to provide a full-fledged commerce solution.

*   **WhatsApp API:** Powers the WhatsApp touchpoint for sending and receiving messages.
*   **Razorpay:** Handles payment processing, enabling secure and diverse payment options for users.
*   **Shopify:** Integrates with existing e-commerce platforms for inventory management, product catalog synchronization, and order fulfillment.
*   **Store POS (Point of Sale):** Connects with in-store systems to facilitate real-time inventory updates, associate assistance, and seamless digital-to-physical handoffs.

This layered architecture ensures scalability, modularity, and the ability to continuously evolve the WEAVE platform with new agents and integrations while maintaining a rich, personalized user experience.
