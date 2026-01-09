# Project 1: Blog API - Implementation

## Step 1: Mock Data (data.js)

```javascript
export const users = [
  { id: '1', name: 'Alice', email: 'alice@example.com' },
  { id: '2', name: 'Bob', email: 'bob@example.com' }
]

export const posts = [
  { id: '1', title: 'GraphQL Basics', content: '...', authorId: '1' },
  { id: '2', title: 'Node.js Tips', content: '...', authorId: '2' }
]

export const comments = [
  { id: '1', text: 'Great post!', postId: '1', authorId: '2' },
  { id: '2', text: 'Very helpful!', postId: '1', authorId: '1' }
]
```

## Step 2: Schema (schema.js)

```javascript
export const typeDefs = `#graphql
  type User {
    id: ID!
    name: String!
    email: String!
    posts: [Post!]!
  }

  type Post {
    id: ID!
    title: String!
    content: String!
    author: User!
    comments: [Comment!]!
  }

  type Comment {
    id: ID!
    text: String!
    author: User!
    post: Post!
  }

  type Query {
    users: [User!]!
    user(id: ID!): User
    posts: [Post!]!
    post(id: ID!): Post
  }

  input CreatePostInput {
    title: String!
    content: String!
    authorId: ID!
  }

  type Mutation {
    createPost(input: CreatePostInput!): Post!
    createComment(postId: ID!, text: String!, authorId: ID!): Comment!
  }
`
```

## Step 3: Resolvers (resolvers.js)

```javascript
import { users, posts, comments } from './data.js'

let nextPostId = 3
let nextCommentId = 3

export const resolvers = {
  Query: {
    users: () => users,
    user: (_, { id }) => users.find(u => u.id === id),
    posts: () => posts,
    post: (_, { id }) => posts.find(p => p.id === id)
  },

  Mutation: {
    createPost: (_, { input }) => {
      const newPost = {
        id: String(nextPostId++),
        ...input,
        createdAt: new Date().toISOString()
      }
      posts.push(newPost)
      return newPost
    },

    createComment: (_, { postId, text, authorId }) => {
      const newComment = {
        id: String(nextCommentId++),
        text,
        postId,
        authorId,
        createdAt: new Date().toISOString()
      }
      comments.push(newComment)
      return newComment
    }
  },

  User: {
    posts: (parent) => posts.filter(p => p.authorId === parent.id)
  },

  Post: {
    author: (parent) => users.find(u => u.id === parent.authorId),
    comments: (parent) => comments.filter(c => c.postId === parent.id)
  },

  Comment: {
    author: (parent) => users.find(u => u.id === parent.authorId),
    post: (parent) => posts.find(p => p.id === parent.postId)
  }
}
```

## Step 4: Server (server.js)

```javascript
import { ApolloServer } from '@apollo/server'
import { startStandaloneServer } from '@apollo/server/standalone'
import { typeDefs } from './schema.js'
import { resolvers } from './resolvers.js'

const server = new ApolloServer({
  typeDefs,
  resolvers
})

const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 }
})

console.log(`🚀 Server ready at ${url}`)
```

## Step 5: Add to package.json

```json
{
  "type": "module",
  "scripts": {
    "start": "node server.js"
  }
}
```

## 🚀 Run It!

```bash
npm start
```

Visit: http://localhost:4000

**Next**: [Project-1-Testing.md](./Project-1-Testing.md)

**[← Previous](./Project-1-Setup.md)** | **[README](./README.md)** | **[Next →](./Project-1-Testing.md)**
