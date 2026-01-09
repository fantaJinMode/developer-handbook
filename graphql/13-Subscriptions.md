# 13 - Subscriptions

> **Goal**: Implement real-time updates with WebSocket

## 📖 What are Subscriptions?

Real-time, push-based data updates.

```graphql
type Subscription {
  messageAdded(chatId: ID!): Message!
  postLiked(postId: ID!): Post!
}
```

## 🔧 Implementation

### Install Dependencies

```bash
npm install graphql-subscriptions
```

### Setup PubSub

```javascript
import { PubSub } from 'graphql-subscriptions'

const pubsub = new PubSub()

Mutation: {
  createMessage: async (_, { input }) => {
    const message = await db.createMessage(input)
    
    // Publish event
    pubsub.publish('MESSAGE_ADDED', {
      messageAdded: message
    })
    
    return message
  }
}

Subscription: {
  messageAdded: {
    subscribe: () => pubsub.asyncIterator(['MESSAGE_ADDED'])
  }
}
```

### Client Usage

```graphql
subscription {
  messageAdded(chatId: "123") {
    id
    text
    author {
      name
    }
  }
}
```

## 🔑 Key Takeaways

✅ Subscriptions enable real-time  
✅ Use PubSub for simple apps  
✅ Publish events in mutations  
✅ Subscribe with asyncIterator  

**[← Previous](./12-Authorization.md)** | **[README](./README.md)** | **[Next →](./14-File-Uploads.md)**
