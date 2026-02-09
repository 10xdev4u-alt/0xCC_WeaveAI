# Bridge Agent

The Bridge Agent is one of WEAVE's most innovative components, designed to seamlessly connect a user's digital shopping journey with their physical, in-store experience. Its purpose is to eliminate the "digital to physical" amnesia that plagues omnichannel retail.

## Core Responsibilities

*   **In-Store Reservations:** Allows users to reserve items online for trial or pickup at a physical store.
*   **Contextual Handoff:** Prepares store associates with a full briefing on an arriving customer's preferences, history, and intent.
*   **Phygital Experience Management:** Manages trial room assignments and sends timely notifications to both the customer and the store staff.

## Triggers

The Bridge Agent is typically invoked when a user expresses intent to interact with a physical store.
*   User explicitly asks, "Can I try this on in a store?"
*   User asks about product availability at a nearby location.
*   User physically scans a QR code upon entering a WEAVE-enabled store.

## System Prompt

The agent's prompt focuses on efficient coordination and creating a "wow" moment for the customer's store visit.

```
You are WEAVE's Bridge Agent. Your mission is to seamlessly connect a user's online shopping journey to an exceptional in-store experience.

Your role as coordinator:
- Help users find the best store for their needs.
- Confirm and reserve items for them to try on.
- Prepare the store associate with a powerful, context-rich briefing.
- Ensure the customer feels recognized and valued from the moment they walk in.

Your approach when bridging a user to a store:
1. Confirm the desired items and the most convenient store location.
2. Use your functions to check real-time store inventory.
3. If available, reserve the items and, if possible, a trial room.
4. Generate a comprehensive briefing for the store associate, including the customer's Style DNA, reserved items, and potential upsell suggestions.
5. Confirm the successful reservation with the user, providing all necessary details.
```

## Authorized Functions

*   `get_store_info`: To find store locations, hours, and contact details.
*   `check_inventory`: To check stock for specific items at a specific store.
*   `reserve_item_for_store`: To place a hold on items for a customer.
*   `reserve_trial_room`: To pre-book a trial room.
*   `notify_store_associate`: To push the final briefing to the associate's in-store device.

## Example Implementation

The following code illustrates the two primary flows for the Bridge Agent: handling an online reservation request and handling a customer's arrival at the store.

```python
# agents/bridge_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import UserInput, Context, Response

class BridgeAgent(BaseAgent):
    """
    Handles the digital-to-physical handoff.
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    FUNCTIONS = [
        "get_store_info",
        "check_inventory",
        "reserve_item_for_store",
        "reserve_trial_room",
        "notify_store_associate"
    ]
    
    def __init__(self):
        super().__init__(
            name="bridge",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )

    async def handle_store_reservation_request(self, user_input: UserInput, context: Context) -> Response:
        """Handles a user's online request to try items in a store."""
        # This flow would use Gemini to confirm items, find the best store,
        # and then use the 'reserve_item_for_store' function.
        # Finally, it would generate a confirmation message for the user.
        pass

    async def handle_store_arrival(self, user_id: str, store_id: str, context: Context) -> dict:
        """
        Handles a user's arrival at a store, triggered by a QR scan.
        This is the core of the "phygital" handoff.
        """
        
        # 1. Get all relevant context for the user.
        reservations = await self._get_reservations(user_id, store_id) # Internal helper
        style_dna = context.style_dna
        recent_browsing = context.recent_browsing_history
        
        # 2. Use Gemini to generate a high-quality briefing for the associate.
        briefing_json = await self._generate_associate_briefing(
            reservations,
            style_dna,
            recent_browsing
        )
        
        # 3. Use a function to push this briefing to the associate's tablet.
        await self.gemini_client.execute_function(
            "notify_store_associate",
            store_id=store_id,
            user_id=user_id,
            briefing=briefing_json
        )
        
        # 4. Generate a welcoming message for the customer's phone.
        welcome_message = f"Welcome to WEAVE, {context.user_name}! We've let the team know you're here. Your items are ready in Trial Room 3."
        
        return {"status": "success", "welcome_message": welcome_message}

    async def _generate_associate_briefing(self, reservations, style_dna, recent_browsing) -> dict:
        """Use Gemini to generate a comprehensive briefing for the store associate."""
        prompt = f"""
        Generate a concise store associate briefing for an arriving customer.

        RESERVED ITEMS:
        {reservations}
        
        CUSTOMER'S STYLE DNA:
        {style_dna}
        
        RECENTLY VIEWED ONLINE:
        {recent_browsing}
        
        Generate a briefing with these exact JSON keys:
        - "greeting": A personalized greeting for the customer.
        - "reserved_summary": A summary of what's waiting for them.
        - "style_notes": 2-3 bullet points on their style preferences.
        - "upsell_suggestions": Top 3 specific product suggestions with reasons why.
        - "important_context": Any other key info (e.g., "Shopping for a wedding").
        """
        
        response = await self.gemini_client.generate(
            prompt=prompt,
            model="gemini-2.0-flash",
            temperature=0.5
        )
        
        return self.gemini_client.parse_json(response.text)
```
This agent provides a powerful, tangible link between the digital and physical worlds, creating a "wow" experience that drives both sales and customer loyalty.
