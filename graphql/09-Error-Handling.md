# 09 - Error Handling

> **Goal**: Handle errors gracefully in GraphQL

## 📖 GraphQL Error Structure

```json
{
  "data": { "user": null },
  "errors": [{
    "message": "User not found",
    "path": ["user"],
    "extensions": { "code": "NOT_FOUND" }
  }]
}
```

## 🔧 Custom Errors

```javascript
import { GraphQLError } from 'graphql'

Query: {
  user: (_, { id }) => {
    const user = users.find(u => u.id === id)
    
    if (!user) {
      throw new GraphQLError('User not found', {
        extensions: {
          code: 'NOT_FOUND',
          userId: id
        }
      })
    }
    
    return user
  }
}
```

## ✅ Best Practices

- Use descriptive error messages
- Add error codes in extensions
- Include helpful context
- Don't expose sensitive data

## 🔑 Key Takeaways

✅ Use GraphQLError for custom errors  
✅ Add codes in extensions  
✅ Provide helpful messages  

**[← Previous](./08-Advanced-Types.md)** | **[README](./README.md)** | **[Next →](./10-Pagination.md)**
