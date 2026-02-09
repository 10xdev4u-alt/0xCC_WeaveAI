# API: Family Cart Functions

Family Cart Functions are the specialized tools used by the **Family Sync Agent** to manage a collaborative shopping experience. They handle the creation of shared carts, member invitations, and the voting process.

---

## 1. `create_family_cart`

Initializes a new family or group shopping cart, distinct from an individual user's cart.

### Schema (for Gemini)
```json
{
    "description": "Create a new family or group shopping cart.",
    "parameters": {
        "type": "object",
        "properties": {
            "creator_id": {
                "type": "string",
                "description": "The user ID of the person creating the cart."
            },
            "name": {
                "type": "string",
                "description": "A descriptive name for the cart (e.g., 'Diwali Shopping', 'Mom's Birthday Gift')."
            }
        },
        "required": ["creator_id", "name"]
    }
}
```

### Python Definition
```python
async def create_family_cart(
    creator_id: str,
    name: str
) -> Dict:
    """Creates a new document in the 'family_carts' collection in Firestore."""
    pass
```

---

## 2. `add_family_member`

Adds a new member to an existing family cart, typically via their phone number, and assigns them a role.

### Schema (for Gemini)
```json
{
    "description": "Add a new member to an existing family cart and assign them a role.",
    "parameters": {
        "type": "object",
        "properties": {
            "cart_id": {"type": "string"},
            "member_phone": {
                "type": "string",
                "description": "The phone number of the person to invite."
            },
            "role": {
                "type": "string",
                "description": "The role of the member.",
                "enum": ["viewer", "approver", "payer"],
                "default": "viewer"
            }
        },
        "required": ["cart_id", "member_phone"]
    }
}
```

### Python Definition
```python
async def add_family_member(
    cart_id: str,
    member_phone: str,
    role: str = "viewer"
) -> Dict:
    """Finds the user by phone number and adds them to the cart document in Firestore."""
    pass
```

---

## 3. `submit_vote`

Logs a member's vote (approve, reject) and optional comment on a specific item within a family cart.

### Schema (for Gemini)
```json
{
    "description": "Submit a vote or feedback on an item within a family cart.",
    "parameters": {
        "type": "object",
        "properties": {
            "cart_id": {"type": "string"},
            "member_id": {"type": "string"},
            "item_id": {"type": "string"},
            "vote": {
                "type": "string",
                "description": "The member's vote.",
                "enum": ["approve", "reject", "comment"]
            },
            "comment": {
                "type": "string",
                "description": "Optional: A comment to go with the vote."
            }
        },
        "required": ["cart_id", "member_id", "item_id", "vote"]
    }
}
```

### Python Definition
```python
async def submit_vote(
    cart_id: str,
    member_id: str,
    item_id: str,
    vote: str,
    comment: Optional[str] = None
) -> Dict:
    """Updates the 'votes' array for the specified item in the family cart document."""
    pass
```

---

## 4. `get_family_cart_status`

Retrieves the complete current state of a family cart, including all items, members, and their votes.

### Schema (for Gemini)
```json
{
    "description": "Get the current status of a family cart, including all items and votes.",
    "parameters": {
        "type": "object",
        "properties": {
            "cart_id": {"type": "string"}
        },
        "required": ["cart_id"]
    }
}
```

### Python Definition
```python
async def get_family_cart_status(cart_id: str) -> Dict:
    """Fetches and formats the family cart document from Firestore."""
    pass
```
