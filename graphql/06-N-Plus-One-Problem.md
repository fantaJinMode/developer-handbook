# 06 - The N+1 Problem

> **Goal**: Understand the most common GraphQL performance issue

## 📖 What is N+1?

When fetching related data causes N additional queries.

```graphql
query {
  posts {           # 1 query
    title
    author {        # N queries (one per post)
      name
    }
  }
}
```

## 📊 The Problem

```
Query 1: SELECT * FROM posts (returns 10 posts)
Query 2: SELECT * FROM users WHERE id = 1
Query 3: SELECT * FROM users WHERE id = 2
... (8 more queries)

Total: 11 queries for 10 posts!
```

## 💡 The Solution

Use **DataLoader** to batch requests:
```
Query 1: SELECT * FROM posts
Query 2: SELECT * FROM users WHERE id IN (1,2,3...)

Total: 2 queries!
```

## 🔑 Key Takeaways

✅ N+1 happens with nested data  
✅ Each related field = additional query  
✅ DataLoader solves this with batching  

**[← Previous](./05-Relationships.md)** | **[README](./README.md)** | **[Next →](./07-DataLoader.md)**
