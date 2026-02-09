# Proactive Agent

The Proactive Agent acts as WEAVE's intelligent outreach engine. Unlike other agents that are primarily reactive to user input, the Proactive Agent is triggered by external events and data, allowing WEAVE to initiate conversations that are timely, relevant, and valuable to the user.

## Core Responsibilities

*   **Event-Driven Engagement:** Initiates contact based on real-world triggers like weather changes, upcoming calendar events, or product restocks.
*   **Personalized Notifications:** Delivers hyper-personalized alerts, such as price drops on wishlist items or new arrivals that match a user's Style DNA.
*   **Trend-Based Recommendations:** Informs users about emerging fashion trends in their location that align with their personal style.

## Triggers

This agent is purely event-driven, subscribing to various topics on the Event Bus.
*   `trigger.weather_change`: A significant weather change is forecast for the user's location.
*   `trigger.calendar_event`: An event (e.g., "Rahul's Wedding," "Vacation to Goa") is approaching on the user's connected calendar.
*   `inventory.restock`: An item on a user's wishlist or a previously viewed out-of-stock item is now available.
*   `inventory.price_drop`: An item in a user's cart or wishlist has decreased in price.
*   `trends.new`: A new fashion trend relevant to the user's Style DNA has been detected.

## System Prompt

The prompt for this agent emphasizes subtlety, relevance, and value. The goal is to be a helpful advisor, not a spam bot.

```
You are WEAVE's Proactive Agent. Your purpose is to be a thoughtful and timely fashion advisor, reaching out to users with information that is genuinely valuable to them.

Your approach:
- Always lead with the context. State CLEARLY why you are messaging them (e.g., "I saw the weather in Mumbai is dropping...").
- Connect the event to their personal style. The message must feel bespoke, not generic.
- Provide a clear, actionable suggestion.
- Be concise and respectful of their time. One message is enough.

NEVER:
- Send generic marketing spam.
- Reach out without a clear, user-centric reason.
- Create fake urgency.

Your goal is to make the user think, "Wow, that's actually really helpful."
```

## Authorized Functions

*   `get_weather`: To fetch weather forecast data.
*   `get_user_calendar`: To get information about a user's upcoming events.
*   `get_trending_products`: To find trending styles for a specific location or category.
*   `send_notification`: To send the final message to the user via their preferred channel (WhatsApp, Push Notification).

## Example Implementation

The following code illustrates how the Proactive Agent might handle a weather-based trigger.

```python
# agents/proactive_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import User, Context, Response

class ProactiveAgent(BaseAgent):
    """
    Initiates personalized, event-driven outreach to users.
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    FUNCTIONS = [
        "get_weather",
        "get_user_calendar",
        "get_trending_products",
        "send_notification"
    ]
    
    def __init__(self):
        super().__init__(
            name="proactive",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )

    async def handle_weather_trigger(self, user: User, context: Context) -> bool:
        """
        Handles a trigger for a weather change in the user's location.
        """
        # 1. Get the specific weather data
        weather_data = await self.gemini_client.execute_function(
            "get_weather",
            city=user.city,
            days=3
        )
        
        # 2. Build a prompt for Gemini to decide if a notification is warranted and what to say.
        prompt = f"""
        Analyze the upcoming weather and the user's style. Is this change significant enough to warrant a notification?

        WEATHER FORECAST:
        {weather_data}

        USER'S STYLE DNA:
        {user.style_dna}

        If a notification is warranted, generate a short, helpful, and personalized message for the user.
        For example, if it's getting cold, suggest jackets that match their style.
        If it's going to rain, suggest waterproof options.
        
        Output JSON with two keys:
        - "should_notify": boolean
        - "message": "The message to send the user, or null."
        """
        
        decision = await self.gemini_client.generate(
            prompt=prompt,
            system_prompt=self.system_prompt
        )
        
        parsed_decision = self.gemini_client.parse_json(decision.text)
        
        # 3. If Gemini decides to notify, send the message.
        if parsed_decision.get("should_notify"):
            await self.gemini_client.execute_function(
                "send_notification",
                user_id=user.id,
                channel=user.preferred_channel,
                message=parsed_decision.get("message")
            )
            return True
            
        return False
```
This intelligent, context-driven approach to outreach allows WEAVE to build a relationship with the user, offering value even when the user isn't actively shopping, thereby fostering significant brand loyalty.
