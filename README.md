# DummyJSON Carts API

The DummyJSON Carts API provides sample e-commerce cart data for learning and practicing how RESTful APIs work. This document could be used by software developers or testers. The base URL here is `https://dummyjson.com/carts` and it doesn't need any specific setup before being used.

The following endpoints are covered in this document.

- `GET /carts` — get a list of all carts

## Get All Carts

**Method:** `GET`
**Path:** `/carts`

This endpoint returns a paginated list of carts. Each cart contains the products a user has added, along with totals calculated for that cart.

Each cart object includes fields such as `id`, `products`, `total`, `discountedTotal` and `userId`. Each product inside a cart's `products` array includes fields such as `id`, `title`, `price` and `quantity`.

**Response Fields:**

| Field | Description |
|---|---|
| `carts` | Array of cart objects for this page of results |
| `total` | Total number of carts available |
| `skip` | Number of carts skipped before this page begins |
| `limit` | Maximum number of carts returned per request |

**Cart Fields:**

| Field | Description |
|---|---|
| `id` | Unique ID of the cart |
| `products` | Array of the products in the cart |
| `total` | Sum of every product's `total` in the cart, before discounts |
| `discountedTotal` | Sum of every product's `discountedTotal` in the cart, after discounts |
| `userId` | ID of the user who owns the cart |
| `totalProducts` | Number of different products in the cart |
| `totalQuantity` | Total number of units across all products in the cart |

**Product Fields (inside each cart's `products` array):**

The fields `id`, `title`, `price`, `quantity` and `thumbnail` are self-explanatory. The calculated fields are described below.

| Field | Description |
|---|---|
| `total` | `price` multiplied by `quantity`, before the discount |
| `discountPercentage` | Discount applied to this product, as a percentage |
| `discountedTotal` | The product's `total` after the discount is applied |

**Response Example:**

The example below shows only one cart, and only one of its four products, for brevity. The `thumbnail` field is also omitted. The cart-level totals still reflect all four products in that cart.

```json
{
  "carts": [
    {
      "id": 1,
      "products": [
        {
          "id": 162,
          "title": "Blue Frock",
          "price": 29.99,
          "quantity": 4,
          "total": 119.96,
          "discountPercentage": 12.13,
          "discountedTotal": 105.41
        }
      ],
      "total": 13037.88,
      "discountedTotal": 11510.81,
      "userId": 1,
      "totalProducts": 4,
      "totalQuantity": 12
    }
  ],
  "total": 208,
  "skip": 0,
  "limit": 30
}
```

**Possible Responses:**

| Status | Meaning |
|---|---|
| `200 OK` | The list of carts is returned |