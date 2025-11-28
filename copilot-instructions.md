# Configuration Driven Maintenance (CDM) System Instructions

## Overview
The Configuration Driven Maintenance (CDM) system is an automated helper for SRE and operations teams. It performs automated checks on maintenance items to proactively notify teams when work is required, reducing manual toil and ensuring systems are kept up to date.

## Architecture
The system is composed of four main components:

1.  **cdm-cli** (`cdm-cli/`): The orchestrator tool.
    -   **Role**: Scaffolds new checks and executes existing checks.
    -   **Execution**: It does *not* compile checks at runtime. It downloads pre-compiled binaries from GitHub Releases based on the check name and version.
    -   **Scaffolding**: Generates new check structures using templates.

2.  **cdm_framework** (`cdm_framework/`): A shared Go library.
    -   **Role**: Standardizes logging, environment initialization, and error reporting for all checks.
    -   **Key Functions**: `InitEnvironment`, `RunCheck`, `FailCheck`, `CheckFailedReport`.

3.  **cdm-checks** (`cdm-checks/`): A repository of modular check applications.
    -   **Structure**: Each check is a standalone Go application.
    -   **Location**: Organized by `provider/resource/check_name`.

4.  **cdm_pipeline** (`cdm_pipeline/`): Azure DevOps pipeline configuration.
    -   **Role**: Schedules and triggers the checks.
    *   **Mechanism**: Downloads `cdm-cli`, sets environment variables, and invokes `cdm-cli runCheck`.

## Data Flow & Execution
1.  **Pipeline Trigger**: The Azure DevOps pipeline (`sre_cdm_checks.yml`) runs on a schedule.
2.  **CLI Setup**: The pipeline downloads the `cdm-cli` binary.
3.  **Check Invocation**: The pipeline runs `./cdm-cli runCheck` with specific environment variables (`CHECK_NAME`, `CHECK_VERSION`, `SKIP_UNTIL`, and check-specific config).
4.  **Binary Download**: The CLI downloads the specific check binary from GitHub Releases.
    -   *Pattern*: `https://github.com/IamSamD/cdm-checks/releases/download/{BINARY_NAME}_{VERSION}/{BINARY_NAME}`
    -   *Naming*: Slugs in check names (e.g., `general/source_control/dependabotprs`) are converted to underscores (e.g., `general_source_control_dependabotprs`).
5.  **Execution**: The CLI runs the check binary as a subprocess.
6.  **Result**:
    -   **Exit Code 0**: Check Passed.
    -   **Exit Code 2**: Check Failed (Maintenance required).
    -   **Other**: System Error.

## Developer Guide

### 1. Scaffolding a New Check
Use the CLI to generate the boilerplate code.
```bash
cdm-cli addCheck -p <provider> -r <resource> -n <name>
```
Example: `cdm-cli addCheck -p general -r source_control -n my_new_check`

### 2. Implementing Logic
-   **Entry Point**: `main.go` initializes the framework.
-   **Logic**: Edit `check/check.go`.
-   **Inputs**: Define required environment variables in the `InitEnvironment` call in `main.go`.
-   **Reporting**:
    -   If a maintenance condition is met (check fails), call `framework.FailCheck()`.
    -   Log details using `framework.CheckFailedReport("Reason...")`.

### 3. Local Development & Testing
The `cdm-cli` and individual checks support `.env` files (using `godotenv`) to simulate the pipeline environment locally.

1.  Create a `.env` file in your check's directory or the CLI root.
2.  Add the variables that the pipeline would usually inject:
    ```env
    CHECK_NAME=general/source_control/dependabotprs
    CHECK_VERSION=v0.0.6
    # Check specific config
    GITHUB_TOKEN=your_local_token
    NO_OF_PRS_THRESHOLD=5
    ```
3.  Run the check directly or via the CLI to test logic without deploying.

## Coding Standards
-   **Framework Usage**: Always use `cdm_framework` for logging and exit handling. Do not use `os.Exit` directly unless necessary; prefer `framework.FailCheck()`.
-   **Configuration**: All inputs must be passed via environment variables.
-   **Idempotency**: Checks should be safe to run multiple times.

## Future Roadmap
-   **Azure Boards Integration**: The `cdm-cli` will be updated to communicate with Azure DevOps Boards. It will automatically raise tickets/work items when a check fails (Exit Code 2), streamlining the workflow from detection to action.
