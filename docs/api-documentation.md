# API Documentation

The CodeForge backend exposes a RESTful JSON interface using the Ktor server framework. All request and response structures are serialized uniformly via `kotlinx.serialization`.

---

## Global Specifications
* **Base URL Path:** `https://codeforge-euxv.onrender.com/` 
* **Content Type Header:** `Content-Type: application/json`
* **Authentication:** None currently required (Public engineering sandbox endpoints).

---

## Endpoint Details

### 1. System Health Check
* **HTTP Method:** `GET`
* **Endpoint:** `/health`
* **Purpose:** Inspect system runtime status and infrastructure readiness.
* **Response Body (Text):** `"OK"`
* **Status Codes:**
  * `200 OK` — System is active and processing requests.

---

### 2. Retrieve All Problems
* **HTTP Method:** `GET`
* **Endpoint:** `/problems`
* **Purpose:** Returns a structured list of all competitive programming problems available in the database.
* **Response Body (JSON Example):**
  ```json
  [
    {
      "id": 1,
      "title": "Way Too Long Words",
      "description": "Abbreviate words longer than 10 characters...",
      "timeLimitMs": 1000,
      "memoryLimitMb": 256
    }
  ]
  ```
* **Status Codes:**
  * `200 OK` — Successful retrieval.

---

### 3. Import Custom Problem
* **HTTP Method:** `POST`
* **Endpoint:** `/problems/import`
* **Purpose:** Programmatically inserts a problem along with its accompanying test data matrix into the persistence engine.
* **Request Body (JSON Example):**
  ```json
  {
    "title": "Watermelon",
    "description": "Divide a watermelon into two even parts...",
    "timeLimitMs": 1000,
    "memoryLimitMb": 256,
    "samples": [
      {
        "input": "8\n",
        "output": "YES\n"
      }
    ]
  }
  ```
* **Response Body (JSON Example):**
  ```json
  {
    "id": 2
  }
  ```
* **Status Codes:**
  * `200 OK` — Problem and test suites successfully stored.

---

### 4. Submit Code Solution
* **HTTP Method:** `POST`
* **Endpoint:** `/submit`
* **Purpose:** Places a user code submission into the distributed database and appends its tracking token onto the Redis message queue.
* **Request Body (JSON Example):**
  ```json
  {
    "problemId": 1,
    "language": "python3",
    "code": "print('YES')\n"
  }
  ```
* **Response Body (JSON Example):**
  ```json
  {
    "submissionId": 42,
    "verdict": "QUEUED"
  }
  ```
* **Status Codes:**
  * `200 OK` — Ingestion succeeded, entry added to the message queue.

---

### 5. Fetch Submission Metrics & Verdict
* **HTTP Method:** `GET`
* **Endpoint:** `/submission/{id}`
* **Purpose:** Retrieve granular tracking, processing status, error logs, and detailed evaluation parameters for a given execution token.
* **Response Body (JSON Example):**
  ```json
  {
    "id": 42,
    "verdict": "ACCEPTED",
    "output": "YES\n",
    "runtimeMs": 45,
    "memoryKb": 12450,
    "testResults": [
      {
        "verdict": "ACCEPTED",
        "output": "YES\n",
        "runtimeMs": 45,
        "memoryKb": 12450,
        "passed": true
      }
    ]
  }
  ```
* **Status Codes:**
  * `200 OK` — Token resolved and entity schema returned.
  * `500 Internal Server Error` — Occurs if the requested ID does not exist or matches no record.

---

### 6. List Submission Feeds
* **HTTP Method:** `GET`
* **Endpoint:** `/submissions`
* **Purpose:** Retrieves a historical record of all submitted programs ordered chronologically by newest entries.
* **Response Body (JSON Example):**
  ```json
  [
    {
      "id": 42,
      "language": "python3",
      "verdict": "ACCEPTED",
      "createdAt": "2023-10-24T14:32:01.123"
    }
  ]
  ```
* **Status Codes:**
  * `200 OK` — History retrieved successfully.

---

### 7. External Codeforces Import Integration
* **HTTP Method:** `GET`
* **Endpoint:** `/cf/{contestId}/{index}`
* **Purpose:** Reaches out to external APIs to scrape and parse standard competitive programming data configurations.
* **Response Body (JSON Example):**
  ```json
  {
    "title": "Theatre Square",
    "statement": "Find the minimum number of flagstones...",
    "inputSpec": "The input contains two positive integers...",
    "outputSpec": "Print the required number of flagstones...",
    "timeLimit": 2000,
    "memoryLimit": 64,
    "samples": [
      {
        "input": "6 6 4\n",
        "output": "4\n"
      }
    ]
  }
  ```
* **Status Codes:**
  * `200 OK` — Successfully retrieved and parsed data from Codeforces.
