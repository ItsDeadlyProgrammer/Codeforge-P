# Engineering Decisions & Technical Design

This design document outlines the technical rationale, architectural trade-offs, and structural decisions governing the implementation of CodeForge.

---

## Language & Framework Rationale

### 1. Unified Language Strategy via Kotlin & Ktor
* **Decision:** Leverage Kotlin for both application servers and frontend presentation layers via Compose Multiplatform. Use Ktor as the core asynchronous backend engine.
* **Rationale:** Traditional setups mandate a multi-language stack (e.g., Node/React + Go/Python backend). Kotlin allows sharing high-fidelity data-transfer objects (DTOs) and data validation contracts seamlessly across boundaries. Ktor’s lightweight design provides non-blocking coroutine-driven thread execution, ensuring the server handles thousands of open polling requests with minimal overhead.

### 2. Exposed ORM over PostgreSQL
* **Decision:** Utilize the JetBrains Exposed framework to manage PostgreSQL storage interactions.
* **Rationale:** Type-safe SQL builders inside Exposed capture column mismatches or serialization errors at compile-time rather than runtime. PostgreSQL was chosen over NoSQL alternatives to guarantee strong structural consistency, cascading integrity, and transactional isolation during multi-step submission state mutations.

---

## Messaging & Queue-Based Processing

### 1. Redis Decoupled Worker Pipeline
* **Decision:** Implement a producer-consumer processing architecture backed by Redis lists and a background event-loop thread.
* **Rationale:** Code execution is inherently computationally heavy and unpredictable. If code evaluation ran synchronously within the HTTP thread pool, short-lived client request channels would saturate and time out. By pushing tasks to a Redis queue and using `BRPOP` for blocking consumption, the API layer maintains immediate response times (`QUEUED` state), insulating users from downstream processing latencies.

---

## Sandbox Isolation & Execution Security

### 1. In-Container Process Boundaries
* **Decision:** Drive user code compilation and execution using standard Java process control loops (`ProcessBuilder`) running directly inside an application environment packaged with pre-configured language toolchains.
* **Rationale:** The application’s master `Dockerfile` compiles and sets up `g++`, `python3`, and `openjdk-21`. Code executors write code strings out to distinct UUID scratchpad paths and delegate tasks to sub-processes.
* **Security & Failure Protections:**
  * **Resource Caps:** Execution loops enforce maximum duration checks via `.waitFor(timeLimit, TimeUnit.MILLISECONDS)`. Loops that hang are forcefully terminated using `destroyForcibly()`.
  * **Error Handling:** Standard error buffers (`errorStream`) are captured explicitly. Compilation crashes or exception stacktraces do not panic the primary application server; instead, they are cleanly bound, serialized, and persisted as `COMPILE_ERROR` or `RUNTIME_ERROR` verdicts.

---

## Architectural Trade-Offs & Limitations

### 1. Co-Located Background Worker Thread
* **Trade-Off:** Currently, the `RedisJudgeWorker` thread pool is co-located inside the primary Ktor application runtime rather than deployed as an isolated cluster of separate worker microservices.
* **Impact:** This simplifies single-instance deployment and local debugging loops, but leaves the API server vulnerable to resource exhaustion if multiple resource-heavy solutions compile concurrently. 

### 2. Basic Process-Level Isolation
* **Trade-Off:** Relying on standard OS sub-processes instead of spawning ephemeral sub-containers per compilation means submissions share the master host filesystem scope.
* **Impact:** Highly performant with negligible execution initialization latency, but requires rigid path-checking to ensure submissions cannot access systemic directory roots or read sibling evaluation scratchpads.

---

## Future Improvements

* **Isolated Distributed Micro-Workers (Future Improvement):** Decouple `RedisJudgeWorker` from the main Ktor jar completely, allowing the workers to spin up as autonomous, autoscaling container blocks on separate nodes.
* **Kernel-Level Sandboxing via gVisor/NSJail (Future Improvement):** Wrap sub-process invocations inside Linux namespaces or gVisor runtimes to prevent unauthorized syscall utilization and lock down network sockets at the kernel level.
* **Real-time Push notifications (Future Improvement):** Replace long-polling routes with a high-performance WebSocket router to push evaluation results instantly to the web layer.
