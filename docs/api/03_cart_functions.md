# API: Cart Functions

Cart Functions are tools used by agents to manage a user's shopping cart. They are used by multiple agents, including the **Discovery Agent** and the **Rescue Agent**.

---

## 1. `add_to_cart`

Adds a specific item (with size and quantity) to the user's active shopping cart.

### Schema (for Gemini)
```json
{
    "description": "Add an item to the user's current shopping cart.",
    "parameters": {
        "type": "object",
        "properties": {
            "user_id": {
                "type": "string",
                "description": "The ID of the user whose cart is being modified."
            },
            "product_id": {
                "type": "string",
                "description": "The ID of the product to add."
            },
            "size": {
                "type": "string",
                "description": "The selected size of the product."
            },
            "quantity": {
                "type": "integer",
                "description": "The quantity to add.",
                "default": 1
            }
        },
        "required": ["user_id", "product_id", "size"]
    }
}
```

### Python Definition
```python
async def add_to_cart(
    user_id: str,
    product_id: str,
    size: str,
    quantity: int = 1
) -> Dict:
    """Implementation interacts with the Hot Memory (Redis) cart object."""
    pass
```

---

## 2. `get_cart`

Retrieves the full contents of a user's current shopping cart.

### Schema (for Gemini)
```json
{
    "description": "Get the current contents of a user's shopping cart.",
    "parameters": {
        "type": "object",
        "properties": {
            "user_id": {
                "type": "string",
                "description": "The ID of the user whose cart should be retrieved."
            }
        },
        "required": ["user_id"]
    }
}
```

### Python Definition
```python
async def get_cart(user_id: str) -> Dict:
    """
    Returns:
        A dictionary representing the cart, e.g.,
        {
            "items": [{"product_id", "name", "size", "quantity", "price"}],
            "subtotal": 1299.00
        }
    """
    pass
```

---

## 3. `hold_inventory`

Temporarily holds a specific item for a user, preventing others from buying it. This is a key tool for the **Rescue Agent** to create genuine urgency.

### Schema (for Gemini)
```json
{
    "description": "Temporarily hold inventory for a specific item for a user, preventing others from buying it.",
    "parameters": {
        "type": "object",
        "properties": {
            "product_id": {
                "type": "string"
            },
            "size": {
                "type": "string"
            },
            "duration_hours": {
                "type": "integer",
                "description": "How long to hold the item for.",
                "default": 2
            },
            "user_id": {
                "type": "string"
            }
        },
        "required": ["product_id", "size"]
    }
}
```

### Python Definition
```python
async def hold_inventory(
    product_id: str,
    size: str,
    duration_hours: int = 2,
    user_id: str = None
) -> Dict:
    """
    Returns:
        {
            "success": bool,
            "hold_id": str,
            "expires_at": "timestamp"
        }
    """
    pass
```
