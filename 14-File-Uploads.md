# 14 - File Uploads

> **Goal**: Handle file uploads in GraphQL

## 📖 Upload Scalar

```graphql
scalar Upload

type Mutation {
  uploadAvatar(file: Upload!): User!
}
```

## 🔧 Implementation

```javascript
import { GraphQLUpload } from 'graphql-upload'
import fs from 'fs'
import path from 'path'

const resolvers = {
  Upload: GraphQLUpload,
  
  Mutation: {
    uploadAvatar: async (_, { file }, { user }) => {
      if (!user) throw new Error('Authentication required')
      
      const { createReadStream, filename, mimetype } = await file
      
      // Validate
      if (!mimetype.startsWith('image/')) {
        throw new Error('Only images allowed')
      }
      
      // Save file
      const uniqueName = `${user.id}-${Date.now()}-${filename}`
      const filepath = path.join('uploads', uniqueName)
      
      await new Promise((resolve, reject) => {
        createReadStream()
          .pipe(fs.createWriteStream(filepath))
          .on('finish', resolve)
          .on('error', reject)
      })
      
      // Update user
      const updatedUser = await db.updateUser(user.id, {
        avatarUrl: `/uploads/${uniqueName}`
      })
      
      return updatedUser
    }
  }
}
```

## 🔑 Key Takeaways

✅ Use Upload scalar  
✅ Validate file types  
✅ Generate unique filenames  
✅ Store files securely  

**[← Previous](./13-Subscriptions.md)** | **[README](./README.md)** | **[Next →](./15-Schema-Design.md)**
