# 🎬 Product Walkthrough & Live Demo

This directory contains the operational demonstration, media assets, and active distribution links for CodeForge.

---

## 🌐 Live Deployments

*   **Web Application Frontend:** [https://super-lolly-fd22a7.netlify.app/](https://super-lolly-fd22a7.netlify.app/)
    *   *Architecture Note:* Built using Compose Multiplatform compiled down to WebAssembly (WASM), delivering a full material-designed canvas interface directly in modern web browsers.
*   **Backend Application Server:** [https://codeforge-euxv.onrender.com/health](https://codeforge-euxv.onrender.com/health)
    *   *Architecture Note:* The core Ktor API service exposes JSON REST endpoints and routes processing payloads to the background processing worker.

---

## 📺 Interactive Product Walkthrough

Below is the live platform demonstration. It showcases problem fetching via external connectors, solution staging, and isolated execution telemetry updates.

<p align="center">
  <img src="./Codeforge.gif" width="100%" alt="CodeForge Product Walkthrough">
</p>

### 📝 Core User Workflow Illustrated

1.  **Dashboard Ingestion:** Navigating to the client interface displaying challenge cards synced from PostgreSQL.
2.  **API Connection Scrape:** Inputting a Codeforces challenge token (e.g., `4C` or `158A`) to programmatically load structured layout data and constraints.
3.  **Template Generation:** Opening the multi-language code editor workspace and selecting a target compiler environment (Python 3, C++17, or Java 21).
4.  **Decoupled Submission Pipeline:** Pressing submit, triggering an instant HTTP response while the job ID transitions to a `QUEUED` state via the Redis broker.
5.  **Telemetry & Verdict Processing:** Witnessing the background `RedisJudgeWorker` spin up subprocess wrappers, pipe stream data, evaluate outputs, and update the interactive verdict grid with precise execution duration metrics.
