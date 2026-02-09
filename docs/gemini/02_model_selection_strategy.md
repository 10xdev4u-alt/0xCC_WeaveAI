# Gemini: Model Selection Strategy

WEAVE does not rely on a single AI model. We employ a dynamic, intelligent **Model Routing** strategy to ensure that every task is handled by the optimal Gemini model, balancing speed, intelligence, and cost. This strategic selection is a core component of our system's efficiency.

## The Model Arsenal

We utilize three primary models from the Gemini family, each with a distinct role:

| Model | Codename | Primary Use Case | Key Attribute |
|---|---|---|---|
| **Gemini 2.0 Flash** | `flash` | Real-time conversation, intent classification, agent routing. | **Speed** (<500ms latency) |
| **Gemini 2.0 Pro** | `pro` | Deep analysis, Style DNA generation, complex reasoning. | **Intelligence** (Advanced reasoning) |
| **(Hypothetical) Gemini 2.0 Flash-Thinking** | `flash_thinking`| Complex reasoning tasks that require transparency. | **Traceability** |

## The `ModelRouter` Class

The logic for this dynamic selection is encapsulated within a `ModelRouter` class. This class inspects the requirements of each incoming request and routes it to the most appropriate model.

```python
# gemini/model_router.py (Illustrative)

class ModelRouter:
    """
    Routes requests to the optimal Gemini model based on requirements.

    Strategy:
    - Flash for all real-time, user-facing conversations (latency is critical).
    - Pro for asynchronous, deep analytical tasks (intelligence is critical).
    - Cached responses for repetitive, non-personalized queries.
    """

    MODELS = {
        "flash": "gemini-2.0-flash",
        "pro": "gemini-2.0-pro",
        "flash_thinking": "gemini-2.0-flash-thinking" # Hypothetical
    }

    def select_model(self, request: dict) -> str:
        """
        Selects the best model for a given request.

        Args:
            request (dict): A dictionary describing the task requirements.
        """
        task_type = request.get("task_type")
        requires_low_latency = request.get("requires_low_latency", False)

        # Rule 1: All real-time user interactions must be fast.
        if requires_low_latency:
            return self.MODELS["flash"]

        # Rule 2: Style DNA generation requires deep reasoning.
        if task_type == "style_analysis":
            return self.MODELS["pro"]
            
        # Rule 3: A task needing a reasoning trace might use a specific model.
        if request.get("needs_reasoning_trace"):
            return self.MODELS.get("flash_thinking", self.MODELS["flash"])

        # Rule 4: Default to the fastest model for all other tasks.
        return self.MODELS["flash"]

    def estimate_cost(self, request: dict) -> float:
        """
        Estimates the potential API cost for a given request before execution.
        """
        model_name = self.select_model(request)
        estimated_tokens = request.get("estimated_tokens", 1000)

        # Hypothetical per-token rates
        RATES = {
            "flash": 0.000001,
            "pro": 0.000005,
            "flash_thinking": 0.000002
        }

        rate = RATES.get(model_name, RATES["flash"])
        return estimated_tokens * rate
```

## How It Works in Practice

1.  **User Sends a Message:** A user sends "Show me blue kurtas" via WhatsApp. The `GeminiOrchestrator` creates a request: `{"task_type": "chat", "requires_low_latency": True}`. The `ModelRouter` immediately selects **Gemini 2.0 Flash**.

2.  **User Completes a Purchase:** After a user makes a purchase, a background job is triggered to update their profile. The orchestrator creates a request: `{"task_type": "style_analysis", "requires_low_latency": False, "user_data": ...}`. The `ModelRouter` selects **Gemini 2.0 Pro** for this heavy, non-time-sensitive analytical task.

This intelligent routing allows WEAVE to provide a snappy user experience where it matters most, while leveraging the powerful, deep-reasoning capabilities of larger models for complex background tasks, all in a cost-effective manner.
