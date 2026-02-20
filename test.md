# 🧪 Code Syntax Highlight Test Page

A comprehensive markdown test page for GitHub Pages theme development. Tests various languages, callouts, tables, inline code, and more.

---

## 📋 Table of Contents

- [Bash / Shell](#bash--shell)
- [Python](#python)
- [JavaScript / TypeScript](#javascript--typescript)
- [Rust](#rust)
- [Go](#go)
- [Java](#java)
- [C / C++](#c--c)
- [SQL](#sql)
- [YAML / JSON / TOML](#yaml--json--toml)
- [HTML / CSS](#html--css)
- [Ruby](#ruby)
- [PHP](#php)
- [Kotlin](#kotlin)
- [Swift](#swift)
- [Dockerfile](#dockerfile)
- [Callouts / Alerts](#callouts--alerts)
- [Inline Code](#inline-code)
- [Tables](#tables)
- [Nested Lists with Code](#nested-lists-with-code)
- [Diff](#diff)

---

## Bash / Shell

```bash
#!/bin/bash

# Setup virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Environment variables
export API_KEY="your_api_key_here"
export DEBUG=true

# Loop example
for file in ./src/*.py; do
  echo "Processing: $file"
  python "$file"
done

# Function
greet() {
  local name=$1
  echo "Hello, ${name}!"
}

greet "World"
```

---

## Python

### Basic Script

```python
import os
import json
from pathlib import Path
from typing import Optional

# Constants
BASE_DIR = Path(__file__).parent
CONFIG_PATH = BASE_DIR / "config.json"

def load_config(path: Path) -> dict:
    """Load configuration from a JSON file."""
    with open(path, "r", encoding="utf-8") as f:
        return json.load(f)

class DataProcessor:
    def __init__(self, config: dict):
        self.config = config
        self.results: list[dict] = []

    def process(self, data: list[str]) -> list[dict]:
        for item in data:
            result = {"input": item, "length": len(item)}
            self.results.append(result)
        return self.results

if __name__ == "__main__":
    config = load_config(CONFIG_PATH)
    processor = DataProcessor(config)
    output = processor.process(["hello", "world", "python"])
    print(json.dumps(output, indent=2))
```

### Async / Await

```python
import asyncio
import aiohttp

async def fetch(session: aiohttp.ClientSession, url: str) -> dict:
    async with session.get(url) as response:
        return await response.json()

async def main():
    urls = [
        "https://api.example.com/users/1",
        "https://api.example.com/users/2",
    ]
    async with aiohttp.ClientSession() as session:
        tasks = [fetch(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
        for r in results:
            print(r)

asyncio.run(main())
```

### Decorators & Generators

```python
import functools
import time

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        end = time.perf_counter()
        print(f"{func.__name__} took {end - start:.4f}s")
        return result
    return wrapper

@timer
def fibonacci(n: int):
    def _gen(n):
        a, b = 0, 1
        for _ in range(n):
            yield a
            a, b = b, a + b
    return list(_gen(n))

print(fibonacci(30))
```

---

## JavaScript / TypeScript

### Modern JavaScript

```javascript
// Fetch with async/await and error handling
const BASE_URL = "https://api.example.com";

async function fetchUser(id) {
  try {
    const response = await fetch(`${BASE_URL}/users/${id}`);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Failed to fetch user:", error);
    return null;
  }
}

// Arrow functions, destructuring, spread
const users = [
  { id: 1, name: "Alice", role: "admin" },
  { id: 2, name: "Bob",   role: "user"  },
];

const admins = users
  .filter(({ role }) => role === "admin")
  .map(({ id, name }) => ({ id, name, isAdmin: true }));

console.log(admins);
```

### TypeScript

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  createdAt: Date;
}

type ApiResponse<T> = {
  data: T;
  status: number;
  message: string;
};

async function getUser(id: number): Promise<ApiResponse<User>> {
  const response = await fetch(`/api/users/${id}`);
  const json = await response.json();
  return {
    data: json as User,
    status: response.status,
    message: "ok",
  };
}

// Generics
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const num = first([1, 2, 3]);       // type: number | undefined
const str = first(["a", "b", "c"]); // type: string | undefined
```

### React Component

```tsx
import React, { useState, useEffect } from "react";

interface Props {
  userId: number;
}

const UserCard: React.FC<Props> = ({ userId }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    getUser(userId).then(({ data }) => {
      setUser(data);
      setLoading(false);
    });
  }, [userId]);

  if (loading) return <div className="skeleton" />;
  if (!user)   return <p>User not found.</p>;

  return (
    <div className="card">
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
};

export default UserCard;
```

---

## Rust

```rust
use std::collections::HashMap;

#[derive(Debug)]
struct Config {
    host: String,
    port: u16,
    max_connections: usize,
}

impl Default for Config {
    fn default() -> Self {
        Config {
            host: String::from("127.0.0.1"),
            port: 8080,
            max_connections: 100,
        }
    }
}

fn word_count(text: &str) -> HashMap<&str, usize> {
    let mut map = HashMap::new();
    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }
    map
}

fn main() {
    let config = Config::default();
    println!("{:?}", config);

    let counts = word_count("the quick brown fox jumps over the lazy dog the");
    let mut pairs: Vec<_> = counts.iter().collect();
    pairs.sort_by(|a, b| b.1.cmp(a.1));

    for (word, count) in pairs {
        println!("{}: {}", word, count);
    }
}
```

---

## Go

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
)

type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email"`
}

func getUsers(w http.ResponseWriter, r *http.Request) {
    users := []User{
        {1, "Alice", "alice@example.com"},
        {2, "Bob",   "bob@example.com"},
    }

    w.Header().Set("Content-Type", "application/json")
    if err := json.NewEncoder(w).Encode(users); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/users", getUsers)

    fmt.Println("Server running on :8080")
    log.Fatal(http.ListenAndServe(":8080", mux))
}
```

---

## Java

```java
import java.util.List;
import java.util.stream.Collectors;

public class StreamExample {

    record User(int id, String name, String role) {}

    public static void main(String[] args) {
        List<User> users = List.of(
            new User(1, "Alice", "ADMIN"),
            new User(2, "Bob",   "USER"),
            new User(3, "Carol", "ADMIN")
        );

        // Filter + map with streams
        List<String> adminNames = users.stream()
            .filter(u -> u.role().equals("ADMIN"))
            .map(User::name)
            .sorted()
            .collect(Collectors.toList());

        System.out.println("Admins: " + adminNames);

        // Optional example
        users.stream()
            .filter(u -> u.id() == 2)
            .findFirst()
            .ifPresentOrElse(
                u -> System.out.println("Found: " + u.name()),
                ()  -> System.out.println("Not found")
            );
    }
}
```

---

## C / C++

### C

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    int   id;
    char  name[64];
    float score;
} Student;

int compare_score(const void *a, const void *b) {
    const Student *sa = (const Student *)a;
    const Student *sb = (const Student *)b;
    return (sb->score > sa->score) - (sb->score < sa->score);
}

int main(void) {
    Student students[] = {
        {1, "Alice", 92.5f},
        {2, "Bob",   78.0f},
        {3, "Carol", 88.3f},
    };
    int n = sizeof(students) / sizeof(students[0]);

    qsort(students, n, sizeof(Student), compare_score);

    for (int i = 0; i < n; i++) {
        printf("%d. %s - %.1f\n", i + 1, students[i].name, students[i].score);
    }
    return 0;
}
```

### C++

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <ranges>

int main() {
    std::vector<int> nums = {5, 3, 8, 1, 9, 2, 7, 4, 6};

    // C++20 ranges
    auto even_squares = nums
        | std::views::filter([](int n) { return n % 2 == 0; })
        | std::views::transform([](int n) { return n * n; });

    for (int v : even_squares) {
        std::cout << v << " ";
    }
    std::cout << "\n";

    std::sort(nums.begin(), nums.end());
    std::cout << "Sorted: ";
    for (int n : nums) std::cout << n << " ";
    std::cout << "\n";

    return 0;
}
```

---

## SQL

```sql
-- Create tables
CREATE TABLE users (
    id         SERIAL PRIMARY KEY,
    name       VARCHAR(100) NOT NULL,
    email      VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE orders (
    id         SERIAL PRIMARY KEY,
    user_id    INT REFERENCES users(id) ON DELETE CASCADE,
    total      NUMERIC(10, 2) NOT NULL,
    status     VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Insert sample data
INSERT INTO users (name, email) VALUES
    ('Alice', 'alice@example.com'),
    ('Bob',   'bob@example.com');

-- Complex query with CTE
WITH ranked_orders AS (
    SELECT
        u.name,
        o.total,
        o.status,
        ROW_NUMBER() OVER (PARTITION BY u.id ORDER BY o.total DESC) AS rn
    FROM users u
    JOIN orders o ON o.user_id = u.id
    WHERE o.status = 'completed'
)
SELECT name, total
FROM ranked_orders
WHERE rn = 1
ORDER BY total DESC;
```

---

## YAML / JSON / TOML

### YAML

```yaml
# GitHub Actions workflow
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements-dev.txt

      - name: Run tests
        run: pytest --cov=src --cov-report=xml
```

### JSON

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "test": "vitest run"
  },
  "dependencies": {
    "react": "^18.3.0",
    "react-dom": "^18.3.0"
  },
  "devDependencies": {
    "typescript": "^5.4.0",
    "vite": "^5.2.0",
    "vitest": "^1.5.0"
  }
}
```

### TOML

```toml
[package]
name    = "my-rust-app"
version = "0.1.0"
edition = "2021"

[dependencies]
serde       = { version = "1.0", features = ["derive"] }
tokio       = { version = "1",   features = ["full"] }
reqwest     = { version = "0.12", features = ["json"] }

[profile.release]
opt-level = 3
lto       = true
```

---

## HTML / CSS

### HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>My App</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <header class="site-header">
    <nav>
      <a href="/" class="logo">MyApp</a>
      <ul>
        <li><a href="/about">About</a></li>
        <li><a href="/blog">Blog</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <section class="hero">
      <h1>Welcome to <em>MyApp</em></h1>
      <p>Build something amazing.</p>
      <button type="button" class="btn btn-primary">Get Started</button>
    </section>
  </main>
</body>
</html>
```

### CSS

```css
/* Custom properties + responsive layout */
:root {
  --color-primary: #6366f1;
  --color-bg: #0f172a;
  --color-text: #e2e8f0;
  --radius: 0.5rem;
  --font-sans: "Inter", system-ui, sans-serif;
}

*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: var(--font-sans);
  background-color: var(--color-bg);
  color: var(--color-text);
  line-height: 1.6;
}

.btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.6rem 1.2rem;
  border-radius: var(--radius);
  font-weight: 600;
  cursor: pointer;
  transition: background-color 200ms ease;
}

.btn-primary {
  background-color: var(--color-primary);
  color: #fff;
  border: none;
}

.btn-primary:hover {
  background-color: color-mix(in srgb, var(--color-primary) 80%, black);
}

@media (max-width: 768px) {
  .hero h1 { font-size: 1.8rem; }
}
```

---

## Ruby

```ruby
# frozen_string_literal: true

require "json"
require "net/http"

module ApiClient
  BASE_URI = URI("https://api.example.com")

  def self.get(path, params: {})
    uri = BASE_URI.dup
    uri.path  = path
    uri.query = URI.encode_www_form(params) unless params.empty?

    response = Net::HTTP.get_response(uri)
    JSON.parse(response.body, symbolize_names: true)
  rescue StandardError => e
    { error: e.message }
  end
end

# Using Struct
User = Struct.new(:id, :name, :email, keyword_init: true) do
  def to_s
    "#{name} <#{email}>"
  end
end

users = ApiClient.get("/users").map { |u| User.new(**u) }
users.each { |u| puts u }
```

---

## PHP

```php
<?php

declare(strict_types=1);

namespace App\Controllers;

use App\Models\User;
use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;

class UserController
{
    public function __construct(
        private readonly UserRepository $repo
    ) {}

    public function index(Request $request, Response $response): Response
    {
        $users = $this->repo->findAll();

        $payload = json_encode(
            array_map(fn(User $u) => [
                'id'    => $u->id,
                'name'  => $u->name,
                'email' => $u->email,
            ], $users),
            JSON_PRETTY_PRINT
        );

        $response->getBody()->write($payload);
        return $response->withHeader('Content-Type', 'application/json');
    }
}
```

---

## Kotlin

```kotlin
import kotlinx.coroutines.*
import kotlinx.serialization.*
import kotlinx.serialization.json.*

@Serializable
data class User(val id: Int, val name: String, val email: String)

suspend fun fetchUser(id: Int): User = withContext(Dispatchers.IO) {
    // Simulated network call
    delay(100)
    User(id, "User $id", "user$id@example.com")
}

fun main() = runBlocking {
    val users = (1..5).map { id ->
        async { fetchUser(id) }
    }.awaitAll()

    val json = Json { prettyPrint = true }
    println(json.encodeToString(users))

    // Extension functions
    fun List<User>.byId(id: Int) = find { it.id == id }
    users.byId(3)?.let { println("Found: ${it.name}") }
}
```

---

## Swift

```swift
import Foundation

// Codable model
struct User: Codable, Identifiable {
    let id: Int
    let name: String
    let email: String
}

// Generic async fetch
func fetch<T: Codable>(_ type: T.Type, from url: URL) async throws -> T {
    let (data, response) = try await URLSession.shared.data(from: url)
    guard let http = response as? HTTPURLResponse, http.statusCode == 200 else {
        throw URLError(.badServerResponse)
    }
    return try JSONDecoder().decode(T.self, from: data)
}

// Actor for thread-safe state
actor UserStore {
    private var cache: [Int: User] = [:]

    func user(for id: Int) -> User? { cache[id] }

    func store(_ user: User) { cache[user.id] = user }
}

// SwiftUI-style usage
Task {
    let url = URL(string: "https://api.example.com/users/1")!
    let user = try await fetch(User.self, from: url)
    print("Hello, \(user.name)!")
}
```

---

## Dockerfile

```dockerfile
# syntax=docker/dockerfile:1

# ---- Build stage ----
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --frozen-lockfile

COPY . .
RUN npm run build

# ---- Production stage ----
FROM node:20-alpine AS runner

ENV NODE_ENV=production
WORKDIR /app

RUN addgroup --system --gid 1001 nodejs \
 && adduser  --system --uid 1001 nextjs

COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/public           ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/static     ./.next/static

USER nextjs
EXPOSE 3000

CMD ["node", "server.js"]
```

---

## Callouts / Alerts

> [!NOTE]
> This is a **note** callout. Use it for general supplementary information.

> [!TIP]
> This is a **tip** callout. Great for best-practice suggestions.

> [!IMPORTANT]
> This is an **important** callout. Use when something must not be missed.

> [!WARNING]
> This is a **warning** callout. Use before potentially breaking actions.

> [!CAUTION]
> This is a **caution** callout. Signals danger or destructive operations.

---

## Inline Code

Use `pip install -r requirements.txt` to install dependencies.  
Set the environment variable with `export API_KEY="your_key"`.  
Call the endpoint at `GET /api/v1/users/:id`.  
The config file lives at `~/.config/myapp/config.toml`.  
Run the test suite via `pytest -v --tb=short`.

---

## Tables

| Language   | Paradigm          | Typing       | Use Case                  |
|------------|-------------------|--------------|---------------------------|
| Python     | Multi-paradigm    | Dynamic      | Data, scripting, backend  |
| Rust       | Systems           | Static       | Performance, safety       |
| TypeScript | OOP / Functional  | Static       | Frontend, fullstack       |
| Go         | Concurrent        | Static       | Backend, CLIs, cloud      |
| Haskell    | Purely functional | Static       | Academia, finance         |
| Ruby       | OOP / Scripting   | Dynamic      | Web apps, tooling         |

---

## Nested Lists with Code

1. **Set up the project**
   ```bash
   mkdir my-project && cd my-project
   git init
   ```

2. **Install dependencies**

   > [!TIP]
   > Always pin your dependency versions in production.

   ```bash
   npm install express zod dotenv
   npm install -D typescript @types/node ts-node
   ```

3. **Configure TypeScript**
   ```json
   {
     "compilerOptions": {
       "target": "ES2022",
       "module": "NodeNext",
       "strict": true,
       "outDir": "dist"
     }
   }
   ```

4. **Write your entry point** and save it as `src/index.ts`:
   ```typescript
   import express from "express";
   const app = express();

   app.get("/health", (_req, res) => {
     res.json({ status: "ok", timestamp: new Date() });
   });

   app.listen(3000, () => console.log("🚀 Listening on port 3000"));
   ```

5. **Run in development**
   ```bash
   npx ts-node src/index.ts
   ```

---

## Diff

```diff
- const apiUrl = "http://localhost:3000";
+ const apiUrl = process.env.API_URL ?? "http://localhost:3000";

  function fetchData(endpoint: string) {
-   return fetch(apiUrl + endpoint);
+   return fetch(`${apiUrl}${endpoint}`, {
+     headers: { Authorization: `Bearer ${process.env.TOKEN}` },
+   });
  }
```

---

> _This page is intended solely for theme and syntax highlighting testing on GitHub Pages._
