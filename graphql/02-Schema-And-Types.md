# 02 - Schema & Type System

> **Goal**: Master GraphQL's type system and learn to design effective schemas

---

## 📖 What is a Schema?

A **schema** is your API's **contract**. It defines:

- ✅ What data is available
- ✅ What types that data has
- ✅ What operations clients can perform
- ✅ How types relate to each other

Think of it as a **blueprint** for your entire API.

---

## 🏗️ Schema Example

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
  posts: [Post!]!
}
```

This schema tells you:
- What a `User` and `Post` look like
- What queries are available
- What you'll get back

---

## 🔤 Scalar Types (Built-in)

GraphQL has 5 built-in scalar types:

### 1. **String**
```graphql
type User {
  name: String!     # Text data
  bio: String       # Optional text
}
```

**Examples**: `"Alice"`, `"Hello World"`, `"test@email.com"`

---

### 2. **Int**
```graphql
type Product {
  quantity: Int!    # Whole numbers only
  price: Int        # Optional
}
```

**Examples**: `42`, `-10`, `0`  
**Range**: -2³¹ to 2³¹-1

---

### 3. **Float**
```graphql
type Product {
  rating: Float!    # Decimal numbers
  weight: Float
}
```

**Examples**: `3.14`, `-0.5`, `4.0`

---

### 4. **Boolean**
```graphql
type User {
  isActive: Boolean!
  isVerified: Boolean
}
```

**Examples**: `true`, `false`

---

### 5. **ID**
```graphql
type User {
  id: ID!          # Unique identifier
}
```

**Examples**: `"1"`, `"user-abc-123"`, `"550e8400-e29b-41d4"`

**Note**: ID is serialized as a String but semantically represents a unique identifier.

---

## ⚠️ Non-Null Modifier (!)

The exclamation mark `!` means a field **CANNOT be null**.

```graphql
type User {
  id: ID!           # ✅ MUST be present
  name: String!     # ✅ MUST be present
  age: Int          # ❌ Can be null
}
```

### Examples

```javascript
// ✅ Valid
{
  "id": "1",
  "name": "Alice",
  "age": 30
}

// ✅ Valid (age can be null)
{
  "id": "1",
  "name": "Alice",
  "age": null
}

// ❌ Invalid (name is required)
{
  "id": "1",
  "age": 30
}
```

---

## 📦 Object Types

Object types define the structure of your data.

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  age: Int
  isActive: Boolean!
}
```

**Reading this:**
- `type User` → Defines a new type called "User"
- Each line is a **field** on the User type
- Each field has a **type** (String, Int, Boolean, etc.)
- `!` means non-nullable (required)

---

## 📚 List Types

Lists are defined with square brackets `[]`.

```graphql
type User {
  id: ID!
  name: String!
  posts: [Post!]!
}
```

### Understanding List Syntax

```graphql
# Different list configurations:

posts: [Post]
# → Array of posts, can be null
# → Posts in array can be null
# → Valid: null, [], [post1, null, post2]

posts: [Post]!
# → Array is required (not null)
# → Posts in array can be null
# → Valid: [], [post1, null, post2]
# → Invalid: null

posts: [Post!]
# → Array can be null
# → Posts in array cannot be null
# → Valid: null, [], [post1, post2]
# → Invalid: [post1, null, post2]

posts: [Post!]!
# → Array is required
# → Posts in array are required
# → Valid: [], [post1, post2]
# → Invalid: null, [post1, null, post2]
```

**Most common**: `[Post!]!` (required array of required posts)

---

## 🔗 Relationships Between Types

```graphql
type User {
  id: ID!
  name: String!
  posts: [Post!]!    # One user has many posts
}

type Post {
  id: ID!
  title: String!
  author: User!       # One post has one author
}
```

### Visualization

```
User
├── id: "1"
├── name: "Alice"
└── posts: [
      { id: "101", title: "Post 1" },
      { id: "102", title: "Post 2" }
    ]

Post
├── id: "101"
├── title: "Post 1"
└── author: { id: "1", name: "Alice" }
```

---

## 🎯 Root Types

GraphQL has 3 special "root" types:

### 1. Query (Required)
Defines **read operations**

```graphql
type Query {
  user(id: ID!): User
  users: [User!]!
  post(id: ID!): Post
}
```

---

### 2. Mutation (Optional)
Defines **write operations**

```graphql
type Mutation {
  createUser(name: String!, email: String!): User!
  updateUser(id: ID!, name: String): User
  deleteUser(id: ID!): Boolean!
}
```

---

### 3. Subscription (Optional)
Defines **real-time operations**

```graphql
type Subscription {
  userCreated: User!
  postUpdated(postId: ID!): Post!
}
```

---

## 📝 Input Types

Input types are used for complex input arguments.

### Without Input Type (❌ Not great)

```graphql
type Mutation {
  createUser(
    name: String!
    email: String!
    age: Int
    bio: String
    website: String
  ): User!
}
```

Problems:
- Hard to read
- Hard to maintain
- Can't reuse

---

### With Input Type (✅ Better)

```graphql
input CreateUserInput {
  name: String!
  email: String!
  age: Int
  bio: String
  website: String
}

type Mutation {
  createUser(input: CreateUserInput!): User!
}
```

Benefits:
- ✅ Easier to read
- ✅ Reusable
- ✅ Better for validation
- ✅ Can extend easily

---

## 🎭 Enums

Enums define a set of allowed values.

```graphql
enum UserRole {
  ADMIN
  MODERATOR
  USER
  GUEST
}

type User {
  id: ID!
  name: String!
  role: UserRole!
}
```

