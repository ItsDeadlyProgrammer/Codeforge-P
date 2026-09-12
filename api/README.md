# API Quick Reference

This directory serves as a high-level index of the available endpoints in CodeForge. For full request/response schemas, status codes, and implementation trade-offs, refer to the [Full API Documentation](../docs/api-documentation.md).

## Endpoints Summary

### System Health
* `GET /health` — Check server status and database connectivity.

### Problem Management
* `GET /problems` — Retrieve a list of all available competitive programming problems.
* `POST /problems/import` — Save an imported problem along with its test cases into the database.

### Submissions & Judging
* `POST /submit` — Place a code solution onto the Redis asynchronous judging queue.
* `GET /submission/{id}` — Fetch detailed execution statistics, verdicts, and test case evaluation summaries for a submission.
* `GET /submissions` — Retrieve the history of all compiled and evaluated user submissions.

### External Integration
* `GET /cf/{contestId}/{index}` — Fetch problem descriptions, constraints, and official sample test cases from Codeforces using the Codeforces API.

---

For detailed payloads and examples, please consult the complete architectural guide in [API Documentation](../docs/api-documentation.md).
