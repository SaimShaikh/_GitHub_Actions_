| Category | Variable                | Description                                                   |
| -------- | ----------------------- | ------------------------------------------------------------- |
| General  | GITHUB_ACTION           | Name of the action currently running graphite​                |
|          | GITHUB_ACTIONS          | Always "true" when GitHub Actions is running graphite​        |
|          | GITHUB_ACTOR            | Actor (user/org/app) that triggered the workflow graphite​    |
|          | GITHUB_API_URL          | GitHub API URL graphite​                                      |
|          | GITHUB_BASE_REF         | Base ref for PRs (branch/PR target) graphite​                 |
|          | GITHUB_EVENT_NAME       | Event that triggered workflow (e.g., "push") graphite​        |
|          | GITHUB_EVENT_PATH       | Path to JSON event payload graphite​                          |
|          | GITHUB_GRAPHQL_URL      | GitHub GraphQL API URL graphite​                              |
|          | GITHUB_HEAD_REF         | Head ref for PRs (source branch) graphite​                    |
|          | GITHUB_REF              | Full ref of branch/tag/PR (e.g., "refs/heads/main") graphite​ |
|          | GITHUB_REF_NAME         | Short ref name (e.g., "main") graphite​                       |
|          | GITHUB_REF_PROTECTED    | "true" if branch protected graphite​                          |
|          | GITHUB_REF_TYPE         | "branch" or "tag" graphite​                                   |
|          | GITHUB_REPOSITORY       | "owner/repo" graphite​                                        |
|          | GITHUB_REPOSITORY_OWNER | Repository owner graphite​                                    |
|          | GITHUB_RETRY_COUNT      | Current retry number (0-based) graphite​                      |
|          | GITHUB_RUN_ATTEMPT      | Attempt number of this run graphite​                          |
|          | GITHUB_RUN_ID           | Unique ID for workflow run graphite​                          |
|          | GITHUB_RUN_NUMBER       | Incremental run number graphite​                              |
|          | GITHUB_SERVER_URL       | GitHub server URL graphite​                                   |
|          | GITHUB_SHA              | Commit SHA graphite​                                          |
|          | GITHUB_SHORT_SHA        | First 8 chars of SHA (since 2025) graphite​                   |
|          | GITHUB_TOKEN            | Token for API/repo access graphite​                           |
|          | GITHUB_WORKFLOW         | Workflow filename graphite​                                   |
|          | GITHUB_WORKSPACE        | Default working directory graphite​                           |
| Jobs     | GITHUB_JOB              | Current job ID graphite​                                      |
|          | GITHUB_MATRIX_*         | Matrix values (e.g., GITHUB_MATRIX_OS) graphite​              |
| Runner   | RUNNER_ARCH             | Runner architecture (X64, ARM64) graphite​                    |
|          | RUNNER_ENVIRONMENT      | Runner environment (e.g., "github-hosted") graphite​          |
|          | RUNNER_NAME             | Runner name graphite​                                         |
|          | RUNNER_OS               | OS (Linux, Windows, macOS) graphite​                          |
|          | RUNNER_TEMP             | Temp dir path graphite​                                       |
|          | RUNNER_TOOL_CACHE       | Tool cache dir graphite​                                      |
|          | RUNNER_TRACKING_URI     | Runner tracking URI graphite​                                 |
|          | RUNNER_TEMP             | Temp dir graphite​                                            |
