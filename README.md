# Drift

A lightweight, high-level HTTP server library for ZEN.

**Version:** 1.0.0  
**Author:** Jishith M P

## Features

- HTTP routing
- GET, POST, PUT and DELETE routes
- Path parameters
- Query parameters
- Static file serving
- Basic MIME detection
- Automatic 404 handling

## Installation

Install Drift using the ZEN package manager:

```text
zen install drift
```

## Import

```zen
import (App, Request, listen) from "drift"
```

## API

### `listen(port, routes)`

Starts the HTTP server.

```zen
listen(8080, routes)
```

- `port` — server port
- `routes` — function reference receiving an `App`

### `App`

#### `app.get(pattern, handler)`
Registers a GET route.

#### `app.post(pattern, handler)`
Registers a POST route.

#### `app.put(pattern, handler)`
Registers a PUT route.

#### `app.del(pattern, handler)`
Registers a DELETE route.

#### `app.static(prefix, dir)`
Serves static files from a directory.

### `Request`

Every route handler receives a `Request`.

Properties:

- `req.raw` — underlying `HttpRequest`
- `req.path` — normalized request path
- `req.method` — HTTP method
- `req.query` — query parameters
- `req.params` — path parameters

## Example

```zen
import (App, Request, listen) from "drift"

fn home(Request req) void {
  req.raw.send("Hello from Drift!")
}

fn user(Request req) void {
  string id = req.params.getString("id")
  req.raw.send("User: " + id)
}

fn routes(App app) void {
  app.get("/", home)
  app.get("/users/:id", user)
  app.static("/public", "./public")
}

listen(8080, routes)
```

Run the server:

```text
zen run main.zen
```

Then:

```text
curl http://localhost:8080/
curl http://localhost:8080/users/42
```

## Path Parameters

Use `:name` in a route pattern:

```zen
fn user(Request req) void {
  string id = req.params.getString("id")
  req.raw.send(id)
}

fn routes(App app) void {
  app.get("/users/:id", user)
}
```

Request:

```text
GET /users/42
```

`req.params` contains:

```text
id = "42"
```

## Query Parameters

Request:

```text
GET /search?name=john&age=20
```

Access values through `req.query`:

```zen
fn search(Request req) void {
  string name = req.query.getString("name")
  string age = req.query.getString("age")
  req.raw.send(name + " " + age)
}
```

## Static Files

```zen
fn routes(App app) void {
  app.static("/public", "./public")
}
```

For example:

```text
/public/index.html
/public/style.css
/public/app.js
```

Drift currently detects:

- `.html` → `text/html; charset=UTF-8`
- `.css` → `text/css`
- `.js` → `application/javascript`
- other extensions → `application/octet-stream`

## 404 Handling

If no registered route or static file handles a request, Drift automatically returns:

```text
404 Not Found
```

## License

Drift is licensed under the MIT License.

Copyright (c) 2026 Jishith M P
