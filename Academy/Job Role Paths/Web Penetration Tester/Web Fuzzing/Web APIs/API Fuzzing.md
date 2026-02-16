# API Fuzzing

API fuzzing is a specialized form of fuzzing tailored for web APIs. While the core principles of fuzzing remain the same—sending unexpected or invalid inputs to a target—API fuzzing focuses on the unique structure and protocols used by web APIs.

API fuzzing involves bombarding an API with automated tests, where each test sends a slightly modified request to an API endpoint. These modifications might include:

- Altering parameter values
- Modifying request headers
- Changing the order of parameters
- Introducing unexpected data types or formats

The goal is to trigger API errors, crashes, or unexpected behavior, revealing potential vulnerabilities like input validation flaws, injection attacks, or authentication issues.

## Why Fuzz APIs?

API fuzzing is crucial for several reasons:

- **Uncovering Hidden Vulnerabilities**: APIs often have hidden or undocumented endpoints and parameters susceptible to attacks. Fuzzing helps uncover these hidden attack surfaces.
- **Testing Robustness**: Fuzzing assesses the API's ability to gracefully handle unexpected or malformed input without crashing or exposing sensitive data.
- **Automating Security Testing**: Manual testing of all possible input combinations is infeasible. Fuzzing automates this process, saving time and effort.
- **Simulating Real-World Attacks**: Fuzzing can mimic malicious actors' actions, allowing you to identify vulnerabilities before attackers exploit them.

## Types of API Fuzzing

### Parameter Fuzzing
Focuses on systematically testing different values for API parameters, including query parameters, headers, and request bodies. By injecting unexpected or invalid values, this technique exposes vulnerabilities like SQL injection, command injection, XSS, and parameter tampering.

### Data Format Fuzzing
Targets structured data formats like JSON or XML by manipulating structure, content, or encoding. This reveals vulnerabilities related to parsing errors, buffer overflows, or improper handling of special characters.

### Sequence Fuzzing
Examines how APIs respond to sequences of requests, uncovering vulnerabilities like race conditions, insecure direct object references (IDOR), or authorization bypasses.

## Exploring the API

Start the target system and access the API documentation at `http://IP:PORT/docs`. The FastAPI interface shows five endpoints:

- `GET /` - Fetches the root resource
- `GET /items/{item_id}` - Retrieves a specific item
- `DELETE /items/{item_id}` - Deletes an item
- `PUT /items/{item_id}` - Updates an item
- `POST /items/` - Creates or updates an item

**Note**: APIs may contain undocumented endpoints omitted from public documentation for various reasons (internal use, security through obscurity, development).

## Fuzzing the API

Use a fuzzer with a wordlist to discover undocumented endpoints:

```bash
git clone https://github.com/PandaSt0rm/webfuzz_api.git
cd webfuzz_api
pip3 install -r requirements.txt
python3 api_fuzzer.py http://IP:PORT
```

The fuzzer identifies:
- **Valid endpoints**: `/docs` (documented) and `/cz...` (undocumented)
- **405 Method Not Allowed**: `/items` endpoint with incorrect HTTP method

Explore the undocumented endpoint:

```bash
curl http://localhost:8000/cz...
```

## Parameter Fuzzing for Vulnerabilities

Parameter fuzzing can reveal:

- **Broken Object-Level Authorization**: Unauthorized access through parameter manipulation
- **Broken Function-Level Authorization**: Unauthorized function calls via parameter manipulation
- **Server-Side Request Forgery (SSRF)**: Malicious parameter values trick the server into unintended requests

For more details on web API vulnerabilities, refer to the API Attacks module.
