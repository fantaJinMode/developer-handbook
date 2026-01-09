# Project 1: Blog API - Setup

> **Goal**: Build a GraphQL API for a blog with users, posts, and comments

## 🎯 What You'll Build

- Users with authentication
- Blog posts with authors
- Comments on posts
- Relationships between all types

## 📦 Setup

### Step 1: Initialize Project

```bash
mkdir blog-graphql-api
cd blog-graphql-api
npm init -y
```

### Step 2: Install Dependencies

```bash
npm install @apollo/server graphql
```

### Step 3: Create Files

```bash
touch server.js schema.js resolvers.js data.js
```

## 📁 Project Structure

```
blog-graphql-api/
├── server.js      # Apollo Server setup
├── schema.js      # Type definitions
├── resolvers.js   # Resolver functions
└── data.js        # Mock data
```

## 🎓 Concepts You'll Practice

- Schema design
- Queries and mutations
- Resolvers
- Relationships (one-to-many)
- Field resolvers

## 🚀 Ready?

**Next**: [Project-1-Implementation.md](./Project-1-Implementation.md)

**[← Previous](./05-Relationships.md)** | **[README](./README.md)** | **[Next →](./Project-1-Implementation.md)**
