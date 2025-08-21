# URL Shortener with Rate Limiting (Go + Fiber + Redis)

### A simple URL shortener service built with Golang, Fiber and Redis. The service allows you to shorten long URLs, create custom short URLs, set expiry times, and includes per-IP rate limiting to prevent abuse.


## Features

#### - Shorten any valid URL
#### - Set expiration times (default: 20 hours)
#### - Rate limiting using Redis (default: 10 requests per 30 minutes per IP)
#### - Automatic HTTPS enforcement (if no scheme provided)
#### - Redirect counting (hit counter stored in Redis)

## How It Works

### 1. Shorten a URL
POST /api/v1

#### Request Body
```json
{
    "url" : "https://www.example.com/main?v=3ExDEeSnyvE&list="
}
```

#### Response Body:
```json
{
  "url": "https://google.com",
  "short": "localhost:3000/myid",
  "expiry": 24,
  "rate_limit": 9,
  "rate_limit_reset": 30
}
```

### 2. Redirect to Original URL
GET /:url

#### If you visit this, the service will:

##### a. Look up the mapping in Redis
##### b. Increment a redirect counter
##### c. Redirect you (301) to the original URL

#### If the URL does not exist, it returns:
```json
{
  "error": "short url not found in the DB"
}
```

### 3. Rate Limiting

#### Each IP gets 10 requests per 30 minutes (configurable via API_QUOTA in .env)

#### If exceeded, API returns:
```json
{
  "error": "Rate limit exceeded",
  "rate_limit_rset": 28
}
```


## Setup Instructions

### Install Dependencies

| Make sure you have:        |
|----------------------------|
| Go 1.20+                   |
Docker + Docker Compose      |

### Clone and Build
```sh
git clone https://github.com/Mahima-Prajapati/URLShortener.git
cd URLShortener
```


### Run with Docker Compose
```sh
docker-compose up --build
```


#### This will:

##### Build and run the Go API on localhost:3000
##### Start Redis on localhost:6379

### Test the API

#### Use curl or Postman:
```sh
curl -X POST localhost:3000/api/v1 \
  -H "Content-Type: application/json" \
  -d '{"url":"https://google.com"}'
```

#### Visit the returned short URL in your browser:
`http://localhost:3000/<id>`
