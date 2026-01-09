# 17 - Performance Tips

> **Goal**: Optimize GraphQL API performance

## 🚀 Optimization Strategies

### 1. DataLoader (Most Important)
Batch and cache database queries.

### 2. Query Complexity Limits
```javascript
import { createComplexityLimitRule } from 'graphql-validation-complexity'

const server = new ApolloServer({
  validationRules: [createComplexityLimitRule(1000)]
})
```

### 3. Query Depth Limits
```javascript
import depthLimit from 'graphql-depth-limit'

const server = new ApolloServer({
  validationRules: [depthLimit(5)]
})
```

### 4. Pagination
Always paginate large lists.

### 5. Caching
```javascript
// Cache at resolver level
const cache = new Map()

Query: {
  popularPosts: async () => {
    if (cache.has('popular')) {
      return cache.get('popular')
    }
    const posts = await db.getPopularPosts()
    cache.set('popular', posts)
    return posts
  }
}
```

### 6. Database Indexes
```sql
CREATE INDEX idx_posts_author ON posts(author_id);
CREATE INDEX idx_likes_post ON likes(post_id);
```

### 7. SELECT Only Needed Fields
```javascript
// ❌ Selects all columns
db.query('SELECT * FROM users')

// ✅ Only needed fields
db.query('SELECT id, name FROM users')
```

## 🔑 Key Takeaways

✅ DataLoader is essential  
✅ Limit query complexity  
✅ Always paginate  
✅ Add database indexes  
✅ Cache expensive operations  

**[← Previous](./16-Common-Mistakes.md)** | **[README](./README.md)** | **[Next →](./18-Production-Checklist.md)**
