# 16 - Common Mistakes

> **Goal**: Avoid common GraphQL pitfalls

## 🚨 Top 10 Mistakes

### 1. Not Using DataLoader
```javascript
// ❌ N+1 problem
Post: {
  author: (parent) => db.query('SELECT * FROM users WHERE id = $1', [parent.authorId])
}

// ✅ Use DataLoader
Post: {
  author: (parent, _, { loaders }) => loaders.user.load(parent.authorId)
}
```

### 2. Sharing Loaders Across Requests
```javascript
// ❌ Security issue
const loaders = createLoaders()
context: () => ({ loaders })

// ✅ Fresh per request
context: () => ({ loaders: createLoaders() })
```

### 3. Not Validating Input
```javascript
// ❌ No validation
createUser: (_, { input }) => db.createUser(input)

// ✅ Validate first
createUser: (_, { input }) => {
  if (!input.email.includes('@')) throw new Error('Invalid email')
  return db.createUser(input)
}
```

### 4. Exposing Sensitive Data
```javascript
// ❌ Shows to everyone
type User {
  email: String!
  password: String!
}

// ✅ Field-level protection
User: {
  email: (parent, _, { user }) => {
    return user?.id === parent.id ? parent.email : null
  }
}
```

### 5. Not Using Input Types
```javascript
// ❌ Many arguments
createPost(title: String!, content: String!, tags: [String!]): Post!

// ✅ Input type
createPost(input: CreatePostInput!): Post!
```

## 🔑 Key Takeaways

✅ Always use DataLoader  
✅ Create fresh loaders per request  
✅ Validate all inputs  
✅ Protect sensitive fields  
✅ Use Input types  

**[← Previous](./15-Schema-Design.md)** | **[README](./README.md)** | **[Next →](./17-Performance-Tips.md)**
