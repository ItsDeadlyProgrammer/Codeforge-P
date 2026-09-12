# Project Architecture & Module Structure

CodeForge is organized as a multi-module Gradle project. This decoupling enforces clean architectural separation between presentation components, shared serialization contracts, and background compute runtimes.

---

## Architectural Breakdown of Sub-Projects

### 1. `backend` Module
The operational heart of the system. This module compiles down to a single standalone executable service handling both incoming web requests and worker operations.
* **Responsibilities:**
  * **Routing & Controllers:** Exposes endpoints for managing problems, routing submission requests, and interfacing with external data scrapers.
  * **Background Processing Loop (`worker` Package):** Houses the `RedisJudgeWorker` thread pool, which polls the message broker and triggers local compilation cycles.
  * **Compilation & Execution Engine (`executor` Package):** Contains language-specific isolation managers (`DockerPythonExecutor`, `DockerCppExecutor`, `DockerJavaExecutor`) that safely call subprocess runtimes, manage runtime clocks, and parse terminal streams.
  * **Persistence Management:** Configures database connection lifecycles and initializes tables using the Exposed mapping framework.

### 2. `app` Module (Compose Multiplatform Applications)
Contains the cross-platform user experience codebase. This sub-project leverages a federated design to compile one layout implementation into multiple native targets.
* **`app:shared`:** Houses the core design tokens, view models, network request managers, and Material 3 UI layouts. Everything here is pure Kotlin and Jetpack Compose.
* **`app:webApp` / `app:androidApp` / `app:desktopApp`:** Target-specific entry points. They contain the lightweight compilation bindings needed to mount the shared application canvas into an active Android Activity, a Windows desktop window framework, or a browser canvas via WebAssembly (WASM).

### 3. `core` / `server` Modules
Provide shared utilities and structural data specifications.
* **Responsibilities:**
  * Define standard serializable data transfer objects (DTOs) utilized by both the frontend application and the backend Ktor server.
  * Prevent code duplication by ensuring networking request payloads perfectly mirror the validation models expected by the database controllers.

---

## Design Advantages

* **High Cohesion, Low Coupling:** Changes made to execution sandbox logic inside `backend` require zero structural modifications within the UI presentation layers (`app`).
* **Shared Contract Integrity:** Because DTO models are compiled into a shared dependency block, modifications to an API payload immediately flag compile-time errors if the client or server goes out of sync, preventing runtime parsing exceptions.
