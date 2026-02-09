# Memory Manager Implementation

The `MemoryManager` is a sophisticated Python class that acts as the central nervous system for Thread Memory™. It provides a unified interface for the AI agents to interact with all three tiers of memory, and it houses the critical `build_context` method that assembles the rich, multi-layered context for Gemini.

## Core Responsibilities

*   **Abstraction:** Provides a single, high-level API for memory operations, hiding the complexity of whether data lives in Redis, Vertex AI, or Firestore.
*   **Context Assembly:** Intelligently gathers and formats data from all three memory tiers to build a comprehensive context prompt for Gemini.
*   **Data Persistence:** Manages the logic for saving, updating, and expiring data across the different memory tiers (e.g., saving a conversation summary to both Firestore and Vertex AI).

## The Context Building Process

The `build_context` method is the most critical function within the `MemoryManager`. It is a carefully orchestrated process designed to gather the maximum relevant information while staying within the model's token limits.

```python
# memory_manager.py (Illustrative)

class MemoryManager:
    # ... (initialization of clients) ...

    async def build_context(
        self, 
        user_id: str, 
        current_message: str,
        max_tokens: int = 8000
    ) -> str:
        """
        Build complete context for Gemini from all memory tiers.
        This is where Thread Memory™ comes to life.
        """
        
        context_parts = []
        token_count = 0
        
        # 1. Get Hot Memory (Fastest & Most Immediate)
        session = await self.get_session(user_id)
        if session:
            session_context = self._format_session(session)
            context_parts.append(session_context)
            token_count += self._estimate_tokens(session_context)
        
        # 2. Get Semantic Memory (Style & History)
        style_dna = await self.get_style_dna(user_id)
        if style_dna:
            style_context = self._format_style_dna(style_dna)
            if (token_count + self._estimate_tokens(style_context)) < max_tokens:
                context_parts.append(style_context)
                token_count += self._estimate_tokens(style_context)
        
        # 3. Get Relevant Past Conversations (Semantic Search)
        # Only search history if we have token budget left
        if token_count < max_tokens - 2000: # Reserve buffer for other context
            relevant_history = await self.get_relevant_history(
                user_id=user_id,
                current_message=current_message,
                max_results=5
            )
            if relevant_history:
                history_context = self._format_history(relevant_history)
                context_parts.append(history_context)
                token_count += self._estimate_tokens(history_context)
        
        # 4. Get Deep Memory (Structured Future Events)
        events = await self.get_upcoming_events(user_id)
        if events:
            events_context = self._format_events(events)
            if (token_count + self._estimate_tokens(events_context)) < max_tokens:
                context_parts.append(events_context)
                token_count += self._estimate_tokens(events_context)

        # 5. Add Family Info if relevant
        family = await self.get_family_info(user_id)
        if family:
            family_context = self._format_family(family)
            if (token_count + self._estimate_tokens(family_context)) < max_tokens:
                context_parts.append(family_context)

        # Join all parts into a single string for the prompt
        return "

".join(context_parts)
```

## Key Implementation Details

*   **Asynchronous Operations:** All interactions with databases and external services are asynchronous (`async`/`await`) to ensure the system remains non-blocking and responsive.
*   **Serialization:** A consistent serialization method (e.g., JSON) is used to store complex objects in Redis.
*   **Token Estimation:** The `build_context` method includes a rough token estimation to prevent overflowing Gemini's context window. This is a crucial safeguard for system stability.
*   **Dual-Write for Summaries:** When a conversation is saved via `save_conversation`, the manager performs a "dual-write." It saves the full log to Firestore (Deep Memory) and then creates/saves an embedding of the summary to Vertex AI (Semantic Memory). This keeps the semantic search index fresh.
*   **Context Formatting:** Private helper methods (`_format_session`, `_format_style_dna`, etc.) are used to convert the raw data from each memory tier into a clean, human-readable text format that is optimized for Gemini to understand.

This robust `MemoryManager` implementation is the engine that drives Thread Memory™, transforming disparate data points into a cohesive, actionable context for our AI agents.
