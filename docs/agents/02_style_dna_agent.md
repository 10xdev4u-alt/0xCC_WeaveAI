# Style DNA™ Agent

The Style DNA™ Agent is arguably the most sophisticated and value-driving component in the WEAVE ecosystem. It is a deep-reasoning agent responsible for creating and maintaining a rich, multi-dimensional profile of each user's unique fashion sense. This "style fingerprint" is the foundation for all hyper-personalization within WEAVE.

## Core Responsibilities

*   **Profile Generation:** Analyzes a user's complete interaction history to generate a comprehensive Style DNA profile.
*   **Sizing Prediction:** Determines a user's "true size" for different brands based on purchase and return data.
*   **Aesthetic Analysis:** Goes beyond simple preferences to understand a user's underlying aesthetic (e.g., minimalist, bohemian, streetwear).

## Triggers

The Style DNA Agent is invoked at key moments in the user journey to either build or refine a profile.
*   **New User:** Triggered after a user's first few significant interactions (e.g., browsing, searching, liking items).
*   **Size-Related Query:** Activated when a user asks, "What size should I get?" or views a size chart.
*   **Post-Purchase/Return:** Runs an analysis after a purchase is kept or an item is returned to refine brand-specific sizing models.
*   **Explicit Feedback:** Triggered when a user interacts with their Style DNA profile directly.

## System Prompt

This agent uses a highly analytical and detailed prompt, designed to guide Gemini 2.0 Pro in its complex analysis of user data.

```
You are WEAVE's Style DNA Agent. You are a master data analyst and fashion expert. Your task is to analyze a comprehensive set of user data and synthesize it into a structured, 512-dimensional Style DNA profile.

Your approach is purely analytical and data-driven.
1.  Analyze the user's purchase history, paying close attention to what they keep vs. what they return.
2.  Correlate return reasons (e.g., "too small," "color not as expected") with specific products and brands to build a sizing adjustment model.
3.  Infer aesthetic preferences from browsing behavior, liked items, and image searches.
4.  Synthesize all this data into the structured JSON format provided. Be precise and thorough.

Your output is not for the user directly, but for the WEAVE system to use for personalization.
```

## Authorized Functions

*   `analyze_user_history`: A function that retrieves and aggregates all user data (purchases, returns, browsing).
*   `get_product_attributes`: Fetches detailed metadata for a given product.
*   `update_style_dna_embedding`: Saves the newly generated Style DNA profile and its vector embedding to the Memory Layer.

## Example Implementation

The core logic of this agent involves a heavy-duty analytical call to Gemini 2.0 Pro.

```python
# agents/style_dna_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import User, Response

class StyleDNAAgent(BaseAgent):
    """
    Analyzes user data to create and maintain their Style DNA profile.
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    FUNCTIONS = [
        "analyze_user_history",
        "get_product_attributes",
        "update_style_dna_embedding"
    ]
    
    def __init__(self):
        super().__init__(
            name="style_dna",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )

    async def generate_profile(self, user: User) -> dict:
        """
        Generates or updates a user's Style DNA profile.
        This is a heavy, asynchronous operation.
        """
        
        # 1. Get all user data
        user_data = await self.gemini_client.execute_function(
            "analyze_user_history", 
            user_id=user.id
        )
        
        # 2. Build the detailed prompt for Gemini Pro
        prompt = f"""
        Analyze this user's fashion behavior and generate a comprehensive Style DNA profile in the specified JSON format.

        USER DATA:
        {user_data}
        """
        
        # 3. Call the 'pro' model for deep reasoning
        style_dna_json = await self.gemini_client.generate(
            prompt=prompt,
            system_prompt=self.system_prompt,
            model="gemini-2.0-pro", # Use the more powerful model
            temperature=0.4
        )

        # 4. Save the new profile and its vector embedding to memory
        await self.gemini_client.execute_function(
            "update_style_dna_embedding",
            user_id=user.id,
            style_dna_profile=style_dna_json
        )
        
        return style_dna_json

    async def get_specific_sizing_advice(self, user: User, product_id: str) -> Response:
        """
        Provides real-time sizing advice for a specific product.
        """
        prompt = f"""
        Based on the user's Style DNA, especially their brand-sizing adjustments,
        what size should they get for product '{product_id}'?

        STYLE DNA:
        {user.style_dna}

        Provide a short, direct answer and a brief explanation.
        """
        
        response = await self.gemini_client.generate(prompt=prompt)
        return self._format_response(response)

```
By offloading the complex analytical work to Gemini 2.0 Pro, the Style DNA Agent creates a rich, evolving understanding of each user that is far beyond the capabilities of traditional recommendation engines.
