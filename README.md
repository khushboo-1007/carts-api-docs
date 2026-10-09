# DummyJSON Carts API

The DummyJSON Carts API provides sample e-commerce cart data for learning and practicing how RESTful APIs work. This document could be used by software developers or testers. The base URL here is `https://dummyjson.com/carts` and it doesn't need any specific setup before being used.

The following endpoints are covered in this document.

- `GET /carts` — get a list of all carts
- `GET /carts/{id}` — find the cart with the mentioned id
- `GET /carts/user/{id}` — find the carts that belong to the mentioned user

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

## Find Cart by ID

**Method:** `GET`
**Path:** `/carts/{id}`

**Path Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `id` | Yes | The unique ID of the cart to retrieve |

This endpoint returns a single cart matching the given ID, along with the products in it and the totals calculated for that cart. The response is one cart object, not a list, so it has no `carts` array and no `total`, `skip` or `limit` fields. The cart object has the same fields described under Get All Carts.

**Response Example:**

The example below shows only one of the cart's four products, for brevity. The `thumbnail` field is also omitted. The cart-level totals still reflect all four products.

```json
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
```

**Possible Responses:**

| Status | Meaning |
|---|---|
| `200 OK` | The cart with the given ID is returned |
| `404 Not Found` | No cart exists with the given ID; an error message is returned |

## Find Carts by User ID

**Method:** `GET`
**Path:** `/carts/user/{id}`

**Path Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `id` | Yes | The unique ID of the user whose carts you want to retrieve |

This endpoint returns the carts that belong to the given user. The carts are returned in a `carts` array, even when the user has only one cart. Each cart object has the same fields described under Get All Carts.

**Response Fields:**

| Field | Description |
|---|---|
| `carts` | Array of the carts that belong to the user |
| `total` | Number of carts that belong to this user (not the number of carts in the whole system) |
| `skip` | Number of carts skipped before this list begins |
| `limit` | Number of carts returned in this response |

**Response Example:**

The example below is the response for a user who has one cart. Only one of that cart's four products is shown, for brevity, and the `thumbnail` field is also omitted. The cart-level totals still reflect all four products.

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
  "total": 1,
  "skip": 0,
  "limit": 1
}
```

**Possible Responses:**

| Status | Meaning |
|---|---|
| `200 OK` | The carts that belong to the user are returned |
| `404 Not Found` | No user exists with the given ID; an error message is returned |