### Usage in Query

```graphql
mutation {
  createUser(name: "Alice", role: ADMIN) {
    id
    role
  }
}
```

**Benefits:**
- Type safety (can't pass invalid values)
- Auto-completion in IDEs
- Self-documenting

---

## 📋 Complete Schema Example

```graphql
# Scalar types are built-in
# Object types we define

# Enums
enum PostStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

enum UserRole {
  ADMIN
  USER
}

# Input types for mutations
input CreatePostInput {
  title: String!
  content: String!
  status: PostStatus!
}

input UpdatePostInput {
  title: String
  content: String
  status: PostStatus
}

# Object types
type User {
  id: ID!
  name: String!
  email: String!
  role: UserRole!
  posts: [Post!]!
  createdAt: String!
}

type Post {
  id: ID!
  title: String!
  content: String!
  status: PostStatus!
  author: User!
  comments: [Comment!]!
  createdAt: String!
}

type Comment {
  id: ID!
  text: String!
  author: User!
  post: Post!
  createdAt: String!
}

# Root Query type (READ operations)
type Query {
  # Single items
  user(id: ID!): User
  post(id: ID!): Post
  
  # Lists
  users: [User!]!
  posts(status: PostStatus): [Post!]!
  
  # Search
  searchPosts(query: String!): [Post!]!
}

# Root Mutation type (WRITE operations)
type Mutation {
  # User mutations
  createUser(name: String!, email: String!): User!
  updateUser(id: ID!, name: String): User
  deleteUser(id: ID!): Boolean!
  
  # Post mutations
  createPost(input: CreatePostInput!): Post!
  updatePost(id: ID!, input: UpdatePostInput!): Post
  deletePost(id: ID!): Boolean!
  
  # Comment mutations
  createComment(postId: ID!, text: String!): Comment!
}
```

---

## 🧠 Schema Design Principles

### 1. **Design for Clients, Not Database**

❌ **Bad** (exposes database structure):
```graphql
type Post {
  id: ID!
  title: String!
  user_id: ID!         # Database field name
  created_at: String   # Database field name
}
```

✅ **Good** (client-friendly):
```graphql
type Post {
  id: ID!
  title: String!
  author: User!        # Relationship, not ID
  createdAt: String    # camelCase
}
```

---

### 2. **Use Descriptive Names**

❌ **Bad**:
```graphql
type Query {
  get(id: ID!): Post
  list: [Post!]!
}
```

✅ **Good**:
```graphql
type Query {
  post(id: ID!): Post
  posts: [Post!]!
}
```

---

### 3. **Think Nullable vs Non-Nullable**

```graphql
type User {
  id: ID!              # Always required
  name: String!        # Always required
  email: String!       # Always required
  age: Int            # Optional (might not know)
  bio: String         # Optional (might be empty)
  website: String     # Optional (not everyone has)
}
```

**Rule of thumb:**
- IDs → Always `!`
- Required user input → Use `!`
- Optional data → No `!`

---

### 4. **Group Related Fields**

✅ **Good**:
```graphql
type User {
  # Identity
  id: ID!
  username: String!
  email: String!
  
  # Profile
  name: String!
  bio: String
  avatar: String
  
  # Settings
  isActive: Boolean!
  role: UserRole!
  
  # Relationships
  posts: [Post!]!
  followers: [User!]!
  
  # Timestamps
  createdAt: String!
  updatedAt: String!
}
```

---

## 🔍 Introspection

GraphQL schemas are **self-documenting** through introspection.

### Query the Schema Itself

```graphql
query {
  __schema {
    types {
      name
      description
    }
  }
}
```

### Query Type Information

```graphql
query {
  __type(name: "User") {
    name
    fields {
      name
      type {
        name
      }
    }
  }
}
```

This powers tools like GraphQL Playground!

---

## 💡 Common Patterns

### Pattern 1: Timestamps

```graphql
type Post {
  id: ID!
  title: String!
  createdAt: String!
  updatedAt: String!
}
```

---

### Pattern 2: Pagination Info

```graphql
type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

---

### Pattern 3: Error Handling

```graphql
type UserResult {
  success: Boolean!
  message: String
  user: User
}

type Mutation {
  createUser(input: CreateUserInput!): UserResult!
}
```

---

## ✅ Best Practices Summary

1. ✅ Use descriptive names (not DB names)
2. ✅ Design for client needs
3. ✅ Use Input types for complex arguments
4. ✅ Use Enums for fixed sets of values
5. ✅ Think carefully about nullable vs non-nullable
6. ✅ Group related fields together
7. ✅ Use consistent naming (camelCase)
8. ✅ Document with descriptions

---

## 🎯 Exercise

Try designing a schema for a simple **Book Store**:

Requirements:
- Books have title, author, price, ISBN
- Authors have name and biography
- Customers can create orders
- Orders contain multiple books

**Solution in next chapter!**

---

## 🔑 Key Takeaways

✅ Schema is your API contract  
✅ 5 scalar types: String, Int, Float, Boolean, ID  
✅ `!` means non-nullable (required)  
✅ `[]` defines lists/arrays  
✅ Object types define data structure  
✅ Input types for mutation arguments  
✅ Enums for fixed values  
✅ Design for clients, not database  

---

## 🚀 What's Next?

Now you understand types and schemas. Next, let's learn how to **read data** using queries!

**Next**: [03-Queries-And-Resolvers.md](./03-Queries-And-Resolvers.md)

---

**[← Previous: Introduction](./01-Introduction.md)** | **[Back to README](./README.md)** | **[Next: Queries and Resolvers →](./03-Queries-And-Resolvers.md)**
