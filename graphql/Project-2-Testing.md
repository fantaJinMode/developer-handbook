# Project 2: E-Commerce - Testing

## 🧪 Test Queries

### Test 1: Get Products (DataLoader in Action)

```graphql
query GetProducts {
  products(first: 5) {
    edges {
      node {
        id
        name
        price
        category {
          name
        }
      }
      cursor
    }
    pageInfo {
      hasNextPage
      endCursor
    }
    totalCount
  }
}
```

**Check console**: You should see batched category queries!

### Test 2: Pagination

```graphql
# First page
query Page1 {
  products(first: 2) {
    edges {
      node { name }
      cursor
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}

# Second page (use endCursor from Page1)
query Page2 {
  products(first: 2, after: "YOUR_CURSOR_HERE") {
    edges {
      node { name }
    }
  }
}
```

### Test 3: Create Product with Validation

```graphql
mutation CreateProduct {
  createProduct(input: {
    name: "New Gadget"
    description: "Amazing device"
    price: 99.99
    categoryId: "1"
    stock: 100
  }) {
    id
    name
    category {
      name
    }
  }
}
```

## ✅ Success Criteria

- [ ] DataLoader batches queries (check console)
- [ ] Pagination works correctly
- [ ] Validation errors are clear
- [ ] No N+1 problems

## 🎉 Congratulations!

You've completed Project 2 with optimization!

**Next**: [11-Authentication.md](./11-Authentication.md)

**[← Previous](./Project-2-Implementation.md)** | **[README](./README.md)** | **[Next →](./11-Authentication.md)**
