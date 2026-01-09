# 03 - Queries & Resolvers

> **Goal**: Learn how to read data from your GraphQL API using queries and resolvers

---

## 📖 What is a Query?

A **query** is how clients **request data** from your GraphQL API.

Think of it like a **SELECT** in SQL or a **GET** in REST.

```graphql
query {
  user(id: "1") {
    name
    email
  }
}
```

---

## 🎯 Defining Queries in Schema

In your schema, you define available queries in the `Query` type:

```graphql
type Query {
  # Get single user by ID
  user(id: ID!): User
  
  # Get all users
  users: [User!]!
  
  # Get posts with optional filter
  posts(limit: Int, published: Boolean): [Post!]!
  
  # Search
  searchUsers(query: String!): [User!]!
}
```

**Reading the syntax:**
```graphql
user(id: ID!): User
 │    │   │     │
 │    │   │     └─ Return type
 │    │   └─ Parameter type (required)
 │    └─ Parameter name
 └─ Query name
```

---

## 🔧 What is a Resolver?

A **resolver** is a **function that fetches the data** for a field.

```
CLIENT QUERY → GRAPHQL → RESOLVER → DATA SOURCE → RESPONSE
```

### Simple Example

```javascript
const resolvers = {
  Query: {
    user: (parent, args, context, info) => {
      // args.id contains the ID from the query
      return database.findUserById(args.id)
    }
  }
}
```

---

## 📊 Resolver Function Signature

Every resolver receives **4 arguments**:

```javascript
fieldResolver(parent, args, context, info)
```

### 1. **parent** (or root)
Data from the parent resolver

### 2. **args**
Arguments passed in the query

### 3. **context**
Shared data (database, auth, etc.)

### 4. **info**
Query metadata (rarely used)

---

## 🧩 Resolver Arguments Explained

### 1. Parent

```javascript
const resolvers = {
  Query: {
    user: () => {
      return { id: "1", name: "Alice" }
    }
  },
  User: {
    // parent is the User object from Query.user
    posts: (parent) => {
      console.log(parent) // { id: "1", name: "Alice" }
      return database.findPostsByUserId(parent.id)
    }
  }
}
```

---

### 2. Args

```javascript
const resolvers = {
  Query: {
    user: (parent, args) => {
      console.log(args) // { id: "1" }
      return database.findUserById(args.id)
    },
    
    posts: (parent, args) => {
      console.log(args) // { limit: 10, published: true }
      return database.findPosts({
        limit: args.limit,
        published: args.published
      })
    }
  }
}
```

---

### 3. Context

```javascript
// Server setup
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: () => {
    return {
      database: db,
      currentUser: getCurrentUser()
    }
  }
})

// Resolver usage
const resolvers = {
  Query: {
    me: (parent, args, context) => {
      // context contains shared data
      return context.database.findUserById(
        context.currentUser.id
      )
    }
  }
}
```

**Context is perfect for:**
- Database connections
- Authentication data
- Logging
- DataLoaders

---

### 4. Info (Advanced)

```javascript
const resolvers = {
  Query: {
    user: (parent, args, context, info) => {
      console.log(info.fieldName)      // "user"
      console.log(info.returnType)     // "User"
      console.log(info.parentType)     // "Query"
      // Contains full query AST
    }
  }
}
```

**Rarely used** - mainly for advanced optimization.

---

## 🔄 Resolver Execution Flow

### Query

```graphql
query {
  user(id: "1") {
    name
    email
    posts {
      title
    }
  }
}
```

### Execution

```
1. Query.user runs
   └─ Returns: { id: "1", name: "Alice", email: "alice@example.com" }
   
2. For each field requested:
   
   User.name runs (usually returns parent.name)
   └─ Returns: "Alice"
   
   User.email runs
   └─ Returns: "alice@example.com"
   
   User.posts runs (parent is the User object)
   └─ Returns: [{ id: "101", title: "Post 1" }, ...]
   
3. For each post:
   
   Post.title runs
   └─ Returns: "Post 1"
```

