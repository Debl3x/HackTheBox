# Development Frameworks & APIs

## Web Development Frameworks

Web development frameworks simplify building modern web applications by providing reusable components for common functionality like user registration.

**Common frameworks:**
- **Laravel** (PHP) - Popular with startups and smaller companies
- **Express** (Node.JS) - Used by PayPal, Yahoo, Uber, IBM, MySpace
- **Django** (Python) - Used by Google, YouTube, Instagram, Mozilla, Pinterest
- **Rails** (Ruby) - Used by GitHub, Hulu, Twitch, Airbnb, Twitter

> Popular websites typically use multiple frameworks and web servers rather than just one.

## APIs

Web APIs enable communication between front-end and back-end components, allowing data exchange and task execution.

### Query Parameters

The default method for sending arguments uses GET and POST request parameters via the URL or POST data.

**Example:** `/search.php?item=apples`

### Web APIs Overview

An **API (Application Programming Interface)** provides remote access to back-end functionality over HTTP.

**Example use cases:**
- Weather API returning current conditions in JSON
- Twitter API retrieving tweets in XML or JSON format

## API Standards

### SOAP

**SOAP (Simple Objects Access)** transfers structured data using XML format.

**Advantages:**
- Handles complex and binary data
- Supports serialized objects and stateful operations

**Disadvantages:**
- Complex and verbose for simple queries

### REST

**REST (Representational State Transfer)** shares data through URL paths and returns JSON responses.

**HTTP Methods:**
- `GET` - Retrieve data
- `POST` - Create data (non-idempotent)
- `PUT` - Create or replace data (idempotent)
- `DELETE` - Remove data

**Advantages:**
- Simpler and more modular than SOAP
- Better for search, sort, and filter operations
- Scalable architecture
