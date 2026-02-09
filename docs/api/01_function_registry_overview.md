# API: Function Registry Overview

The Function Registry is a core component of WEAVE's architecture that empowers our Gemini-powered agents to interact with the real world. It is a dictionary of all the backend functions (tools) that are available for the agents to call.

## The "Tools" for the AI

In WEAVE, agents don't just generate text; they take action. They can check inventory, hold items, send payment links, and more. This is made possible by Gemini's native **Function Calling** capability.

The process works as follows:
1.  **Declaration:** When we call the Gemini API, we provide it with a list of function declarations (name, description, parameters). This tells the model what "tools" it has in its toolbox.
2.  **Decision:** Based on the user's query and the context, Gemini intelligently decides if one of these tools should be used. If so, it doesn't respond with text, but instead with a `FunctionCall` object containing the name of the function to call and the arguments to use.
3.  **Execution:** Our backend receives this `FunctionCall`, looks up the corresponding function in our registry, and executes it with the provided arguments.
4.  **Response:** The result of the function execution is then sent back to Gemini, which uses this new information to generate a final, natural language response for the user.

## The `FunctionRegistry`

In our codebase, this is managed by a `FunctionRegistry` class that is initialized at startup.

```python
# functions/registry.py (Illustrative)

from typing import Dict, Callable

class FunctionRegistry:
    
    def __init__(self):
        self.functions: Dict[str, Callable] = {}
        self.schemas: Dict[str, Dict] = {}

    def register(self, func: Callable, schema: Dict):
        """
        Registers a function and its schema.
        The schema is what Gemini sees.
        """
        func_name = func.__name__
        self.functions[func_name] = func
        self.schemas[func_name] = schema

    def get_function(self, name: str) -> Callable:
        return self.functions.get(name)

    def get_schema(self, name: str) -> Dict:
        return self.schemas.get(name)

    def get_tools_for_agent(self, agent_function_names: List[str]) -> List[Tool]:
        """
        Prepares the list of Tool objects for the Gemini API
        based on the functions an agent is authorized to use.
        """
        # ... Logic to create google.genai.types.Tool objects ...
        pass

```

## Function Categories

The available functions are organized into logical categories, each documented in its own file:

*   **[Catalog Functions](./02_catalog_functions.md):** For searching and retrieving product information.
*   **[Cart Functions](./03_cart_functions.md):** For managing the user's shopping cart.
*   **[Payment Functions](./04_payment_functions.md):** For handling payments and discounts.
*   **[Store Functions](./05_store_functions.md):** For interacting with physical store operations.
*   **[Family Cart Functions](./06_family_cart_functions.md):** For managing collaborative shopping.
*   **[External Data Functions](./07_external_data_functions.md):** For fetching data from external sources like weather or calendars.

This structured registry provides a secure and scalable way to expose our backend capabilities to our AI agents, forming the critical link between reasoning and action.
