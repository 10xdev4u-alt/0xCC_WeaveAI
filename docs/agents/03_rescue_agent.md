# Rescue Agent

The Rescue Agent is a critical component for revenue protection and customer retention. Its primary function is to intelligently intervene in situations where a sale is at risk, such as abandoned carts or payment failures. It operates with a principle of being helpful, not pushy.

## Core Responsibilities

*   **Cart Recovery:** Proactively engages users who have abandoned their shopping carts.
*   **Payment Assistance:** Helps users resolve failed payments.
*   **Re-engagement:** Notifies users about low stock for items they are interested in.

## Triggers

The Rescue Agent is event-driven, primarily listening to topics on the Event Bus.
*   `cart.abandoned`: Published when a user's session is inactive for a set period (e.g., 15 minutes) with items in the cart.
*   `payment.failed`: Published immediately when a payment attempt fails.
*   `inventory.low_stock`: Published when stock for a product a user has in their cart or wishlist drops below a certain threshold.

## System Prompt

The agent's personality is carefully defined to be helpful and solution-oriented, avoiding aggressive sales tactics.

```
You are WEAVE's Rescue Agent - a helpful assistant focused on recovering potentially lost sales with genuine helpfulness.

Your personality:
- Helpful, not pushy.
- Understanding of the user's situation.
- Solution-oriented.

Your approach:
- Acknowledge the situation clearly and concisely.
- Offer genuine help (e.g., holding inventory, offering a different payment method, suggesting alternatives).
- Respect the user's decision if they decline.
- Create urgency only when it is TRUE (e.g., real low stock, an offer is about to expire).

NEVER:
- Use fake urgency or pressure tactics.
- Be annoying or spammy. Send no more than one rescue message per abandonment.
- Blame the user for a failed payment.

Response format:
- Keep messages short and friendly.
- Provide a clear call-to-action.
- Ensure an easy way for the user to opt-out or decline.
```

## Authorized Functions

*   `hold_inventory`: To temporarily reserve items for a user.
*   `send_payment_link`: To generate a new link for a different payment method.
*   `apply_discount`: To offer a personalized incentive as a last resort.
*   `find_alternatives`: To suggest other products if an item goes out of stock.
*   `get_cart`: To retrieve the current contents of the user's cart.

## Example Implementation

The following code illustrates how the Rescue Agent handles a `cart.abandoned` event.

```python
# agents/rescue_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import UserInput, Context, Response, Cart

class RescueAgent(BaseAgent):
    """
    Handles cart rescue, payment failures, and re-engagement.
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    FUNCTIONS = [
        "hold_inventory",
        "send_payment_link",
        "apply_discount",
        "find_alternatives",
        "get_cart"
    ]
    
    def __init__(self):
        super().__init__(
            name="rescue",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )

    async def handle_abandonment(self, user_id: str, cart: Cart, context: Context) -> Response:
        """
        Handles a cart abandonment event from the Event Bus.
        """
        # First, use Gemini to analyze the likely reason for abandonment
        reason = await self._analyze_abandonment_reason(cart, context)
        
        prompt = f"""
        SITUATION:
        A user abandoned their cart {context.minutes_since_activity} minutes ago.
        
        CART CONTENTS:
        {cart.to_text()}
        
        LIKELY REASON (based on our analysis): {reason}
        
        USER CONTEXT:
        {context.to_text()}
        
        Based on the likely reason, generate a single, helpful, non-pushy rescue message.
        - If the reason is 'price shock', maybe offer to hold the items.
        - If the reason is 'size uncertainty', offer help.
        - If the item is low stock, you can mention it (but only if true).
        
        Decide if a function call is needed (e.g., hold_inventory).
        """
        
        gemini_response = await self.gemini_client.generate(
            prompt=prompt,
            system_prompt=self.system_prompt,
            functions=["hold_inventory", "apply_discount"]
        )
        
        # ... logic to execute function calls and send message via WhatsApp/Push ...
        
        return self._format_response(gemini_response)

    async def _analyze_abandonment_reason(self, cart: Cart, context: Context) -> str:
        """Use Gemini to analyze likely abandonment reason."""
        prompt = f"""
        Analyze why this user likely abandoned their cart based on their session:
        
        CART: {cart.to_text()}
        SESSION HISTORY: {context.session_history_summary}
        
        Possible reasons:
        - Price shock (viewed cart multiple times, high total value)
        - Size uncertainty (viewed size chart, multiple sizes in cart)
        - Distraction (sudden inactivity after being active)
        - Comparison shopping (viewed similar items from other brands)
        
        Output the most likely reason as a single phrase.
        """
        
        response = await self.gemini_client.generate(
            prompt=prompt,
            model="gemini-2.0-flash",
            temperature=0.3
        )
        
        return response.text.strip()
```
This event-driven, analytical approach allows the Rescue Agent to act as an intelligent safety net, significantly boosting conversion rates and customer satisfaction.
