# Gemini: API Client Implementation

To manage the complexity of interacting with the Gemini API for various tasks, we've developed a dedicated `WeaveGeminiClient`. This Python class acts as a centralized, robust abstraction layer, providing a clean and consistent interface for all other components of the WEAVE system to use.

## Design Philosophy

*   **Centralization:** All Gemini API calls go through this client. This prevents scattered API logic and ensures that authentication, error handling, and configuration are managed in one place.
*   **Abstraction:** The rest of the application doesn't need to know the specific details of how to construct a `GenerateContentConfig` or parse a `FunctionCall`. They simply call a high-level method like `client.generate(...)` or `client.classify_intent(...)`.
*   **Testability:** By centralizing access, we can easily mock the `WeaveGeminiClient` during testing to simulate Gemini API responses without making actual network calls.

## Core `generate` Method

The heart of the client is the `generate` method. It is a flexible, multimodal method capable of handling text, images, audio, and function calling in a single, unified interface.

```python
# gemini/gemini_client.py (Illustrative)

from google import genai
from google.genai import types
from typing import List, Dict, Any, Optional

class WeaveGeminiClient:
    """
    WEAVE's Gemini API client with all integrations.
    """
    
    def __init__(self, api_key: str):
        self.client = genai.Client(api_key=api_key)
        self.default_model = "gemini-2.0-flash"
        self.function_registry = {} # Populated at startup
    
    async def generate(
        self,
        prompt: str,
        system_prompt: Optional[str] = None,
        context: Optional[str] = None,
        images: Optional[List[bytes]] = None,
        audio: Optional[bytes] = None,
        functions: Optional[List[str]] = None,
        model: str = None,
        temperature: float = 0.7
    ) -> Dict[str, Any]:
        """
        Generate response with full multimodal support.
        
        Args:
            prompt (str): User message or primary agent prompt.
            system_prompt (str): The master system prompt for the agent.
            context (str): Assembled context from the Memory Manager.
            images (Optional[List[bytes]]): List of image bytes for multimodal input.
            audio (Optional[bytes]): Audio bytes for voice input.
            functions (Optional[List[str]]): List of function names to enable for this call.
            model (str): Optional override for the default model.
            temperature (float): The creativity level for the response.
        
        Returns:
            A dictionary containing the response text, any function calls, and usage metadata.
        """
        
        # 1. Build the list of content parts for the API call
        contents = []
        if system_prompt:
            contents.append(system_prompt)
        if context:
            contents.append(f"CONTEXT:
{context}

")
        
        contents.append(f"USER INPUT:
{prompt}")
        
        if images:
            for img in images:
                contents.append(types.Part.from_image(img))
        
        if audio:
            contents.append(types.Part.from_audio(audio))
        
        # 2. Build the generation configuration
        config = types.GenerateContentConfig(temperature=temperature)
        if functions:
            # Retrieve the full function definition from the registry
            config.tools = [self.function_registry[f] for f in functions if f in self.function_registry]
        
        # 3. Select the model and generate content
        selected_model = model or self.default_model
        response = await self.client.models.generate_content_async(
            model=selected_model,
            contents=contents,
            config=config
        )
        
        # 4. Parse and return a structured response
        return {
            "text": response.text,
            "function_calls": response.candidates[0].content.parts 
                if hasattr(response.candidates[0].content, 'parts') else [],
            "usage": response.usage_metadata
        }
```

This client provides a powerful and flexible foundation for our agents to leverage the full power of Gemini without needing to handle the low-level implementation details of the API.
