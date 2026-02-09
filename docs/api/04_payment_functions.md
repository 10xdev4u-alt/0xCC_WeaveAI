# API: Payment Functions

Payment Functions are tools used to handle all monetary transactions, including applying discounts and triggering payment links. They are primarily used by the **Rescue Agent** and the **Family Sync Agent**.

---

## 1. `send_payment_link`

Generates and sends a payment link to the user for a specific amount. This is used to complete an order or to help a user retry a failed payment.

### Schema (for Gemini)
```json
{
    "description": "Generate and send a payment link to the user for a specific amount.",
    "parameters": {
        "type": "object",
        "properties": {
            "user_id": {
                "type": "string",
                "description": "The ID of the user to send the link to."
            },
            "amount": {
                "type": "number",
                "description": "The payment amount."
            },
            "order_id": {
                "type": "string",
                "description": "The order ID associated with this payment."
            },
            "method": {
                "type": "string",
                "description": "The preferred payment method.",
                "enum": ["upi", "card", "cod"]
            }
        },
        "required": ["user_id", "amount", "order_id"]
    }
}
```

### Python Definition
```python
async def send_payment_link(
    user_id: str,
    amount: float,
    order_id: str,
    method: str = "upi"
) -> Dict:
    """
    Implementation connects to the Razorpay API.
    Returns:
        {
            "link": "https://rzp.io/i/...",
            "expires_at": "timestamp",
            "sent_via": "whatsapp"
        }
    """
    pass
```

---

## 2. `apply_discount`

Applies a personalized discount to a user's cart or a specific item. This is a powerful tool for the **Rescue Agent** during cart recovery.

### Schema (for Gemini)
```json
{
    "description": "Apply a personalized discount to a user's cart or a specific item.",
    "parameters": {
        "type": "object",
        "properties": {
            "user_id": {
                "type": "string"
            },
            "product_id": {
                "type": "string",
                "description": "Optional: Apply to a specific product."
            },
            "discount_percent": {
                "type": "number",
                "description": "The discount percentage to apply (e.g., 10.5 for 10.5%)."
            },
            "reason": {
                "type": "string",
                "description": "The reason for the discount (e.g., 'cart_recovery', 'first_time_user')."
            }
        },
        "required": ["user_id", "discount_percent", "reason"]
    }
}
```

### Python Definition
```python
async def apply_discount(
    user_id: str,
    discount_percent: float,
    reason: str,
    product_id: Optional[str] = None
) -> Dict:
    """
    Applies the discount logic and returns the updated cart total.
    Returns:
        {
            "success": bool,
            "new_total": 1169.10
        }
    """
    pass
```
