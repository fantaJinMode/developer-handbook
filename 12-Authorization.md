# 12 - Authorization

> **Goal**: Implement role-based access control

## 📖 Authorization Patterns

### Field-Level Authorization

```javascript
User: {
  email: (parent, _, { user }) => {
    // Only show to self or admin
    if (user?.id === parent.id || user?.role === 'ADMIN') {
      return parent.email
    }
    return null
  }
}
```

### Role-Based Access

```javascript
function requireRole(user, role) {
  if (!user) throw new Error('Authentication required')
  if (user.role !== role) throw new Error('Insufficient permissions')
}

Mutation: {
  deleteUser: (_, { id }, { user }) => {
    requireRole(user, 'ADMIN')
    return db.deleteUser(id)
  }
}
```

### Resource Ownership

```javascript
Mutation: {
  updatePost: async (_, { id, input }, { user }) => {
    if (!user) throw new Error('Authentication required')
    
    const post = await db.getPost(id)
    if (post.authorId !== user.id) {
      throw new Error('You can only edit your own posts')
    }
    
    return db.updatePost(id, input)
  }
}
```

## 🔑 Key Takeaways

✅ Check permissions in resolvers  
✅ Use field-level for sensitive data  
✅ Verify resource ownership  
✅ Provide clear error messages  

**[← Previous](./11-Authentication.md)** | **[README](./README.md)** | **[Next →](./13-Subscriptions.md)**
