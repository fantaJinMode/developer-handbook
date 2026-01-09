# 07 - DataLoader: Solving the N+1 Problem

> **Goal**: Master DataLoader to optimize GraphQL performance through batching and caching

---

## 📖 What is DataLoader?

DataLoader is a **utility library** that provides:

1. **Batching** - Combine multiple requests into one
2. **Caching** - Avoid duplicate requests in the same query
3. **Simplicity** - Works automatically once configured

Created by Facebook specifically for GraphQL.

---

## 🎯 The Problem: Recap of N+1

### Scenario: Get 10 Posts with Authors

```graphql
query {
  posts {           # Query 1: Get 10 posts
    title
    author {        # Queries 2-11: Get each author
      name
    }
  }
}
```

**Without DataLoader:**
```
Query 1: SELECT * FROM posts
Query 2: SELECT * FROM users WHERE id = 1
Query 3: SELECT * FROM users WHERE id = 2
Query 4: SELECT * FROM users WHERE id = 1  ← Duplicate!
Query 5: SELECT * FROM users WHERE id = 3
... (6 more queries)

Total: 11 database queries
```

---

## 💡 The DataLoader Solution

```
WITHOUT DataLoader:
┌─────────────────────────────────────────┐
│ Get author for post 1 → DB Query        │
│ Get author for post 2 → DB Query        │
│ Get author for post 3 → DB Query        │
└─────────────────────────────────────────┘

WITH DataLoader:
┌─────────────────────────────────────────┐
│ Get author for post 1 ┐                 │
│ Get author for post 2 ├─→ ONE DB Query  │
│ Get author for post 3 ┘                 │
│                                          │
│ WHERE id IN (1,2,3)                     │
└─────────────────────────────────────────┘
```

---

## 🔧 Basic Implementation

### Step 1: Install

```bash
npm install dataloader
```

### Step 2: Create Batch Function

```javascript
import DataLoader from 'dataloader'

async function batchLoadUsers(userIds) {
  // Make ONE query for all IDs
  const users = await db.query(
    'SELECT * FROM users WHERE id = ANY($1)',
    [userIds]
  )
  
  // Return in SAME ORDER as input
  return userIds.map(id => 
    users.find(u => u.id === id) || null
  )
}
```

### Step 3: Create Loader

```javascript
const userLoader = new DataLoader(batchLoadUsers)
```

### Step 4: Use in Resolver

```javascript
Post: {
  author: (parent, _, context) => {
    return context.loaders.user.load(parent.authorId)
  }
}
```

---

## 🔑 Key Takeaways

✅ DataLoader **batches** multiple requests  
✅ DataLoader **caches** results per request  
✅ Create **fresh loaders** per request  
✅ Return results in **same order**  
✅ Reduces queries by 10x or more  

---

**[← Previous](./06-N-Plus-One-Problem.md)** | **[README](./README.md)** | **[Next →](./08-Advanced-Types.md)**