---

## 📝 Complete Example

### Schema

```graphql
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
}

type Query {
  user(id: ID!): User
  users: [User!]!
  post(id: ID!): Post
  posts: [Post!]!
}
```

---

### Mock Data

```javascript
const users = [
  { id: "1", name: "Alice", email: "alice@example.com" },
  { id: "2", name: "Bob", email: "bob@example.com" }
]

const posts = [
  { id: "101", title: "GraphQL Basics", content: "...", authorId: "1" },
  { id: "102", title: "Advanced GraphQL", content: "...", authorId: "1" },
  { id: "103", title: "Node.js Tips", content: "...", authorId: "2" }
]
```

---

### Resolvers

```javascript
const resolvers = {
  // ==========================================
  // ROOT QUERY RESOLVERS
  // ==========================================
  Query: {
    // Get single user
    user: (parent, args) => {
      return users.find(user => user.id === args.id)
    },
    
    // Get all users
    users: () => {
      return users
    },
    
    // Get single post
    post: (parent, args) => {
      return posts.find(post => post.id === args.id)
    },
    
    // Get all posts
    posts: () => {
      return posts
    }
  },
  
  // ==========================================
  // FIELD RESOLVERS
  // ==========================================
  User: {
    // Get posts by this user
    posts: (parent) => {
      // parent is the User object
      return posts.filter(post => post.authorId === parent.id)
    }
  },
  
  Post: {
    // Get author of this post
    author: (parent) => {
      // parent is the Post object
      return users.find(user => user.id === parent.authorId)
    }
  }
}
```

---

## 🧪 Testing Queries

### Query 1: Get User

```graphql
query {
  user(id: "1") {
    id
    name
    email
  }
}
```

**Response:**
```json
{
  "data": {
    "user": {
      "id": "1",
      "name": "Alice",
      "email": "alice@example.com"
    }
  }
}
```

---

### Query 2: Get User with Posts

```graphql
query {
  user(id: "1") {
    name
    posts {
      title
    }
  }
}
```

**Response:**
```json
{
  "data": {
    "user": {
      "name": "Alice",
      "posts": [
        { "title": "GraphQL Basics" },
        { "title": "Advanced GraphQL" }
      ]
    }
  }
}
```

---

### Query 3: Get Post with Author

```graphql
query {
  post(id: "101") {
    title
    author {
      name
      email
    }
  }
}
```

**Response:**
```json
{
  "data": {
    "post": {
      "title": "GraphQL Basics",
      "author": {
        "name": "Alice",
        "email": "alice@example.com"
      }
    }
  }
}
```

---

## 🎨 Query Features

### 1. **Aliases**

```graphql
query {
  alice: user(id: "1") {
    name
  }
  bob: user(id: "2") {
    name
  }
}
```

**Response:**
```json
{
  "data": {
    "alice": { "name": "Alice" },
    "bob": { "name": "Bob" }
  }
}
```

---

### 2. **Fragments**

```graphql
fragment UserDetails on User {
  id
  name
  email
}

query {
  user(id: "1") {
    ...UserDetails
  }
}
```

---

### 3. **Variables**

```graphql
query GetUser($userId: ID!) {
  user(id: $userId) {
    name
    email
  }
}
```

**Variables:**
```json
{
  "userId": "1"
}
```

---

### 4. **Named Queries**

```graphql
query GetUserById {
  user(id: "1") {
    name
  }
}

query GetAllUsers {
  users {
    name
  }
}
```

---

## 🔍 Default Field Resolvers

GraphQL automatically resolves fields that match the data structure:

```javascript
// You DON'T need to write this:
User: {
  id: (parent) => parent.id,
  name: (parent) => parent.name,
  email: (parent) => parent.email
}

// GraphQL does it automatically!
// Only write resolvers for:
// 1. Relationships
// 2. Computed fields
// 3. Custom logic
```

