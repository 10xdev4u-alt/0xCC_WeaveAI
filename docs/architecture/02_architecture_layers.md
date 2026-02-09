# Architecture: Layers Explained

The WEAVE architecture is composed of six distinct layers, each with a specific purpose. This layered design promotes separation of concerns, enhances security, and allows for independent scaling of components.

| Layer | Purpose | Key Technologies |
|---|---|---|
| **User Layer** | Provides the entry points for all customer interactions. This is the "face" of WEAVE. | WhatsApp Business API, Next.js (Web App), Store Kiosk (PWA), Voice IVR |
| **API Gateway Layer** | Acts as the secure front door for all incoming traffic. It handles authentication, rate limiting, and routing to protect our backend services. | Google Cloud Run (API Gateway), Google Cloud Load Balancer, Google Cloud Armor (WAF/DDoS Protection) |
| **Orchestration Layer** | The central AI brain of the entire system. It receives requests from the gateway, understands intent, and delegates tasks to the appropriate agents. | Google Gemini 2.0 Flash |
| **Agent Layer** | A pool of specialized AI agents that handle specific tasks. Each agent is an expert in its domain (e.g., product discovery, cart rescue). | Google Gemini 2.0 (Flash & Pro), Custom Python Classes |
| **Memory Layer** | The stateful foundation of WEAVE. This three-tiered system, known as **Thread Memory™**, provides persistent context for all interactions. | Redis Stack (Hot Memory), Vertex AI Vector Search (Semantic Memory), Google Cloud Firestore (Deep Memory) |
| **Event Bus Layer** | A messaging backbone that enables asynchronous communication between system components. This decouples services and allows for event-driven workflows. | Google Cloud Pub/Sub |
| **Integration Layer**| Manages all communication with external, third-party services and APIs, from payment gateways to e-commerce platforms. | Various REST and gRPC clients |

## 1. User Layer
This is the presentation layer where users interact with WEAVE. It is designed to be channel-agnostic, meaning the core WEAVE intelligence can be surfaced through any interface. Our primary touchpoints are WhatsApp, a rich web application, in-store kiosks, and voice-based systems.

## 2. API Gateway Layer
All traffic from the User Layer is routed through a secure and scalable API Gateway. This layer is responsible for:
*   **Security:** Authentication, authorization, and protection against common web vulnerabilities (DDoS, SQLi) via Cloud Armor.
*   **Traffic Management:** Rate limiting to prevent abuse and load balancing to distribute traffic across backend services.
*   **Routing:** Directing incoming requests to the appropriate internal service, primarily the Orchestration Layer.

## 3. Orchestration Layer
This is the heart of WEAVE, powered by Gemini 2.0 Flash. Its sole responsibility is to act as the central AI coordinator. It receives a user request, understands the intent, assembles the necessary context from the Memory Layer, and routes the task to the correct agent in the Agent Layer.

## 4. Agent Layer
This layer contains the specialized "minds" of WEAVE. It's a pool of seven distinct AI agents, each with a single responsibility. For example, the **Discovery Agent** is an expert at search, while the **Rescue Agent** is an expert at recovering abandoned carts. This separation allows us to develop and improve each agent's expertise independently.

## 5. Memory Layer (Thread Memory™)
This is what makes WEAVE stateful. Our unique three-tiered memory architecture ensures we have the right context available at the right speed.
*   **Hot Memory (Redis):** For millisecond access to immediate session data.
*   **Semantic Memory (Vertex AI):** For fast, meaning-based searches of user preferences and style profiles.
*   **Deep Memory (Firestore):** For permanent storage of the entire user journey.

## 6. Event Bus Layer
Using Google Cloud Pub/Sub, this layer allows our system to communicate asynchronously. When a user abandons a cart, a `cart.abandoned` event is published. The Rescue Agent, subscribed to this topic, can then act on it without blocking the main request-response flow. This makes the system incredibly scalable and resilient.

## 7. Integration Layer
This layer isolates the complexity of dealing with third-party APIs. It contains dedicated clients for services like Shopify (commerce), Razorpay (payments), and the WhatsApp Business API. If an external API changes, we only need to update the corresponding client in this layer, leaving our core business logic untouched.
