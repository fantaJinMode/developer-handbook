# Project 2: E-Commerce - Implementation

## Database Connection (db.js)

```javascript
import pkg from 'pg'
const { Pool } = pkg

const pool = new Pool({
  connectionString: process.env.DATABASE_URL || 'postgresql://localhost/ecommerce_graphql'
})

export default {
  query: async (text, params) => {
    const result = await pool.query(text, params)
    return result.rows
  }
}
```

## DataLoader Setup (loaders.js)

```javascript
import DataLoader from 'dataloader'
import db from './db.js'

async function batchLoadProducts(productIds) {
  const products = await db.query(
    'SELECT * FROM products WHERE id = ANY($1)',
    [productIds]
  )
  return productIds.map(id => products.find(p => p.id === id) || null)
}

async function batchLoadCategories(categoryIds) {
  const categories = await db.query(
    'SELECT * FROM categories WHERE id = ANY($1)',
    [categoryIds]
  )
  return categoryIds.map(id => categories.find(c => c.id === id) || null)
}

export function createLoaders() {
  return {
    product: new DataLoader(batchLoadProducts),
    category: new DataLoader(batchLoadCategories)
  }
}
```

## Schema (schema.js)

```javascript
export const typeDefs = `#graphql
  type Category {
    id: ID!
    name: String!
    description: String
    products: [Product!]!
  }

  type Product {
    id: ID!
    name: String!
    description: String
    price: Float!
    category: Category
    stock: Int!
    reviews: [Review!]!
    averageRating: Float
  }

  type Review {
    id: ID!
    product: Product!
    authorName: String!
    rating: Int!
    comment: String
  }

  type ProductEdge {
    node: Product!
    cursor: String!
  }

  type PageInfo {
    hasNextPage: Boolean!
    endCursor: String
  }

  type ProductConnection {
    edges: [ProductEdge!]!
    pageInfo: PageInfo!
    totalCount: Int!
  }

  input CreateProductInput {
    name: String!
    description: String
    price: Float!
    categoryId: ID!
    stock: Int!
  }

  type Query {
    product(id: ID!): Product
    products(first: Int = 10, after: String): ProductConnection!
    category(id: ID!): Category
    categories: [Category!]!
  }

  type Mutation {
    createProduct(input: CreateProductInput!): Product!
  }
`
```

## Resolvers (resolvers.js)

```javascript
import db from './db.js'

const encodeCursor = (id) => Buffer.from(id.toString()).toString('base64')
const decodeCursor = (cursor) => parseInt(Buffer.from(cursor, 'base64').toString('ascii'))

export const resolvers = {
  Query: {
    product: (_, { id }, { loaders }) => loaders.product.load(parseInt(id)),
    
    products: async (_, { first, after }) => {
      const afterId = after ? decodeCursor(after) : 0
      const products = await db.query(
        'SELECT * FROM products WHERE id > $1 ORDER BY id LIMIT $2',
        [afterId, first + 1]
      )

      const hasNextPage = products.length > first
      const edges = products.slice(0, first).map(product => ({
        node: product,
        cursor: encodeCursor(product.id)
      }))

      const [{ count }] = await db.query('SELECT COUNT(*) FROM products')

      return {
        edges,
        pageInfo: {
          hasNextPage,
          endCursor: edges[edges.length - 1]?.cursor || null
        },
        totalCount: parseInt(count)
      }
    },

    category: (_, { id }, { loaders }) => loaders.category.load(parseInt(id)),
    
    categories: () => db.query('SELECT * FROM categories')
  },

  Mutation: {
    createProduct: async (_, { input }) => {
      if (input.price <= 0) {
        throw new Error('Price must be greater than 0')
      }

      const [product] = await db.query(
        `INSERT INTO products (name, description, price, category_id, stock)
         VALUES ($1, $2, $3, $4, $5) RETURNING *`,
        [input.name, input.description, input.price, input.categoryId, input.stock]
      )

      return product
    }
  },

  Product: {
    category: (parent, _, { loaders }) => {
      return parent.category_id ? loaders.category.load(parent.category_id) : null
    },

    reviews: async (parent) => {
      return db.query('SELECT * FROM reviews WHERE product_id = $1', [parent.id])
    },

    averageRating: async (parent) => {
      const [result] = await db.query(
        'SELECT AVG(rating) as avg FROM reviews WHERE product_id = $1',
        [parent.id]
      )
      return result.avg ? parseFloat(result.avg).toFixed(1) : null
    }
  },

  Review: {
    product: (parent, _, { loaders }) => loaders.product.load(parent.product_id)
  },

  Category: {
    products: async (parent) => {
      return db.query('SELECT * FROM products WHERE category_id = $1', [parent.id])
    }
  }
}
```

## Server (server.js)

```javascript
import { ApolloServer } from '@apollo/server'
import { startStandaloneServer } from '@apollo/server/standalone'
import { typeDefs } from './schema.js'
import { resolvers } from './resolvers.js'
import { createLoaders } from './loaders.js'

const server = new ApolloServer({
  typeDefs,
  resolvers
})

const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
  context: async () => ({
    loaders: createLoaders()
  })
})

console.log(`🚀 Server ready at ${url}`)
```

**Next**: [Project-2-Testing.md](./Project-2-Testing.md)

**[← Previous](./Project-2-Setup.md)** | **[README](./README.md)** | **[Next →](./Project-2-Testing.md)**
