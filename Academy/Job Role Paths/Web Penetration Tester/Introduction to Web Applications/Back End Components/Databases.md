# Databases

Web applications utilize back end databases to store various content and information. This includes core assets like images and files, content like posts and updates, and user data like usernames and passwords. Databases enable dynamic content that varies per user and allow quick data storage and retrieval.

## Database Selection Criteria

Most developers evaluate databases based on:
- **Speed**: Data storage and retrieval performance
- **Size**: Capacity for large datasets
- **Scalability**: Growth accommodation
- **Cost**: Implementation and maintenance expenses

## Relational (SQL) Databases

Relational databases organize data in tables, rows, and columns with unique keys that link tables together.

**Example Structure:**
- **users** table: id, username, first_name, last_name
- **posts** table: id, user_id, date, content

By linking the `id` column from users to `user_id` in posts, user details are retrieved without duplication. This relationship structure is called a **schema**.

### Common SQL Databases

| Database | Description |
|----------|-------------|
| MySQL | Most common; free, open-source |
| MSSQL | Microsoft's relational database; Windows/IIS compatible |
| Oracle | Enterprise-grade; frequent updates; higher cost |
| PostgreSQL | Free, open-source; highly extensible |

Other options: SQLite, MariaDB, Amazon Aurora, Azure SQL

## Non-relational (NoSQL) Databases

NoSQL databases don't use tables, keys, or schemas. They store data using flexible storage models, ideal for unstructured datasets.

### Storage Models

- **Key-Value**: Stores data as key-value pairs (JSON/XML)
- **Document-Based**: Complex JSON objects with metadata
- **Wide-Column**: Columnar storage format
- **Graph**: Node and relationship-based storage

**Key-Value Example (JSON):**
```json
{
    "100001": {
        "date": "01-01-2021",
        "content": "Welcome to this web application."
    },
    "100002": {
        "date": "02-01-2021",
        "content": "This is the first post on this web app."
    }
}
```

### Common NoSQL Databases

| Database | Description |
|----------|-------------|
| MongoDB | Document-based; free, open-source; JSON storage |
| ElasticSearch | Optimized for large dataset analysis; fast search |
| Apache Cassandra | Scalable; fault-tolerant; free, open-source |

Other options: Redis, Neo4j, CouchDB, Amazon DynamoDB

## Integration in Web Applications

Modern frameworks simplify database integration. After installation and setup on the backend, applications can store and retrieve data.

**PHP + MySQL Example:**

Connect to database:
```php
$conn = new mysqli("localhost", "user", "pass", "database1");
```

Query data:
```php
$searchInput = $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```

Return results:
```php
while($row = $result->fetch_assoc()) {
        echo $row["name"]."<br>";
}
```

**Security Note**: Unsecured database code can cause vulnerabilities like SQL Injection. Always use prepared statements and validate user input.
