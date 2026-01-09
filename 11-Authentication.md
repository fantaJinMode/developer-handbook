# 11 - Authentication

> **Goal**: Implement JWT-based authentication

## 📖 Authentication vs Authorization

```
AUTHENTICATION: "Who are you?" (Identity verification)
AUTHORIZATION:  "What can you do?" (Permission check)
```

## 🔧 JWT Implementation

### Install Dependencies

```bash
npm install jsonwebtoken bcrypt
```

### Generate Token

```javascript
import jwt from 'jsonwebtoken'
import bcrypt from 'bcrypt'

Mutation: {
  login: async (_, { email, password }) => {
    const user = await db.findUserByEmail(email)
    if (!user) throw new Error('Invalid credentials')
    
    const valid = await bcrypt.compare(password, user.passwordHash)
    if (!valid) throw new Error('Invalid credentials')
    
    const token = jwt.sign(
      { userId: user.id },
      process.env.JWT_SECRET,
      { expiresIn: '7d' }
    )
    
    return { token, user }
  }
}
```

### Verify Token (Context)

```javascript
context: async ({ req }) => {
  const token = req.headers.authorization?.replace('Bearer ', '')
  
  if (token) {
    try {
      const decoded = jwt.verify(token, process.env.JWT_SECRET)
      const user = await db.findUserById(decoded.userId)
      return { user }
    } catch (err) {
      return { user: null }
    }
  }
  
  return { user: null }
}
```

### Protected Resolver

```javascript
Mutation: {
  createPost: (_, { input }, { user }) => {
    if (!user) {
      throw new Error('You must be logged in')
    }
    
    return db.createPost({ ...input, authorId: user.id })
  }
}
```

## 🔑 Key Takeaways

✅ Use JWT for stateless auth  
✅ Verify tokens in context  
✅ Check user in resolvers  
✅ Never expose passwords  

**[← Previous](./10-Pagination.md)** | **[README](./README.md)** | **[Next →](./12-Authorization.md)**
