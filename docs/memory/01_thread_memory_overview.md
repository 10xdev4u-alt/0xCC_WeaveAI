# Thread Memory™: An Overview

Thread Memory™ is the proprietary, stateful foundation of the WEAVE platform. It is a three-tiered memory architecture designed to provide our AI agents with a complete, nuanced, and persistent understanding of each user. This is how we solve the "Context Amnesia" crisis.

The core principle is to store different types of data in different systems, each optimized for a specific access pattern, speed, and retention requirement.

## The Three Tiers of Memory

| Tier | Name | Purpose | Key Technology | Latency | Retention |
|---|---|---|---|---|---|
| **Tier 1** | **Hot Memory** | Immediate session context | Redis Stack | <5ms | 24 Hours |
| **Tier 2** | **Semantic Memory**| Style DNA & similarity search | Vertex AI Vector Search | <50ms | Persistent |
| **Tier 3** | **Deep Memory** | Complete, permanent user history | Google Cloud Firestore | <100ms | Forever |

This tiered approach ensures that our agents can retrieve the most relevant context with the lowest possible latency.

## How It Works: The Context Builder

When a user interacts with WEAVE, the **Memory Manager**'s `build_context` function is invoked. This "Context Builder" intelligently assembles a rich prompt for Gemini by pulling information from all three tiers:

1.  **Fetch from Hot Memory:** It first grabs the current session data from Redis (e.g., recent messages, active channel, items in cart). This is the fastest and most immediate context.
2.  **Fetch from Semantic Memory:** It then retrieves the user's Style DNA™ vector profile and may perform a semantic search on past conversation summaries to find historically relevant interactions.
3.  **Fetch from Deep Memory:** Finally, it pulls long-term, structured data from Firestore, such as upcoming calendar events, family member information, or detailed purchase history.

All of this information is then formatted and injected into the prompt sent to Gemini, giving the model a panoramic view of the user's past, present, and even future context.

## Architecture Diagram

The diagram below shows how the three tiers work together, managed by the central Memory Manager.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       THREAD MEMORY™ ARCHITECTURE                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                        TIER 1: HOT MEMORY                            │   │
│  │                         (Redis Stack)                                │   │
│  │   Purpose: Immediate session context (TTL: 24h, Latency: <5ms)       │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                      TIER 2: SEMANTIC MEMORY                         │   │
│  │               (Vertex AI Vector Search)                             │   │
│  │   Purpose: Style DNA & similarity (Persistent, Latency: <50ms)       │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                       TIER 3: DEEP MEMORY                            │   │
│  │                         (Firestore)                                  │   │
│  │   Purpose: Complete history (Forever, Latency: <100ms)              │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```
This sophisticated memory system is what allows WEAVE to have a continuous, evolving conversation with every user, making each interaction more intelligent than the last.
