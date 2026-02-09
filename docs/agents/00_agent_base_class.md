# Agent System: The BaseAgent

All specialized agents in WEAVE inherit from a common `BaseAgent` class. This object-oriented approach ensures that every agent adheres to a consistent interface and shares common functionalities for interacting with the Gemini Orchestrator and other core system components.

## Core Design Principles

*   **Single Responsibility:** Each agent is an expert in one specific domain. The `BaseAgent` provides the foundational structure, but the specialized logic resides within the child classes.
*   **Defined Interface:** Every agent must implement an `execute` method. This is the primary entry point called by the Orchestrator. This consistency simplifies the routing logic.
*   **Dependency Injection:** Agents do not create their own dependencies (like the Gemini client or Memory Manager). These are provided to them, which makes them highly testable and decoupled from the infrastructure.

## Base Class Implementation

The following Python code outlines the structure of the `BaseAgent`.

```python
# agents/base_agent.py (Illustrative)

from typing import List, Callable
from ..integrations import WeaveGeminiClient
from ..schemas import UserInput, Context, Response # Assuming schema definitions

class BaseAgent:
    """
    Base class for all WEAVE agents.
    
    Each agent has:
    - A specialized system prompt defining its personality and goals.
    - A specific list of functions (tools) it is allowed to use.
    - Custom logic for building prompts and formatting responses.
    """
    
    def __init__(self, name: str, system_prompt: str, functions: List[str]):
        """
        Initializes the agent.
        
        Args:
            name (str): The unique name of the agent (e.g., "discovery", "rescue").
            system_prompt (str): The master prompt that defines the agent's behavior.
            functions (List[str]): A list of function names this agent can call.
        """
        self.name = name
        self.system_prompt = system_prompt
        self.functions = functions
        self.gemini_client = WeaveGeminiClient() # Injected in a real scenario
    
    async def execute(
        self, 
        user_input: UserInput,
        context: Context
    ) -> Response:
        """
        The main entry point for an agent's logic. This method must be
        implemented by all child agent classes.
        
        It orchestrates building a prompt, calling Gemini, handling function
        calls, and formatting the final response.
        """
        
        # 1. Build a rich, contextual prompt
        prompt = self._build_prompt(user_input, context)
        
        # 2. Call Gemini with the agent's specific configuration
        gemini_response = await self.gemini_client.generate(
            model="gemini-2.0-flash", # Agents typically use the fast model
            system_prompt=self.system_prompt,
            prompt=prompt,
            functions=self.functions
        )
        
        # 3. Handle potential function calls returned by Gemini
        if gemini_response.has_function_calls():
            # Logic to execute functions and get results would be here
            function_results = await self._execute_functions(gemini_response.function_calls)
            
            # Re-prompt Gemini with the function results to get a final natural language response
            final_gemini_response = await self._generate_with_results(
                prompt,
                function_results
            )
            return self._format_response(final_gemini_response)

        # 4. Format and return the final response
        return self._format_response(gemini_response)

    def _build_prompt(self, user_input: UserInput, context: Context) -> str:
        """
        Constructs the full prompt to be sent to Gemini.
        This can be overridden by child classes for more specific prompt engineering.
        """
        return f"""
        USER CONTEXT:
        {context.to_text()}

        USER QUERY:
        {user_input.text}
        """

    def _format_response(self, gemini_response) -> Response:
        """
        Transforms the raw Gemini response into a structured Response object.
        This can be overridden for agent-specific response formats.
        """
        # Logic to create a structured Response object
        return Response(text=gemini_response.text)

    async def _execute_functions(self, function_calls) -> dict:
        """Placeholder for function execution logic."""
        # This would interact with the FunctionRegistry
        return {"status": "success"}

    async def _generate_with_results(self, prompt, function_results) -> dict:
        """Placeholder for re-prompting with function results."""
        return {"text": "Function executed successfully."}

```
This base class provides a solid, reusable foundation, allowing us to rapidly develop new, powerful agents while maintaining architectural consistency.
