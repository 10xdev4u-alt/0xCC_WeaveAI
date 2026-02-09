# Gemini: Specialized Methods

Beyond the core `generate` method, the `WeaveGeminiClient` includes several specialized, high-level methods. These methods are designed for recurring, structured tasks like intent classification or Style DNA generation. They encapsulate the specific prompt engineering required for these tasks, providing a simple and clean API to the rest of the system.

## `classify_intent`

This method takes a raw user message and returns a structured JSON object representing the user's intent and extracted entities.

```python
async def classify_intent(self, message: str, language: str) -> Dict:
    """Classify user intent from a message."""
    
    prompt = f"""
    Classify this customer message:
    
    Message: "{message}"
    Detected Language: {language}
    
    Output a single, minified JSON object with the following structure:
    {{
        "primary_intent": "discovery|purchase|support|rescue|info",
        "sub_intent": "specific action",
        "entities": {{
            "product_type": null or string,
            "occasion": null or string,
            "budget": null or number,
            "size": null or string,
            "color": null or string
        }},
        "urgency": "low|medium|high",
        "sentiment": "positive|neutral|negative"
    }}
    """
    
    response = await self.generate(
        prompt=prompt,
        temperature=0.2  # Lower temperature for more deterministic classification
    )
    
    return self._parse_json(response["text"])
```
*   **Purpose:** To quickly understand what a user wants to do.
*   **Key Technique:** Uses a low temperature (`0.2`) to make the classification more predictable and less "creative." The prompt explicitly asks for a JSON output, which is then parsed by a helper method.

## `generate_style_dna`

This is a powerful method that uses the Gemini 2.0 Pro model to perform a deep analysis of a user's entire history and generate their Style DNA profile.

```python
async def generate_style_dna(self, user_data: Dict) -> Dict:
    """Generate a Style DNA profile from aggregated user history."""
    
    prompt = f"""
    Analyze this user's fashion behavior and generate a comprehensive Style DNA profile in the specified JSON format.

    PURCHASE HISTORY:
    {user_data.get('purchases', [])}
    
    RETURNS (with reasons):
    {user_data.get('returns', [])}
    
    BROWSING BEHAVIOR:
    {user_data.get('browsing', [])}
    
    EXPLICIT PREFERENCES:
    {user_data.get('preferences', {})}
    
    Generate a comprehensive Style DNA profile as a single, minified JSON object.
    """
    
    # Use the more powerful 'pro' model for this complex analytical task
    response = await self.generate(
        prompt=prompt,
        model="gemini-2.0-pro",
        temperature=0.5
    )
    
    return self._parse_json(response["text"])
```
*   **Purpose:** To create the rich, multi-dimensional "style fingerprint" for a user.
*   **Key Technique:** Explicitly calls the `gemini-2.0-pro` model for its advanced reasoning capabilities. The prompt is structured like a data analysis task, feeding raw data to the model and requesting a structured JSON output.

## `create_embedding`

This method is a wrapper around Gemini's embedding models, used to create vector representations of text for semantic search.

```python
async def create_embedding(self, text: str, model: str = "text-embedding-004") -> List[float]:
    """Create a vector embedding for a given piece of text."""
    
    response = await self.client.models.embed_content_async(
        model=model,
        content=text
    )
    
    return response.embedding
```
*   **Purpose:** To power the Semantic Memory tier.
*   **Key Technique:** Uses the dedicated `embed_content_async` method, which is highly optimized for creating embeddings.

By creating these specialized methods, we make our agent logic much cleaner. An agent can simply call `await client.classify_intent(message)` instead of having to know the specific prompt engineering details for that task. This improves code readability, maintainability, and reusability.
