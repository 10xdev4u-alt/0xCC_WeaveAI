# API: Catalog Functions

Catalog Functions are the tools our agents use to search, query, and understand the product catalog. They are primarily used by the **Discovery Agent**.

---

## 1. `search_catalog`

Searches the product catalog with a combination of natural language queries and structured filters.

### Schema (for Gemini)
```json
{
    "description": "Search product catalog with filters.",
    "parameters": {
        "type": "object",
        "properties": {
            "query": {
                "type": "string",
                "description": "Natural language search query (e.g., 'kurta for wedding')"
            },
            "category": {
                "type": "string",
                "description": "Product category to filter by (e.g., 'ethnic_wear')"
            },
            "min_price": {
                "type": "number",
                "description": "Minimum price filter"
            },
            "max_price": {
                "type": "number",
                "description": "Maximum price filter"
            },
            "size": {
                "type": "string",
                "description": "Size filter (e.g., 'M', '40')"
            },
            "color": {
                "type": "string",
                "description": "Color filter (e.g., 'blue')"
            },
            "limit": {
                "type": "integer",
                "description": "Maximum number of results to return",
                "default": 10
            }
        },
        "required": ["query"]
    }
}
```

### Python Definition
```python
async def search_catalog(
    query: str,
    category: Optional[str] = None,
    min_price: Optional[float] = None,
    max_price: Optional[float] = None,
    size: Optional[str] = None,
    color: Optional[str] = None,
    limit: int = 10
) -> List[Dict]:
    """Implementation connects to catalog service (e.g., Elasticsearch or a vector search on product embeddings)."""
    pass
```

---

## 2. `check_inventory`

Checks the real-time inventory for a specific product, optionally for a specific store.

### Schema (for Gemini)
```json
{
    "description": "Check real-time inventory for a product.",
    "parameters": {
        "type": "object",
        "properties": {
            "product_id": {
                "type": "string",
                "description": "The unique identifier of the product."
            },
            "store_id": {
                "type": "string",
                "description": "Optional: The specific store ID to check."
            },
            "size": {
                "type": "string",
                "description": "Optional: The specific size to check availability for."
            }
        },
        "required": ["product_id"]
    }
}
```

### Python Definition
```python
async def check_inventory(
    product_id: str,
    store_id: Optional[str] = None,
    size: Optional[str] = None
) -> Dict:
    """
    Returns:
        {
            "available": bool,
            "quantity": int,
            "stores": [{"store_id": str, "quantity": int}]
        }
    """
    pass
```

---

## 3. `get_similar_products`

Finds products that are visually or stylistically similar to a given product. This is often used for "You might also like" recommendations.

### Schema (for Gemini)
```json
{
    "description": "Get visually or stylistically similar products to a given product.",
    "parameters": {
        "type": "object",
        "properties": {
            "product_id": {
                "type": "string",
                "description": "The ID of the product to find similarities for."
            },
            "limit": {
                "type": "integer",
                "description": "Maximum number of similar products to return.",
                "default": 5
            }
        },
        "required": ["product_id"]
    }
}
```

### Python Definition
```python
async def get_similar_products(
    product_id: str,
    limit: int = 5
) -> List[Dict]:
    """Implementation uses vector similarity search on the product_embeddings index."""
    pass
```
