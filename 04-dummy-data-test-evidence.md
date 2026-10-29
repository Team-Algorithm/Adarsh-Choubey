# 4. Dummy-Data Test Evidence and Verification Log

## Purpose

This document separates evidence already reported during implementation
from tests that still need to be run. It is intended for a portfolio or
pull-request description and should be updated with real terminal output
after a reproducible test run.

## Implemented behavior

The Gitleaks wrapper in
`cli_frontend/packages/core/src/Scanner/gitleaks.ts`:

-   checks tool availability through the shared
    `checkToolAvailability("gitleaks")`;
-   delegates CLI argument creation to the registered `gitleaks` runner;
-   rejects a blank target path;
-   rejects a blank SARIF output path;
-   delegates valid execution options to the shared runner.

The registered runner's intended command arguments are:

``` text
detect --source <targetPath> --report-format sarif --report-path <outputPath>
```

## Unit test cases added

`cli_frontend/packages/core/test/gitleaks.test.ts` contains tests for:

1.  Delegating argument construction to the runner.
2.  Returning failure for a blank target path.
3.  Returning failure for a blank output path.
4.  Forwarding valid options to the runner.
5.  Delegating the availability check.

Run them from `cli_frontend`:

``` powershell
npx vitest run packages/core/test/gitleaks.test.ts
```

**Status:** These tests were added and committed, but a successful
Vitest run has not been confirmed in the work record.

## Reported dummy-data smoke test

During implementation, a disposable repository smoke test was reported
as successful by the implementation workflow, with one test secret
detected and a high-severity count of 1. The temporary repository was
reportedly cleaned afterward.

**Evidence qualification:** This is a reported result from the
implementation workflow, not an independently reproduced run in this
documentation session. Attach the original terminal output or rerun the
test before presenting it as independently verified evidence.

## Reproducible standalone dummy-data test

The command block in `02-setup-and-test-commands.md` creates a temporary
repository and commits the synthetic value:

``` text
AKIAIOSFODNN7EXAMPLE
```

This is a well-known example-shaped placeholder, not a usable
credential. The test checks whether Gitleaks detects it and writes
`report.sarif`. Save the complete output and the boolean result for
report-file existence.

Expected behavior when the rule matches:

-   a finding is reported;
-   the SARIF report exists;
-   the exit code is `1` because the command sets `--exit-code 1`.

Expected behavior is not the same as observed evidence. Fill in the
following after running the test:

  Field                       Actual result
  --------------------------- ---------------
  Date / machine              Not recorded
  `gitleaks version`          Not recorded
  Finding detected            Pending
  SARIF file created          Pending
  Exit code                   Pending
  Sanitized output saved at   Pending

## Git and change evidence

The following was confirmed from terminal output shared during the work:

-   Branch: `AdarshChoubey-Gitleaks`
-   Commit: `71f17a2`
-   Commit message: `feat(core): add Gitleaks scanner wrapper`
-   Commit included 2 files and 140 insertions.
-   Push succeeded to `origin/AdarshChoubey-Gitleaks`.
-   `git diff --cached --check` produced no output before commit.

## Still pending

-   Focused Vitest run.
-   TypeScript/typecheck or the project's configured checks.
-   Full ReleaseShield end-to-end run with `--tools gitleaks`.
-   Verification that the final combined ReleaseShield output contains
    the synthetic finding.
-   Capturing sanitized terminal output and, if useful, a redacted SARIF
    summary.

## Safe evidence practices

-   Never use actual API keys, passwords, tokens, or customer secrets in
    screenshots or public reports.
-   Do not upload the raw test file or report if it contains anything
    sensitive.
-   Show the command, Gitleaks version, detected finding count, SARIF
    file existence, and sanitized output instead.
-   If publishing to a personal portfolio, confirm permission to share
    project details and code.
