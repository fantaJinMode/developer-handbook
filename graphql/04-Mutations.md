# 04 - Mutations

> **Goal**: Learn to create, update, and delete data using mutations

## 📖 What is a Mutation?

Mutations **modify data** (create, update, delete).

```graphql
type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User
  deleteUser(id: ID!): Boolean!
}
```

## 🔧 Basic Example

```javascript
Mutation: {
  createUser: (_, { input }) => {
    const user = {
      id: generateId(),
      ...input,
      createdAt: new Date().toISOString()
    }
    users.push(user)
    return user
  }
}
```

## ✅ Best Practices

- Use Input types for complex arguments
- Validate all input data
- Return the created/updated object
- Handle errors gracefully

## 🔑 Key Takeaways

✅ Mutations modify data  
✅ Use Input types  
✅ Always validate  
✅ Return meaningful data  

**[← Previous](./03-Queries-And-Resolvers.md)** | **[README](./README.md)** | **[Next →](./05-Relationships.md)**
