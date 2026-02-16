# Identifying Endpoints

You must know where to look before you can start fuzzing Web APIs. Identifying the endpoints that the API exposes is the first crucial step in this process. This involves some detective work, but several methods can help uncover these hidden doorways to the application's data and functionality.

## REST

REST APIs are built around the concept of resources, which are identified by unique URLs called endpoints. These endpoints are the targets for client requests, and they often include parameters to provide additional context or control over the requested operation.

### REST Endpoints

Endpoints in REST APIs are structured as URLs representing the resources you want to access or manipulate:

- `/users` - Collection of user resources
- `/users/123` - Specific user with ID 123
- `/products` - Collection of product resources
- `/products/456` - Specific product with ID 456

### REST Parameters

| Type | Description | Example |
|------|-------------|---------|
| Query | Appended after `?`, used for filtering, sorting, pagination | `/users?limit=10&sort=name` |
| Path | Embedded in URL, identifies specific resources | `/products/{id}` |
| Body | Sent in POST/PUT/PATCH requests, creates or updates resources | `{ "name": "New Product", "price": 99.99 }` |

### Discovering REST Endpoints

- **API Documentation**: Refer to official specs (Swagger/OpenAPI, RAML)
- **Network Traffic Analysis**: Use Burp Suite or browser dev tools to inspect requests/responses
- **Parameter Fuzzing**: Use ffuf/wfuzz with wordlists to discover hidden parameters

## SOAP

SOAP (Simple Object Access Protocol) APIs use XML-based messages and WSDL (Web Services Description Language) files to define their interfaces.

### SOAP Characteristics

- Single endpoint URL where SOAP server listens
- Parameters defined in XML-based SOAP message body
- Structure defined in WSDL file

### SOAP Example

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:lib="http://example.com/library">
    <soapenv:Header/>
    <soapenv:Body>
        <lib:SearchBooks>
            <lib:keywords>cybersecurity</lib:keywords>
            <lib:author>Dan Kaminsky</lib:author>
        </lib:SearchBooks>
    </soapenv:Body>
</soapenv:Envelope>
```

### Discovering SOAP Endpoints

- **WSDL Analysis**: Parse WSDL to identify operations, parameters, data types, and endpoint URL
- **Network Traffic Analysis**: Use Wireshark/tcpdump to capture and examine SOAP traffic
- **Fuzzing**: Send malformed values in SOAP requests to uncover hidden operations

## GraphQL

GraphQL APIs expose a single endpoint (typically `/graphql`) and use a unique query language for flexible data requests.

### GraphQL Queries

Queries fetch data with precise field selection:

```graphql
query {
  user(id: 123) {
     name
     email
     posts(limit: 5) {
        title
        body
     }
  }
}
```

| Component | Description | Example |
|-----------|-------------|---------|
| Field | Specific data to retrieve | `name`, `email` |
| Relationship | Connection between data types | `posts` |
| Nested Object | Field returning another object | `posts { title, body }` |
| Argument | Modifies behavior (filtering, pagination) | `posts(limit: 5)` |

### GraphQL Mutations

Mutations modify data (create, update, delete):

```graphql
mutation {
  createPost(title: "New Post", body: "This is the content of the new post") {
     id
     title
  }
}
```

| Component | Description | Example |
|-----------|-------------|---------|
| Operation | Action to perform | `createPost`, `updateUser`, `deleteComment` |
| Argument | Input data required | `title: "New Post"` |
| Selection | Response fields | `id`, `title` |

### Discovering GraphQL APIs

- **Introspection**: Send introspection queries to retrieve complete schema with types, fields, queries, and mutations
- **API Documentation**: Review guides with GraphiQL/GraphQL Playground for interactive exploration
- **Network Traffic Analysis**: Capture requests to `/graphql` endpoint to observe real-world queries and mutations

GraphQL is designed for flexibility, so focus on understanding the underlying schema and how to construct valid requests.
