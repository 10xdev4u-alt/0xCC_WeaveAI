# API: External Data Functions

External Data Functions are tools that allow WEAVE's agents, particularly the **Proactive Agent**, to fetch real-world, real-time information from outside the immediate commerce ecosystem. This enables more intelligent, context-aware, and timely recommendations.

---

## 1. `get_weather`

Fetches the weather forecast for a specific city. This can be used to proactively recommend weather-appropriate clothing.

### Schema (for Gemini)
```json
{
    "description": "Get the weather forecast for a specific city to make proactive, weather-appropriate recommendations.",
    "parameters": {
        "type": "object",
        "properties": {
            "city": {
                "type": "string",
                "description": "The city to get the weather for (e.g., 'Mumbai')."
            },
            "days": {
                "type": "integer",
                "description": "Number of days for the forecast.",
                "default": 3
            }
        },
        "required": ["city"]
    }
}
```

### Python Definition
```python
async def get_weather(
    city: str,
    days: int = 3
) -> Dict:
    """Implementation connects to a third-party weather API (e.g., OpenWeatherMap)."""
    pass
```

---

## 2. `get_user_calendar`

Retrieves upcoming events from a user's connected calendar (with their permission). This helps in recommending outfits for specific, known occasions.

### Schema (for Gemini)
```json
{
    "description": "Get upcoming events from a user's connected calendar to recommend outfits for specific occasions.",
    "parameters": {
        "type": "object",
        "properties": {
            "user_id": {"type": "string"},
            "days_ahead": {
                "type": "integer",
                "description": "How many days into the future to look for events.",
                "default": 14
            }
        },
        "required": ["user_id"]
    }
}
```

### Python Definition
```python
async def get_user_calendar(
    user_id: str,
    days_ahead: int = 14
) -> List[Dict]:
    """
    Connects to Google Calendar API via OAuth.
    Returns a list of events, e.g.,
    [{"title": "Rahul's Sangeet", "date": "...", "location": "Goa"}]
    """
    pass
```

---

## 3. `get_trending_products`

Fetches currently trending products or styles, optionally filtered by category or location. This helps agents make recommendations that are fashionable and relevant.

### Schema (for Gemini)
```json
{
    "description": "Get a list of currently trending products or styles, which can be filtered by category or location.",
    "parameters": {
        "type": "object",
        "properties": {
            "category": {
                "type": "string",
                "description": "Optional: The product category to check trends for."
            },
            "location": {
                "type": "string",
                "description": "Optional: The city or region to check trends for (e.g., 'Delhi')."
            }
        },
        "required": []
    }
}
```

### Python Definition
```python
async def get_trending_products(
    category: Optional[str] = None,
    location: Optional[str] = None
) -> List[Dict]:
    """Implementation might connect to Google Trends API or an internal analytics service."""
    pass
```
