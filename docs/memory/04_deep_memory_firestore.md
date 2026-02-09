# Tier 3: Deep Memory (Google Cloud Firestore)

The Deep Memory tier is WEAVE's system of record. It is a highly structured, persistent database that stores the complete and permanent history of every user's journey. While Hot Memory is for speed and Semantic Memory is for understanding, Deep Memory is for **truth**.

## Key Characteristics

*   **Technology:** Google Cloud Firestore
*   **Purpose:** Complete, persistent user history and structured data.
*   **Latency:** < 100ms
*   **Retention:** Forever (or until a user requests data deletion).

## Data Stored (NoSQL Collections)

We use Firestore's document/collection model to store rich, structured data for each user. This provides flexibility while maintaining strong query capabilities.

**Top-Level Collection:** `users`

**Document:** `{user_id}`

Within each user document, we have fields and sub-collections:

*   **`profile` (Map):**
    *   `name`: "Priya Sharma"
    *   `phone`: "+91..."
    *   `email`: "priya@example.com"
    *   `created_at`: Timestamp
*   **`preferences` (Map):**
    *   `language`: "hi-en"
    *   `notifications`: `{"whatsapp": true, "sms": false}`
    *   `preferred_channel`: "whatsapp"
*   **`style_dna` (Map):**
    *   The full, structured JSON object of the user's Style DNA profile.
*   **`family` (Array of Maps):**
    *   `[{member_id, relationship, role}]`

---

**Sub-Collection:** `users/{user_id}/purchases`

*   **Document:** `{purchase_id}`
*   **Fields:**
    *   `items`: Array of product objects.
    *   `total_amount`: 7999.00
    *   `purchase_date`: Timestamp
    *   `purchase_channel`: "web"
    *   `status`: "delivered"

---

**Sub-Collection:** `users/{user_id}/conversations`

*   **Document:** `{conversation_id}`
*   **Fields:**
    *   `channel`: "whatsapp"
    *   `start_time`: Timestamp
    *   `messages`: Array of message objects (for full historical record).
    *   `summary`: A Gemini-generated summary of the conversation.
    *   `final_intent`: The primary intent of the conversation.

---

**Sub-Collection:** `users/{user_id}/events`

*   **Document:** `{event_id}`
*   **Fields:**
    *   `type`: "wedding", "vacation"
    *   `date`: Date of the event.
    *   `description`: "Friend's sangeet in Goa"
    *   `mentioned_on`: Timestamp when the user mentioned this.
    *   `source`: "conversation"

---

**Top-Level Collection:** `family_carts`

*   **Document:** `{cart_id}`
*   **Fields:**
    *   Details of the shared cart, including `members`, `items`, `votes`, and `status`.

## Role in the System

1.  **Source of Truth:** Deep Memory is the definitive historical record. It's used for auditing, deep analytics, and regenerating semantic embeddings if needed.
2.  **Structured Data for Agents:** When the Proactive Agent needs to know about an upcoming event, or the Family Sync Agent needs to retrieve roles, they query Firestore directly for this structured data.
3.  **Data for Style DNA Generation:** The Style DNA Agent's most critical inputs—purchase history and return data—are stored here.
4.  **User Profile Management:** All user-facing profile and preference information is managed within this tier.

By using a scalable, serverless NoSQL database like Firestore, we can store vast amounts of complex, structured data reliably and cost-effectively, ensuring that WEAVE's long-term memory is both comprehensive and durable.
