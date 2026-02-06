# CRUD API

## Overview
Web applications often expose **API endpoints** to interact with backend databases. These APIs commonly allow clients to specify a database entity and perform actions on it using different HTTP methods. Instead of using URL parameters and server-rendered pages, APIs typically exchange structured data such as JSON.

---

## APIs and Database Interaction
Many APIs are designed to operate directly on database tables and rows. A typical API structure allows:
- Specifying the target table
- Identifying a specific record
- Using an HTTP method to define the desired operation

Example structure:
```

/api.php/<table>/<entity>

````

---

## CRUD Operations
Most database-driven APIs support four fundamental operations, commonly referred to as **CRUD**.

| Operation | HTTP Method | Description |
|---------|-------------|-------------|
| Create | POST | Inserts a new entry into the database |
| Read | GET | Retrieves data from the database |
| Update | PUT | Modifies an existing database entry |
| Delete | DELETE | Removes an entry from the database |

These principles are also used in RESTful APIs, though access control determines which operations are permitted for a given user.

---

## Read
Data retrieval is performed using the GET method.

### Reading a Specific Entry
```bash
curl http://<SERVER_IP>:<PORT>/api.php/city/london
````

The response is returned in JSON format. For improved readability, output can be formatted using `jq`:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq
```

### Searching and Listing

* Partial matches:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/le | jq
```

* All entries:

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/ | jq
```

---

## Create

New entries are added using a POST request with JSON data.

```bash
curl -X POST \
  http://<SERVER_IP>:<PORT>/api.php/city/ \
  -d '{"city_name":"HTB_City","country_name":"HTB"}' \
  -H 'Content-Type: application/json'
```

The newly created entry can then be retrieved using a GET request.

---

## Update

Updating existing entries is done using the PUT method. The target entity must be specified in the URL.

```bash
curl -X PUT \
  http://<SERVER_IP>:<PORT>/api.php/city/london \
  -d '{"city_name":"New_HTB_City","country_name":"HTB"}' \
  -H 'Content-Type: application/json'
```

After the update, the original entity is replaced with the new data.

### PUT vs PATCH

* **PUT**: Replaces the entire entity
* **PATCH**: Modifies only specific fields

API behavior varies; some APIs may create new entries if the target entity does not exist.

---

## Delete

Entries are removed using the DELETE method.

```bash
curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```

Attempting to read a deleted entry returns an empty result.

---

## Authentication and Access Control

In real-world applications:

* CRUD operations are often restricted by user roles
* Authentication may require cookies, API keys, or tokens (e.g. JWT)
* Unauthorized write access is considered a serious security vulnerability

Once authenticated, API interactions follow the same principles demonstrated in this section.

---

## Key Takeaways

* CRUD defines standard database operations in APIs
* HTTP methods map directly to CRUD actions
* APIs typically exchange JSON data
* cURL is an effective tool for testing API endpoints
* Proper authentication and authorization are essential for API security.
