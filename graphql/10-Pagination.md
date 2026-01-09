# 10 - Pagination

> **Goal**: Handle large datasets efficiently

## 📖 Three Pagination Patterns

### 1. Offset-Based (Simple)

```graphql
type Query {
  posts(limit: Int!, offset: Int!): [Post!]!
}
```

### 2. Cursor-Based (Recommended)

```graphql
type PostConnection {
  edges: [PostEdge!]!
  pageInfo: PageInfo!
}

type PostEdge {
  node: Post!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  endCursor: String
}
```

### 3. Page-Based

```graphql
type Query {
  posts(page: Int!, pageSize: Int!): PostPage!
}
```

## 🔧 Cursor Implementation

```javascript
Query: {
  posts: (_, { first, after }) => {
    const afterId = after ? decodeCursor(after) : 0
    const posts = db.query(
      'SELECT * FROM posts WHERE id > $1 LIMIT $2',
      [afterId, first + 1]
    )
    
    const hasNextPage = posts.length > first
    const edges = posts.slice(0, first).map(post => ({
      node: post,
      cursor: encodeCursor(post.id)
    }))
    
    return {
      edges,
      pageInfo: {
        hasNextPage,
        endCursor: edges[edges.length - 1]?.cursor
      }
    }
  }
}
```

## 🔑 Key Takeaways

✅ Use cursor-based for infinite scroll  
✅ Use offset for simple pagination  
✅ Always include hasNextPage  

**[← Previous](./09-Error-Handling.md)** | **[README](./README.md)** | **[Next →](./Project-2-Setup.md)**
