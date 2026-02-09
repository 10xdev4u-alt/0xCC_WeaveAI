# Tier 1: Hot Memory (Redis Stack)

The Hot Memory tier is the fastest layer of Thread Memory™, designed for millisecond-speed access to immediate, ephemeral session data. Its primary role is to provide the AI agents with the instant context needed for fluid, real-time conversations.

## Key Characteristics

*   **Technology:** Redis Stack
*   **Purpose:** Immediate session context
*   **Latency:** < 5ms
*   **Time-to-Live (TTL):** 24 hours (data is automatically expired)

## Data Stored

Hot Memory stores data that is highly relevant to the current user session but does not need to be persisted forever. This includes:

*   **Session State:** The user's current status, including their active channel (`whatsapp`, `web`), detected language (`hi-en`), and the current primary intent (`shopping_wedding_ethnic`).
*   **Active Cart:** The real-time contents of the user's shopping cart.
*   **Recent Messages:** A sliding window of the last 5-10 messages exchanged, allowing the AI to easily refer to the immediate past conversation.
*   **Activity Timestamps:** The timestamp of the user's last activity, used by the Rescue Agent to detect cart abandonment.

## Data Structure in Redis

We use Redis Hashes to store the session state for each user, providing a structured and efficient way to manage session data.

**Key:** `session:{user_id}`

**Fields:**
*   `current_intent`: "shopping_wedding_ethnic"
*   `cart`: JSON string of cart contents `[{"product_id": "...", "size": "M"}]`
*   `active_channel`: "whatsapp"
*   `language`: "hi-en"
*   `context_tokens`: 4500 (An estimate of the current session's token count)
*   `last_activity`: `1678886400` (Unix timestamp)

A separate Redis List is used to maintain the sliding window of recent messages.

**Key:** `session:{user_id}:messages`

**Values:** A list of JSON strings, each representing a message object.

## Role in the System

1.  **Low-Latency Conversations:** When a user sends multiple messages in a row, the Orchestrator can pull the recent message history directly from Redis without needing to query the slower Deep Memory tier. This is crucial for maintaining a natural conversational flow.
2.  **Session Management:** It provides a lightweight way to manage user sessions across different stateless server instances.
3.  **Real-time Triggers:** The `last_activity` timestamp is monitored by our event system. If it's not updated within a certain timeframe, a `cart.abandoned` event is published to the Event Bus, activating the Rescue Agent.

By using an in-memory database like Redis for this tier, we ensure that the most frequently accessed, time-sensitive data is always available at lightning speed.
