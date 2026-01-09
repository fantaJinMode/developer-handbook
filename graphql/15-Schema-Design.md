# 15 - Schema Design Best Practices

> **Goal**: Design production-ready schemas

## ✅ Best Practices

### 1. Design for Clients

```graphql
# ❌ Bad: Database structure
type Post {
  user_id: ID!
  created_at: String
}

# ✅ Good: Client-friendly
type Post {
  author: User!
  createdAt: String!
}
```

### 2. Use Descriptive Names

```graphql
# ❌ Bad
type Query {
  get(id: ID!): Post
}

# ✅ Good
type Query {
  post(id: ID!): Post
}
```

### 3. Nullable Strategy

```graphql
type User {
  id: ID!           # Always required
  name: String!     # Always required
  bio: String       # Optional
}
```

### 4. Input Types

```graphql
# ✅ Always use input types
input CreatePostInput {
  title: String!
  content: String!
}

type Mutation {
  createPost(input: CreatePostInput!): Post!
}
```

### 5. Pagination

```graphql
# ✅ Use Connection pattern
type PostConnection {
  edges: [PostEdge!]!
  pageInfo: PageInfo!
}
```

## 🔑 Key Takeaways

✅ Design for clients, not database  
✅ Use descriptive names  
✅ Think carefully about nullability  
✅ Always use Input types  
✅ Use Connection pattern for lists  

**[← Previous](./14-File-Uploads.md)** | **[README](./README.md)** | **[Next →](./Project-3-Setup.md)**
