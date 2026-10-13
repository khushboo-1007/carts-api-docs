# DummyJSON Carts API

The DummyJSON Carts API provides sample e-commerce cart data for learning and practicing how RESTful APIs work. This document could be used by software developers or testers. The base URL here is `https://dummyjson.com/carts` and it doesn't need any specific setup before being used.

The following endpoints are covered in this document.

- `GET /carts` — get a list of all carts
- `GET /carts/{id}` — find the cart with the mentioned id
- `GET /carts/user/{id}` — find the carts that belong to the mentioned user
- `POST /carts/add` — add a new cart
- `PUT /carts/{id}` — update an existing cart

## Get All Carts

**Method:** `GET`
**Path:** `/carts`

This endpoint returns a paginated list of carts. Each cart contains the products a user has added, along with totals calculated for that cart.

Each cart object includes fields such as `id`, `products`, `total`, `discountedTotal` and `userId`. Each product inside a cart's `products` array includes fields such as `id`, `title`, `price` and `quantity`.

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `limit` | No | Maximum number of carts to return. If omitted, 30 carts are returned. |
| `skip` | No | Number of carts to skip before the list begins. If omitted, no carts are skipped (`0`). |

For example, `GET /carts?limit=3&skip=10` skips the first 10 carts and returns the next 3 (carts 11, 12 and 13).

**Response Fields:**

| Field | Description |
|---|---|
| `carts` | Array of cart objects for this page of results |
| `total` | Total number of carts available. This stays the same whatever `limit` and `skip` you use. |
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

## Add a New Cart

**Method:** `POST`

**Path:** `/carts/add`

This endpoint simulates adding a new cart and returns the new cart with a newly generated `id`. The request body contains a `userId` and a `products` array, and each item in `products` has an `id` and a `quantity`. The server fills in the rest: it looks up each product's details and calculates the totals, so the response contains more fields than you sent.

> **Note:** DummyJSON simulates this request. The new cart is returned in the response, but it is not added to the server — a subsequent `GET /carts/{id}` call using the returned `id` will return `404 Not Found`.

**Request Body:**

```json
{
  "userId": 1,
  "products": [
    { "id": 144, "quantity": 4 },
    { "id": 98, "quantity": 1 }
  ]
}
```

**Response Example:**

Inside each product, the discounted amount is named `discountedPrice` in this response, not `discountedTotal` as in the GET endpoints above. The `thumbnail` field is omitted from the example below for brevity.

```json
{
  "id": 209,
  "products": [
    {
      "id": 144,
      "title": "Cricket Helmet",
      "price": 44.99,
      "quantity": 4,
      "total": 179.96,
      "discountPercentage": 9.64,
      "discountedPrice": 163
    },
    {
      "id": 98,
      "title": "Rolex Submariner Watch",
      "price": 13999.99,
      "quantity": 1,
      "total": 13999.99,
      "discountPercentage": 5.05,
      "discountedPrice": 13293
    }
  ],
  "total": 14179.949999999999,
  "discountedTotal": 13456,
  "userId": 1,
  "totalProducts": 2,
  "totalQuantity": 5
}
```

**Possible Responses:**

| Status | Meaning |
|---|---|
| `201 Created` | The simulated cart is returned with a new `id` (not actually saved) |
| `400 Bad Request` | The request was rejected as invalid; an error message is returned |
| `404 Bad Request` | The cart with the mentioned Id was not found |


## Update a Cart

**Method:** `PUT`

**Path:** `/carts/{id}`

**Path Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `id` | Yes | The unique ID of the cart to update |

This endpoint simulates updating a cart and returns the updated cart, including the newly added products. The request body contains a `products` array, where each item has an `id` and a `quantity`, and a `merge` field. The server fills in the rest: it looks up each product's details and calculates the totals, so the response contains more fields than you sent.

With `"merge": true`, the products already in the cart are kept and the new products are added to them.

> **Note:** DummyJSON simulates this request. The updated cart is returned in the response, but it is not saved to the server — a subsequent `GET /carts/{id}` call will show the original, unmodified cart.

**Request Body:**

```json
{
  "merge": true,
  "products": [
    { "id": 1, "quantity": 1 }
  ]
}
```

**Response Example:**

The example below is the response for cart `200`. It shows two of the cart's six products: one that was already in the cart, and the newly added product (`id` 1). The `thumbnail` field is omitted for brevity. The cart-level totals still reflect all six products. As in Add a New Cart, the discounted amount inside each product is named `discountedPrice`.

```json
{
  "id": 200,
  "products": [
    {
      "id": 87,
      "title": "Men Check Shirt",
      "price": 27.99,
      "quantity": 5,
      "total": 139.95,
      "discountPercentage": 11.38,
      "discountedPrice": 124
    },
    {
      "id": 1,
      "title": "Essence Mascara Lash Princess",
      "price": 9.99,
      "quantity": 1,
      "total": 9.99,
      "discountPercentage": 10.48,
      "discountedPrice": 9
    }
  ],
  "total": 22554.79,
  "discountedTotal": 21663,
  "userId": 200,
  "totalProducts": 6,
  "totalQuantity": 21
}
```

**Possible Responses:**

| Status | Meaning |
|---|---|
| `200 OK` | The simulated updated cart is returned (not actually saved) |
| `400 Bad Request` | The request was rejected as invalid; an error message is returned |
| `404 Not Found` | No cart exists with the given ID; an error message is returned |