# Shopify Routing Reference

**App name:** `shopify`
**Base URL proxied:** `{subdomain}.myshopify.com`

The router automatically handles the store subdomain from your connection.

## Important: GraphQL API

Shopify is deprecating REST APIs in favor of GraphQL. All new development should use the GraphQL Admin API.

## GraphQL API Path Pattern

```
POST /shopify/admin/api/{version}/graphql.json
Content-Type: application/json
```

## API Versioning

**Always use the latest stable version first:** `2026-01`

Fallback versions (use only if latest fails):
1. `2025-10`
2. `2025-07`
3. `2025-04`
4. `2025-01`

Shopify releases quarterly versions (January, April, July, October). Each version is supported for 12 months. Always try the latest version first before falling back to older ones.

## Common GraphQL Queries

### Shop Info
```graphql
{
  shop {
    name
    email
    myshopifyDomain
    currencyCode
  }
}
```

### List Products
```graphql
{
  products(first: 10) {
    edges {
      node {
        id
        title
        handle
        status
        vendor
        productType
      }
      cursor
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

### Get Product
```graphql
{
  product(id: "gid://shopify/Product/123456") {
    id
    title
    handle
    status
    descriptionHtml
    priceRangeV2 {
      minVariantPrice {
        amount
        currencyCode
      }
    }
  }
}
```

### List Orders
```graphql
{
  orders(first: 10) {
    edges {
      node {
        id
        name
        createdAt
        displayFinancialStatus
        displayFulfillmentStatus
        totalPriceSet {
          shopMoney {
            amount
            currencyCode
          }
        }
      }
    }
  }
}
```

### Get Order
```graphql
{
  order(id: "gid://shopify/Order/123456") {
    id
    name
    createdAt
    displayFulfillmentStatus
    lineItems(first: 10) {
      edges {
        node {
          title
          quantity
        }
      }
    }
  }
}
```

### List Customers
```graphql
{
  customers(first: 10) {
    edges {
      node {
        id
        createdAt
        numberOfOrders
        state
      }
    }
  }
}
```

### List Collections
```graphql
{
  collections(first: 10) {
    edges {
      node {
        id
        title
        handle
      }
    }
  }
}
```

### List Webhook Subscriptions
```graphql
{
  webhookSubscriptions(first: 10) {
    edges {
      node {
        id
        topic
        endpoint {
          __typename
        }
      }
    }
  }
}
```

## Common GraphQL Mutations

### Create Product
```graphql
mutation {
  productCreate(input: {
    title: "New Product"
    vendor: "My Store"
    productType: "Clothing"
  }) {
    product {
      id
      title
      handle
    }
    userErrors {
      field
      message
    }
  }
}
```

### Update Product
```graphql
mutation {
  productUpdate(input: {
    id: "gid://shopify/Product/123456"
    title: "Updated Title"
    descriptionHtml: "<p>New description</p>"
  }) {
    product {
      id
      title
    }
    userErrors {
      field
      message
    }
  }
}
```

### Delete Product
```graphql
mutation {
  productDelete(input: {
    id: "gid://shopify/Product/123456"
  }) {
    deletedProductId
    userErrors {
      field
      message
    }
  }
}
```

## Example curl Request

```bash
curl -X POST '/shopify/admin/api/2026-01/graphql.json' \
  -H 'Content-Type: application/json' \
  -d '{"query":"{ shop { name email } }"}'
```

## Pagination

Shopify GraphQL uses cursor-based pagination:

```graphql
{
  products(first: 10, after: "cursor_from_previous_page") {
    edges {
      node { id title }
      cursor
    }
    pageInfo {
      hasNextPage
      hasPreviousPage
      endCursor
    }
  }
}
```

## Global IDs

Shopify uses Global IDs (GIDs) in GraphQL:
- Products: `gid://shopify/Product/123456`
- Orders: `gid://shopify/Order/123456`
- Customers: `gid://shopify/Customer/123456`
- Collections: `gid://shopify/Collection/123456`

## Cost and Rate Limiting

GraphQL responses include cost information:
```json
{
  "extensions": {
    "cost": {
      "requestedQueryCost": 10,
      "actualQueryCost": 10,
      "throttleStatus": {
        "maximumAvailable": 2000,
        "currentlyAvailable": 1990,
        "restoreRate": 100
      }
    }
  }
}
```

## Notes

