# Project 1: Blog API - Testing

## 🧪 Test Queries

### Test 1: Get All Users

```graphql
query GetUsers {
  users {
    id
    name
    email
    posts {
      title
    }
  }
}
```

### Test 2: Get Single Post with Author and Comments

```graphql
query GetPost {
  post(id: "1") {
    title
    content
    author {
      name
      email
    }
    comments {
      text
      author {
        name
      }
    }
  }
}
```

### Test 3: Create New Post

```graphql
mutation CreatePost {
  createPost(input: {
    title: "My First Post"
    content: "This is exciting!"
    authorId: "1"
  }) {
    id
    title
    author {
      name
    }
  }
}
```

### Test 4: Add Comment

```graphql
mutation AddComment {
  createComment(
    postId: "1"
    text: "Great article!"
    authorId: "2"
  ) {
    id
    text
    author {
      name
    }
    post {
      title
    }
  }
}
```

## ✅ Success Criteria

- [ ] All queries return correct data
- [ ] Mutations create new records
- [ ] Relationships work correctly
- [ ] Nested queries work

## 🎉 Congratulations!

You've completed Project 1!

**Next**: [06-N-Plus-One-Problem.md](./06-N-Plus-One-Problem.md)

**[← Previous](./Project-1-Implementation.md)** | **[README](./README.md)** | **[Next →](./06-N-Plus-One-Problem.md)**
