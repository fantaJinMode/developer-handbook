# 05 - Relationships

> **Goal**: Handle related data between types

## 📖 One-to-Many Relationships

```graphql
type User {
  id: ID!
  name: String!
  posts: [Post!]!  # User has many posts
}

type Post {
  id: ID!
  title: String!
  author: User!     # Post belongs to one user
}
```

## 🔧 Field Resolvers

```javascript
User: {
  posts: (parent) => {
    return posts.filter(p => p.authorId === parent.id)
  }
}

Post: {
  author: (parent) => {
    return users.find(u => u.id === parent.authorId)
  }
}
```

## 🔑 Key Takeaways

✅ Use field resolvers for relationships  
✅ Parent contains the parent object data  
✅ Filter/find related data based on foreign keys  

**[← Previous](./04-Mutations.md)** | **[README](./README.md)** | **[Next →](./Project-1-Setup.md)**
