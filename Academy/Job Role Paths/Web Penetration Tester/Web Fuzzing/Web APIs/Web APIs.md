# Web APIs

A Web API (Web Application Programming Interface) is a set of rules and specifications enabling different software applications to communicate over the web. It functions as a bridge between a server (hosting data and functionality) and a client (web browser, mobile app, or another server).

## Common Web API Types

### REST (Representational State Transfer)
- Uses stateless, client-server communication
- Leverages standard HTTP methods (GET, POST, PUT, DELETE) for CRUD operations
- Exchanges data in JSON or XML format

**Example:**
```http
GET /users/123
```

### SOAP (Simple Object Access Protocol)
- Formal, standardized protocol for structured information exchange
- Uses XML messages encapsulated in SOAP envelopes
- Includes built-in security, reliability, and transaction management

**Example:**
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org/">
    <soapenv:Body>
        <tem:GetStockPrice>
            <tem:StockName>AAPL</tem:StockName>
        </tem:GetStockPrice>
    </soapenv:Body>
</soapenv:Envelope>
```

### GraphQL
- Query language with a single endpoint
- Clients request only needed data (prevents over/under-fetching)
- Strong typing and introspection capabilities

**Example:**
```graphql
query {
  user(id: 123) {
     name
     email
  }
}
```

## Key Advantages
- Standardized data access and manipulation
- Code reusability and integration of third-party services
- Foundation for microservices architecture
- Enhanced scalability and flexibility

## Web APIs vs. Web Servers

| Feature | Web Server | API |
|---------|-----------|-----|
| **Purpose** | Serve static/dynamic web pages | Enable application-to-application communication |
| **Communication** | HTTP with web browsers | HTTP, HTTPS, SOAP, or others |
| **Data Format** | HTML, CSS, JavaScript | JSON, XML, or others |
| **User Interaction** | Direct browser interaction | Indirect (app-mediated) |
| **Access** | Publicly accessible | Public, private, or partner-only |
| **Example** | Website rendering HTML pages | Weather app fetching data from API |

When fuzzing APIs, focus on endpoints and parameters rather than hidden directories, paying attention to data formats in requests and responses.
