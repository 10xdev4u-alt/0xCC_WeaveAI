# WEAVE - Gemini Integration Deep Dive

WEAVE is built from the ground up to be Gemini-native, leveraging the advanced capabilities of Gemini 2.0 (Flash and Pro) to deliver a truly intelligent and responsive commerce operating system. This document outlines how Gemini is integrated into WEAVE's core architecture and its impact on the user experience.

## 1. Core Gemini Capabilities Utilized

WEAVE strategically employs different Gemini models and features to optimize performance, cost, and intelligence across various functionalities.

*   **Gemini 2.0 Flash for Real-time Orchestration:**
    *   **Purpose:** Powers the primary conversational flow and agent routing. Its speed (<500ms response times) is critical for fluid user interactions in chat-based interfaces.
    *   **Usage:**
        *   **Intent Detection:** Rapidly identifies the user's goal or query from natural language input.
        *   **Language Understanding:** Processes diverse linguistic inputs, including Hinglish and 12 Indian languages, ensuring broad accessibility.
        *   **Agent Routing:** Acts as the central dispatcher, dynamically selecting and invoking the most appropriate specialized agent based on the detected intent and context.
        *   **Response Generation:** Crafts natural, contextually relevant, and personalized responses to users after agent processing.

*   **Gemini 2.0 Pro for Complex Reasoning:**
    *   **Purpose:** Utilized for tasks requiring deeper analytical capabilities and more extensive context processing.
    *   **Usage:**
        *   **Style DNA Agent:** Analyzes complex user data, fashion trends, and product attributes to create a 512-dimensional style profile. This involves sophisticated reasoning to understand aesthetic preferences, sizing nuances, and compatibility.

*   **2M Token Context Window for Cross-session Memory:**
    *   **Purpose:** Enables the "Thread Memory™ Layer" by allowing WEAVE to maintain long-term, rich conversational context across multiple sessions and channels.
    *   **Impact:** Solves the "context amnesia" problem by remembering past interactions, preferences, and purchase history, leading to highly personalized and continuous user journeys.

*   **Multimodal Capabilities (Vision and Audio):**
    *   **Purpose:** Enhances user interaction by allowing input beyond text.
    *   **Usage:**
        *   **Voice Transcription:** Processes spoken queries in various languages, converting them to text for intent detection and agent processing.
        *   **Image Matching:** Allows users to upload images (e.g., of clothing items) for visual search and style inspiration, directly feeding into the Discovery and Style DNA Agents.

*   **Function Calling:**
    *   **Purpose:** Seamlessly connects Gemini's intelligence with WEAVE's backend systems and external integrations.
    *   **Usage:** Enables agents to dynamically invoke tools and APIs for:
        *   **Inventory Management:** Checking stock levels and product availability.
        *   **Payments:** Initiating and processing transactions (e.g., via Razorpay).
        *   **Reservations:** Booking in-store appointments or holding items.
        *   **Catalog Search:** Querying the product database for specific items or categories.

*   **Search Grounding:**
    *   **Purpose:** Keeps Gemini's responses relevant and up-to-date with real-world information.
    *   **Usage:** Integrates real-time data on trending fashion, local events, and market conditions to inform agent decisions and recommendations.

## 2. Gemini Client and Integration Flow

The `gemini_client.py` within the `backend/integrations/` directory encapsulates all interactions with the Gemini API. This module handles:

*   **API Key Management:** Securely manages authentication for Gemini API calls.
*   **Model Selection:** Dynamically selects between `gemini-2.0-flash` and `gemini-2.0-pro` based on the agent's requirements.
*   **Content Formatting:** Prepares user inputs (text, images, audio) and contextual information into a format suitable for the Gemini API.
*   **Function Tool Declaration:** Registers available backend functions (e.g., `search_catalog`, `check_inventory`, `reserve_item`) with Gemini, enabling its function calling capability.
*   **Response Parsing:** Processes Gemini's responses, including generated text, function call requests, and safety attributes.

**Example Integration Snippet (from `GITHUB_README.md`):**

```python
from google import genai
from google.genai import types

client = genai.Client()

def discovery_agent(user_input: dict, user_context: dict):
    # This function represents how an agent might interact with Gemini
    # It demonstrates content formulation and tool configuration
    response = client.models.generate_content(
        model="gemini-2.0-flash", # Using Flash for real-time interaction
        contents=[
            f"""You are WEAVE, a shopping assistant for Indian fashion.
            
            User Context:
            - Style DNA: {user_context['style_dna']}
            - Language: {user_context['language']}
            - Past purchases: {user_context['history']}
            
            User Query: {user_input['message']}
            
            Respond naturally in the user's language.
            Include personalized size recommendations.
            """,
            user_input.get('image'),  # Optional image input
            user_input.get('audio'),  # Optional voice input
        ],
        config=types.GenerateContentConfig(
            tools=[search_catalog, check_inventory, reserve_item], # Declaring available tools
            temperature=0.7 # Configuration for response creativity
        )
    )
    # Further logic to process Gemini's response, execute function calls, etc.
    return response
```

## 3. Impact on User Experience

The deep integration with Gemini 2.0 allows WEAVE to offer an unparalleled commerce experience:

*   **Hyper-Personalization:** Through Style DNA and 2M token context, every interaction feels bespoke and understands the user's unique journey.
*   **Seamless Multilingual Support:** Breaking down language barriers for a vast Indian demographic.
*   **Intuitive Multimodal Interaction:** Users can communicate naturally through text, voice, or images.
*   **Proactive and Intelligent Assistance:** Agents anticipate needs and offer relevant solutions before being explicitly asked.
*   **Efficient Bridging of Channels:** Unifying online and offline shopping through intelligent orchestration.

By making Gemini 2.0 the central AI brain, WEAVE delivers a truly intelligent, adaptive, and human-centric commerce platform.