- Authentication is automatic - the router injects the `X-Shopify-Access-Token` header
- Store subdomain is automatically determined from your connection
- All GraphQL requests use POST method
- Check `userErrors` array in mutation responses for validation errors
- Some customer fields (like `displayName`, `email`) require protected customer data approval

## Resources

- [GraphQL API Overview](https://shopify.dev/docs/api/admin-graphql/latest)
- [List Products](https://shopify.dev/docs/api/admin-graphql/latest/queries/products.md)
- [Get Product](https://shopify.dev/docs/api/admin-graphql/latest/queries/product.md)
- [Create Product](https://shopify.dev/docs/api/admin-graphql/latest/mutations/productCreate.md)
- [Update Product](https://shopify.dev/docs/api/admin-graphql/latest/mutations/productUpdate.md)
- [Delete Product](https://shopify.dev/docs/api/admin-graphql/latest/mutations/productDelete.md)
- [Bulk Create Product Variants](https://shopify.dev/docs/api/admin-graphql/latest/mutations/productVariantsBulkCreate.md)
- [Bulk Update Product Variants](https://shopify.dev/docs/api/admin-graphql/latest/mutations/productVariantsBulkUpdate.md)
- [Bulk Delete Product Variants](https://shopify.dev/docs/api/admin-graphql/latest/mutations/productVariantsBulkDelete.md)
- [List Orders](https://shopify.dev/docs/api/admin-graphql/latest/queries/orders.md)
- [Get Order](https://shopify.dev/docs/api/admin-graphql/latest/queries/order.md)
- [Create Draft Order](https://shopify.dev/docs/api/admin-graphql/latest/mutations/draftOrderCreate.md)
- [Complete Draft Order](https://shopify.dev/docs/api/admin-graphql/latest/mutations/draftOrderComplete.md)
- [Update Order](https://shopify.dev/docs/api/admin-graphql/latest/mutations/orderUpdate.md)
- [Close Order](https://shopify.dev/docs/api/admin-graphql/latest/mutations/orderClose.md)
- [Cancel Order](https://shopify.dev/docs/api/admin-graphql/latest/mutations/orderCancel.md)
- [List Customers](https://shopify.dev/docs/api/admin-graphql/latest/queries/customers.md)
- [Get Customer](https://shopify.dev/docs/api/admin-graphql/latest/queries/customer.md)
- [Create Customer](https://shopify.dev/docs/api/admin-graphql/latest/mutations/customerCreate.md)
- [Update Customer](https://shopify.dev/docs/api/admin-graphql/latest/mutations/customerUpdate.md)
- [Delete Customer](https://shopify.dev/docs/api/admin-graphql/latest/mutations/customerDelete.md)
- [Get Inventory Item](https://shopify.dev/docs/api/admin-graphql/latest/queries/inventoryItem.md)
- [Adjust Inventory Quantities](https://shopify.dev/docs/api/admin-graphql/latest/mutations/inventoryAdjustQuantities.md)
- [Set Inventory Quantities](https://shopify.dev/docs/api/admin-graphql/latest/mutations/inventorySetQuantities.md)
- [List Collections](https://shopify.dev/docs/api/admin-graphql/latest/queries/collections.md)
- [Get Collection](https://shopify.dev/docs/api/admin-graphql/latest/queries/collection.md)
- [Create Collection](https://shopify.dev/docs/api/admin-graphql/latest/mutations/collectionCreate.md)
- [Update Collection](https://shopify.dev/docs/api/admin-graphql/latest/mutations/collectionUpdate.md)
- [Delete Collection](https://shopify.dev/docs/api/admin-graphql/latest/mutations/collectionDelete.md)
- [List Webhook Subscriptions](https://shopify.dev/docs/api/admin-graphql/latest/queries/webhookSubscriptions.md)
- [Create Webhook Subscription](https://shopify.dev/docs/api/admin-graphql/latest/mutations/webhookSubscriptionCreate.md)
- [Update Webhook Subscription](https://shopify.dev/docs/api/admin-graphql/latest/mutations/webhookSubscriptionUpdate.md)
- [Delete Webhook Subscription](https://shopify.dev/docs/api/admin-graphql/latest/mutations/webhookSubscriptionDelete.md)
- [API Versioning](https://shopify.dev/docs/api/usage/versioning.md)
- [Rate Limits](https://shopify.dev/docs/api/usage/limits.md)
- [GraphQL Pagination](https://shopify.dev/docs/api/usage/pagination-graphql.md)
