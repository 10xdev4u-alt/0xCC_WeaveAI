# Discovery Agent

The Discovery Agent is the user's primary guide to the product catalog. It is an expert fashion shopping assistant responsible for handling product search, browsing, and personalized recommendations.

## Core Responsibilities

*   **Multimodal Search:** Understands user queries in text, voice, or image form.
*   **Personalized Recommendations:** Leverages the user's Style DNA and past behavior to provide relevant, personalized suggestions.
*   **Inventory Awareness:** Checks real-time stock to ensure recommended products are available.

## Triggers

The Discovery Agent is activated by the Gemini Orchestrator when the user's intent is related to finding products. Common triggers include:
*   Directly asking for products (e.g., "Show me red kurtas").
*   Casually browsing categories.
*   Uploading an image of an outfit for visual matching ("shop the look").
*   Asking for alternatives to a viewed product.

## System Prompt

The behavior of the Discovery Agent is governed by a detailed system prompt that defines its personality, knowledge base, and approach.

```
You are WEAVE's Discovery Agent - an expert fashion shopping assistant.

Your personality:
- Friendly and helpful, like a knowledgeable friend.
- Adapt to user's language (respond in same language they use).
- Proactive with suggestions, but not pushy.

Your knowledge:
- You have access to the user's Style DNA profile.
- You know their past purchases and preferences.
- You can see real-time inventory.

Your approach:
1. Understand what the user is looking for (occasion, style, budget).
2. Match with their Style DNA for personalization.
3. Recommend 3-5 products with clear reasoning.
4. Adjust sizes based on brand-specific fit data from the user's Style DNA.
5. Mention availability and alternatives if needed.

Response format:
- Keep responses concise but informative.
- Use product cards for visual items.
- Always explain WHY a product fits their style.
- Include personalized sizing advice.
```

## Authorized Functions

To perform its duties, the Discovery Agent has access to a specific set of tools from the Function Registry:

*   `search_catalog`: To find products based on queries and filters.
*   `check_inventory`: To verify stock levels for recommended items.
*   `get_similar_products`: To find stylistically similar alternatives.
*   `add_to_cart`: To allow the user to easily add an item to their cart.

## Example Implementation

The following code illustrates how the Discovery Agent might handle a standard text query versus a more complex image-based query.

```python
# agents/discovery_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import UserInput, Context, Response

class DiscoveryAgent(BaseAgent):
    """
    Handles product search, browsing, and recommendations.
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    
    FUNCTIONS = [
        "search_catalog",
        "check_inventory", 
        "get_similar_products",
        "add_to_cart"
    ]
    
    def __init__(self):
        super().__init__(
            name="discovery",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )
    
    async def execute(self, user_input: UserInput, context: Context) -> Response:
        """
        Main execution logic for the Discovery Agent.
        Delegates to specialized handlers based on input type.
        """
        # Handle image input (outfit matching)
        if user_input.has_image():
            return await self._handle_image_search(user_input, context)
        
        # Handle standard text/voice query
        return await self._handle_text_search(user_input, context)

    async def _handle_text_search(self, user_input: UserInput, context: Context) -> Response:
        """Handles a standard text-based search query."""
        prompt = self._build_prompt(user_input, context)
        
        gemini_response = await self.gemini_client.generate(
            prompt=prompt,
            system_prompt=self.system_prompt,
            functions=self.FUNCTIONS
        )
        
        # ... logic to handle function calls and format response ...
        return self._format_response(gemini_response)
    
    async def _handle_image_search(self, user_input: UserInput, context: Context) -> Response:
        """Handles an image-based "shop the look" query."""
        prompt = f"""
        The user has uploaded an image looking for similar products.
        
        USER CONTEXT:
        {context.to_text()}
        
        Analyze the image and:
        1. Describe the style/outfit in the image.
        2. Identify key elements (color, style, occasion).
        3. Use the 'search_catalog' function to find similar products.
        4. Recommend the best matches based on the user's Style DNA.
        
        Respond naturally to the user with your findings.
        """
        
        gemini_response = await self.gemini_client.generate(
            prompt=prompt,
            system_prompt=self.system_prompt,
            images=[user_input.image],
            functions=["search_catalog"]
        )
        
        # ... logic to handle function calls and format response ...
        return self._format_response(gemini_response)

```
This specialized design allows the Discovery Agent to be a powerful and flexible tool for product exploration, adapting its approach based on the user's input modality.
