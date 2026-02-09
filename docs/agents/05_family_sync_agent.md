# Family Sync Agent

The Family Sync Agent is WEAVE's answer to the collectivist nature of Indian shopping. It addresses the 73% of purchasing decisions that are influenced by family by transforming the solitary online cart into a collaborative, multi-user experience.

## Core Responsibilities

*   **Shared Cart Management:** Creates and manages shopping carts that multiple users can view and contribute to.
*   **Voting & Feedback System:** Allows family members to vote ("approve," "reject") and comment on items in a shared cart.
*   **Role-Based Permissions:** Manages different roles for members (e.g., `viewer`, `contributor`, `approver`, `payer`).

## Triggers

This agent is activated when a user wants to involve others in their shopping process.
*   User clicks a "Share Cart" or "Get Feedback" button.
*   User explicitly types, "I want to show this to my mom."
*   A user who is part of an existing family group initiates a shopping session for a group-related occasion.

## System Prompt

The prompt for this agent focuses on its role as a facilitator and organizer for group decisions.

```
You are WEAVE's Family Sync Agent. Your purpose is to make group shopping simple, collaborative, and fun. You are an expert organizer.

Your role:
- Facilitate the creation of shared shopping carts.
- Invite members and clearly explain their roles.
- Tally votes and summarize feedback on products.
- Notify the group when a decision is reached or a payment is needed.

Your approach:
- Be clear and concise in your communication.
- Use simple language that everyone can understand.
- Act as a neutral facilitator, presenting information without bias.
- Ensure the process is smooth and respects the roles assigned to each member.
```

## Authorized Functions

*   `create_family_cart`: To initialize a new shared cart session.
*   `add_family_member`: To invite a user to a shared cart via phone number.
*   `submit_vote`: To log a member's vote and comments on a specific item.
*   `get_family_cart_status`: To retrieve the current state of a shared cart, including all items and votes.
*   `process_payment_for_group`: To handle payment logic once a cart is approved.

## Example Implementation

The following code illustrates how the Family Sync Agent might handle the creation of a new shared cart and the process of a member submitting a vote.

```python
# agents/family_sync_agent.py (Illustrative)

from .base_agent import BaseAgent
from ..schemas import UserInput, Context, Response

class FamilySyncAgent(BaseAgent):
    """
    Manages collaborative, multi-user shopping carts.
    """
    
    SYSTEM_PROMPT = "..." # As defined above
    FUNCTIONS = [
        "create_family_cart",
        "add_family_member",
        "submit_vote",
        "get_family_cart_status"
    ]
    
    def __init__(self):
        super().__init__(
            name="family_sync",
            system_prompt=self.SYSTEM_PROMPT,
            functions=self.FUNCTIONS
        )

    async def handle_creation_request(self, user_input: UserInput, context: Context) -> Response:
        """
        Handles a user's request to create a new family cart.
        """
        # 1. Use Gemini to understand the user's intent and extract details.
        # e.g., "I want to create a 'Diwali Shopping' cart with my sister."
        
        # 2. Call the function to create the cart in the backend.
        cart_details = await self.gemini_client.execute_function(
            "create_family_cart",
            creator_id=context.user_id,
            name="Diwali Shopping" # Extracted by Gemini
        )

        # 3. Use Gemini to generate a confirmation message with next steps.
        prompt = f"""
        A new family cart named '{cart_details['name']}' has been created.
        Generate a confirmation message for the user, and ask them who they'd like to invite.
        """
        response = await self.gemini_client.generate(prompt=prompt)
        return self._format_response(response)

    async def handle_vote_submission(self, user_input: UserInput, context: Context) -> Response:
        """
        Handles a family member submitting a vote on an item.
        """
        # 1. Use Gemini to parse the user's vote from their message.
        # e.g., "I don't like the red one, but the blue one is great!"
        
        parsed_vote = {
            "item_id": "red_kurta_123", # Extracted by Gemini
            "vote": "reject",
            "comment": "User doesn't like the red one."
        }
        
        # 2. Call the function to log the vote.
        await self.gemini_client.execute_function(
            "submit_vote",
            cart_id=context.family_cart_id,
            member_id=context.user_id,
            item_id=parsed_vote["item_id"],
            vote=parsed_vote["vote"],
            comment=parsed_vote["comment"]
        )
        
        # 3. Get the updated cart status and generate a summary for the group.
        cart_status = await self.gemini_client.execute_function(
            "get_family_cart_status",
            cart_id=context.family_cart_id
        )
        
        prompt = f"""
        A vote has been submitted. The new status of the family cart is:
        {cart_status}
        
        Generate a brief notification for all group members summarizing the change.
        """
        
        response = await self.gemini_client.generate(prompt=prompt)
        
        # Logic to broadcast this response to all members
        await self._broadcast_to_group(context.family_cart_id, response.text)

        return self._format_response(response) # To the original voter
```
By digitalizing the collaborative shopping process, the Family Sync Agent directly addresses a core cultural behavior, significantly reducing friction and increasing conversion for group-related purchases.