---

## 💡 Resolver Patterns

### Pattern 1: Async Resolvers

```javascript
Query: {
  user: async (parent, args, context) => {
    // Wait for database
    const user = await context.db.query(
      'SELECT * FROM users WHERE id = $1',
      [args.id]
    )
    return user
  }
}
```

---

### Pattern 2: Computed Fields

```javascript
User: {
  // Field not in database
  fullName: (parent) => {
    return `${parent.firstName} ${parent.lastName}`
  },
  
  // Computed from data
  postCount: (parent) => {
    return parent.posts.length
  }
}
```

---

### Pattern 3: Conditional Logic

```javascript
User: {
  email: (parent, args, context) => {
    // Only show email to logged-in user
    if (context.currentUser?.id === parent.id) {
      return parent.email
    }
    return null // Hidden
  }
}
```

---

### Pattern 4: Error Handling

```javascript
Query: {
  user: (parent, args) => {
    const user = users.find(u => u.id === args.id)
    
    if (!user) {
      throw new Error('User not found')
    }
    
    return user
  }
}
```

---

## 🚨 Common Mistakes

### Mistake 1: Not Returning Data

```javascript
// ❌ WRONG
Query: {
  users: () => {
    users // Forgot to return!
  }
}

// ✅ CORRECT
Query: {
  users: () => {
    return users
  }
}
```

---

### Mistake 2: Wrong Resolver Location

```javascript
// ❌ WRONG - author should be in Post, not Query
Query: {
  author: (parent) => {
    return users.find(u => u.id === parent.authorId)
  }
}

// ✅ CORRECT
Post: {
  author: (parent) => {
    return users.find(u => u.id === parent.authorId)
  }
}
```

---

### Mistake 3: Not Using Parent

```javascript
// ❌ WRONG - Where does the user ID come from?
User: {
  posts: () => {
    return posts // All posts?
  }
}

// ✅ CORRECT
User: {
  posts: (parent) => {
    return posts.filter(p => p.authorId === parent.id)
  }
}
```

---

## 🎯 Practical Tips

### Tip 1: Keep Resolvers Thin

```javascript
// ❌ Bad - logic in resolver
Query: {
  user: async (parent, args) => {
    const user = await db.query('SELECT * FROM users WHERE id = $1', [args.id])
    user.formattedName = user.name.toUpperCase()
    user.postCount = await db.query('SELECT COUNT(*) FROM posts WHERE author_id = $1', [user.id])
    return user
  }
}

// ✅ Good - move logic to separate functions
Query: {
  user: (parent, args, context) => {
    return context.userService.findById(args.id)
  }
}
```

---

### Tip 2: Use Context for Shared Data

```javascript
// ✅ Good pattern
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: () => ({
    db,
    auth: authService,
    loaders: createLoaders()
  })
})
```

---

### Tip 3: Return Consistent Shapes

```javascript
// Make sure your resolvers return the shape
// defined in your schema

// Schema says:
type User {
  id: ID!
  name: String!
}

// Resolver must return:
{ id: "1", name: "Alice" }

// NOT:
{ userId: "1", userName: "Alice" }
```

---

## 🔑 Key Takeaways

✅ Queries **read data** from your API  
✅ Resolvers **fetch the actual data** for each field  
✅ Resolvers receive: `parent, args, context, info`  
✅ GraphQL **automatically resolves** simple fields  
✅ Only write resolvers for **relationships** and **custom logic**  
✅ Resolvers can be **async**  
✅ Use **context** for shared data  

---

## 🚀 What's Next?

You know how to read data with queries. Next, let's learn how to **modify data** with mutations!

**Next**: [04-Mutations.md](./04-Mutations.md)

---

**[← Previous: Schema and Types](./02-Schema-And-Types.md)** | **[Back to README](./README.md)** | **[Next: Mutations →](./04-Mutations.md)**
