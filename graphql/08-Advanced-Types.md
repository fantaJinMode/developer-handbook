# 08 - Advanced Types

> **Goal**: Master Interfaces and Unions

## 📖 Interfaces

Common fields across types:

```graphql
interface SearchResult {
  id: ID!
  title: String!
}

type Post implements SearchResult {
  id: ID!
  title: String!
  content: String!
}

type Video implements SearchResult {
  id: ID!
  title: String!
  duration: Int!
}
```

## 📖 Unions

One of several types:

```graphql
union FeedItem = Post | Ad | Video

type Query {
  feed: [FeedItem!]!
}
```

## 🔧 Querying

```graphql
query {
  feed {
    __typename
    ... on Post {
      title
    }
    ... on Video {
      duration
    }
  }
}
```

## 🔑 Key Takeaways

✅ Interfaces = shared fields  
✅ Unions = one of several types  
✅ Use `__typename` to identify type  

**[← Previous](./07-DataLoader.md)** | **[README](./README.md)** | **[Next →](./09-Error-Handling.md)**
