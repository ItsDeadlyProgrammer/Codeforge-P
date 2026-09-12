# Architecture Documentation

This directory provides a high-level visual representation of the CodeForge platform.

## Diagram Maintenance
The primary system diagram for the project resides in `architecture.png`. This asset should be generated based on the architectural specifications defined in the `docs/architecture.md` file.

## Visualization Requirements
A professional engineering diagram should be placed here, covering:
* Data flow from the browser to the backend via REST.
* State persistence into PostgreSQL through Exposed ORM.
* Job distribution via Redis lists.
* The internal worker-executor lifecycle.
* Isolation strategies for user code execution using process-level separation within the Dockerized runtime environment.

For a textual explanation of these components and diagrams generated via Mermaid code, please refer to the [System Architecture Document](../docs/architecture.md).
