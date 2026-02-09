# API: Store Functions

Store Functions are the tools that power the **Bridge Agent**, enabling it to connect the digital and physical retail worlds. They handle interactions with physical store locations, from checking inventory to briefing associates.

---

## 1. `reserve_item_for_store`

Reserves a specific item at a physical store for a customer to try on or pick up. This is a core function for the "phygital" experience.

### Schema (for Gemini)
```json
{
    "description": "Reserve an item at a physical store for customer pickup or trial.",
    "parameters": {
        "type": "object",
        "properties": {
            "product_id": {"type": "string"},
            "size": {"type": "string"},
            "store_id": {"type": "string"},
            "user_id": {"type": "string"}
        },
        "required": ["product_id", "size", "store_id", "user_id"]
    }
}
```

### Python Definition
```python
async def reserve_item_for_store(
    product_id: str,
    size: str,
    store_id: str,
    user_id: str
) -> Dict:
    """
    Connects to the store's POS or inventory management system.
    Returns:
        {
            "reservation_id": "res_12345",
            "store_name": "WEAVE Jubilee Hills",
            "store_address": "...",
            "trial_room": "3" (if assigned),
            "expires_at": "timestamp"
        }
    """
    pass
```

---

## 2. `reserve_trial_room`

Reserves a trial room for a customer at a specific store, often in conjunction with `reserve_item_for_store`.

### Schema (for Gemini)
```json
{
    "description": "Reserve a trial room at a specific store for a customer.",
    "parameters": {
        "type": "object",
        "properties": {
            "store_id": {"type": "string"},
            "user_id": {"type": "string"},
            "items": {
                "type": "array",
                "items": {"type": "string"},
                "description": "List of product IDs to be placed in the room."
            }
        },
        "required": ["store_id", "user_id", "items"]
    }
}
```

### Python Definition
```python
async def reserve_trial_room(
    store_id: str,
    user_id: str,
    items: List[str]
) -> Dict:
    """Returns the assigned trial room number and details."""
    pass
```

---

## 3. `notify_store_associate`

Pushes a detailed customer briefing to a store associate's tablet or in-store device. This is the critical handoff step.

### Schema (for Gemini)
```json
{
    "description": "Send a detailed customer briefing to a store associate's in-store device.",
    "parameters": {
        "type": "object",
        "properties": {
            "store_id": {"type": "string"},
            "user_id": {"type": "string"},
            "briefing": {
                "type": "object",
                "description": "A JSON object containing the customer's name, Style DNA summary, reserved items, and upsell suggestions."
            }
        },
        "required": ["store_id", "user_id", "briefing"]
    }
}
```

### Python Definition
```python
async def notify_store_associate(
    store_id: str,
    user_id: str,
    briefing: Dict
) -> Dict:
    """Implementation uses a WebSocket or push notification service connected to the in-store tablets."""
    pass
```

---

## 4. `get_store_info`

Retrieves information about physical stores, such as location, hours, and contact details. Can be used to find the nearest store.

### Schema (for Gemini)
```json
{
    "description": "Get information about physical stores, optionally finding the nearest ones to a location.",
    "parameters": {
        "type": "object",
        "properties": {
            "store_id": {
                "type": "string",
                "description": "Optional: Get info for a specific store."
            },
            "location": {
                "type": "object",
                "description": "Optional: User's current location to find nearest stores.",
                "properties": {"latitude": {"type": "number"}, "longitude": {"type": "number"}}
            },
            "limit": {"type": "integer", "default": 5}
        }
    }
}
```

### Python Definition
```python
async def get_store_info(
    store_id: Optional[str] = None,
    location: Optional[Dict] = None,
    limit: int = 5
) -> List[Dict]:
    """Queries the store database."""
    pass
```
