# 1. Architecture and Data Flow --- Gitleaks in ReleaseShield AI

## Purpose

Gitleaks is the secret-scanning tool in this integration. It scans a
target repository and writes findings in SARIF format so the existing
ReleaseShield scan pipeline can process them alongside findings from
other tools.

## Relevant project areas

The project context identifies an npm-workspaces monorepo under
`cli_frontend`:

-   `packages/core` --- scanner orchestration, shared tool runners,
    SARIF handling, and core tests.
-   `packages/cli` --- command-line/TUI and non-interactive CLI entry
    points.

Files identified during the integration work:

  ------------------------------------------------------------------------------------------
  File                                                   Responsibility
  ------------------------------------------------------ -----------------------------------
  `cli_frontend/packages/core/src/Scanner/gitleaks.ts`   Gitleaks wrapper: availability
                                                         check, argument delegation, input
                                                         validation, and runner execution

  `cli_frontend/packages/core/src/tools.ts`              Shared tool-runner registry and
                                                         common execution path; Gitleaks
                                                         runner is registered here

  `cli_frontend/packages/core/src/types.ts`              Shared scan option/output types
                                                         used by the wrapper

  `cli_frontend/packages/core/src/sarif.ts`              SARIF parsing/normalization/merging
                                                         path identified in the project
                                                         context; confirm current
                                                         names/exports in the checked-out
                                                         branch

  `cli_frontend/packages/core/src/findings.ts`           Findings-related processing
                                                         identified in the project context;
                                                         confirm current use in the
                                                         checked-out branch

  `cli_frontend/packages/cli/src/index.tsx`              CLI entry point identified in the
                                                         project context

  `cli_frontend/packages/cli/src/nonInteractive.ts`      Non-interactive CLI path identified
                                                         in the project context

  `cli_frontend/packages/core/test/gitleaks.test.ts`     Unit tests added for the Gitleaks
                                                         wrapper
  ------------------------------------------------------------------------------------------

> The file list and high-level flow were assembled from the integration
> work context. Confirm paths and the actual call chain against the
> current repository before treating this as a complete architecture
> specification.

## Gitleaks wrapper behavior

`Scanner/gitleaks.ts` exposes three functions:

1.  `isGitleaksAvailable()` delegates to
    `checkToolAvailability("gitleaks")`.
2.  `buildGitleaksArgs(targetPath, outputPath)` delegates argument
    construction to `getToolRunner("gitleaks")`.
3.  `runGitleaks(options)` validates that `targetPath` and `outputPath`
    are non-empty, returns a failed `ToolRunOutput` for blank inputs,
    and otherwise delegates execution to the shared runner.

The wrapper uses shared `ToolRunOptions` and `ToolRunOutput` types
rather than introducing a separate result contract.

## Runner command

The registered Gitleaks runner builds the following argument list:

``` text
gitleaks detect
  --source <targetPath>
  --report-format sarif
  --report-path <outputPath>
```

In array form, the implementation uses:

``` ts
[
  "detect",
  "--source",
  targetPath,
  "--report-format",
  "sarif",
  "--report-path",
  outputPath,
]
```

The common runner is responsible for executing the tool. The broader
scan pipeline was described as:

``` text
CLI / non-interactive entry point
        |
        v
scan orchestration (runScan / related core scan path)
        |
        v
shared runner registry (tools.ts)
        |
        v
Gitleaks process
        |
        v
SARIF report written to outputPath
        |
        v
SARIF parse / normalize / merge path
        |
        v
combined findings and CLI/report output
```

The exact functions and final output paths can vary by
mode/configuration; inspect the current branch before documenting a
specific generated filename as guaranteed.

## Input and output contract

-   **Input:** target repository path and output report path, passed
    through `ToolRunOptions`.
-   **Scanner:** Gitleaks executable available on `PATH`.
-   **Output format:** SARIF (`--report-format sarif`).
-   **Validation:** blank/missing target path or SARIF output path
    returns a failed result without invoking the runner.
-   **Availability:** wrapper checks whether the `gitleaks` executable
    can be found.

## Why SARIF?

SARIF gives the project a common machine-readable format for security
findings. Gitleaks can therefore contribute findings to the existing
pipeline instead of requiring an entirely separate output format.

## What to verify before publishing

-   Check whether the current CLI routes `--tools gitleaks` into the
    shared `runScan` path.
-   Confirm the exact function that reads the Gitleaks SARIF report and
    merges findings.
-   Confirm where scan output is written for the chosen CLI mode.
-   Do not claim that the full end-to-end route passed until it has been
    run and its output saved.
