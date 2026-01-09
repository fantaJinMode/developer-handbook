# Project 2: E-Commerce Catalog - Setup

> **Goal**: Build a product catalog with DataLoader optimization

## 🎯 What You'll Build

- Products with categories
- Reviews and ratings
- Optimized with DataLoader
- Cursor-based pagination
- Search functionality

## 📦 Setup

### Install Dependencies

```bash
npm install @apollo/server graphql pg dataloader
```

### Database Setup (PostgreSQL)

```sql
CREATE DATABASE ecommerce_graphql;

CREATE TABLE categories (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  description TEXT
);

CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  description TEXT,
  price DECIMAL(10, 2) NOT NULL,
  category_id INTEGER REFERENCES categories(id),
  stock INTEGER DEFAULT 0
);

CREATE TABLE reviews (
  id SERIAL PRIMARY KEY,
  product_id INTEGER REFERENCES products(id),
  author_name VARCHAR(100) NOT NULL,
  rating INTEGER CHECK (rating >= 1 AND rating <= 5),
  comment TEXT
);

-- Insert sample data
INSERT INTO categories (name, description) VALUES
  ('Electronics', 'Electronic devices'),
  ('Books', 'Physical and digital books');

INSERT INTO products (name, description, price, category_id, stock) VALUES
  ('Laptop Pro', 'High-performance laptop', 1299.99, 1, 50),
  ('GraphQL Book', 'Learn GraphQL', 39.99, 2, 100);
```

## 🎓 Concepts You'll Practice

- Database integration
- DataLoader for N+1 optimization
- Cursor-based pagination
- Interfaces
- Error handling

**Next**: [Project-2-Implementation.md](./Project-2-Implementation.md)

**[← Previous](./10-Pagination.md)** | **[README](./README.md)** | **[Next →](./Project-2-Implementation.md)**
