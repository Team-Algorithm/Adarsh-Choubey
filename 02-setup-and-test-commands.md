# 2. Setup and Test Commands

These instructions target Windows PowerShell. Run commands from the
indicated directory. A path containing spaces should be enclosed in
double quotes.

## A. Install and verify Gitleaks

If the package is available through winget on your machine:

``` powershell
winget install --id Gitleaks.Gitleaks -e
```

Close and reopen PowerShell, then verify:

``` powershell
gitleaks version
```

The implementation environment previously reported Gitleaks `8.30.1`;
another machine may show a different version. If winget cannot find the
package, use the official Gitleaks releases page:
https://github.com/gitleaks/gitleaks/releases

Also verify prerequisites:

``` powershell
git --version
node --version
npm --version
```

## B. Clone the project and check out the integration branch

``` powershell
git clone https://github.com/Team-Algorithm/ReleaseSheild_EPAM-Project.git
Set-Location "ReleaseSheild_EPAM-Project"
git fetch origin
git checkout AdarshChoubey-Gitleaks
git pull origin AdarshChoubey-Gitleaks
```

If the repository already exists locally, use `Set-Location` with the
actual path instead of cloning again. Example:

``` powershell
Set-Location "D:\New folder\ReleaseSheild_EPAM-Project"
```

## C. Install project dependencies

``` powershell
Set-Location "cli_frontend"
npm ci
```

`npm ci` uses the lockfile. It may take time and requires network
access. Do not run it if you are intentionally doing a read-only review
or cannot install dependencies.

## D. Run the focused Gitleaks unit test

From `cli_frontend`:

``` powershell
npx vitest run packages/core/test/gitleaks.test.ts
```

A passing result is expected only if the test runner and dependencies
install correctly. This test has not been independently confirmed as
passing in the work record.

## E. Run the standalone Gitleaks smoke test on synthetic data

This creates a disposable repository, commits a synthetic example secret
so a Git-history scan can see it, runs Gitleaks, and checks whether a
SARIF file was created. Do not put real credentials in this test.

``` powershell
$testRepo = Join-Path $env:TEMP "gitleaks-smoke-test"
New-Item -ItemType Directory -Force -Path $testRepo | Out-Null
Set-Location $testRepo

git init
Set-Content -Path "test-secret.txt" -Value 'aws_access_key_id = "AKIAIOSFODNN7EXAMPLE"'
git add test-secret.txt
git -c user.name="Gitleaks Test" -c user.email="gitleaks-test@example.com" commit -m "Add fake test secret"

gitleaks detect --source $testRepo --report-format sarif --report-path "$testRepo\report.sarif" --exit-code 1

Write-Host "GITLEAKS_EXIT_CODE=$LASTEXITCODE"
Write-Host "SARIF_REPORT_EXISTS=$(Test-Path "$testRepo\report.sarif")"
```

Expected behavior for this test: Gitleaks reports a finding, the exit
code is `1` due to `--exit-code 1`, and `SARIF_REPORT_EXISTS=True`. If
the test behaves differently, save the full output and investigate
rather than marking it as passed.

## F. Inspect project scripts and CLI help before running a full scan

From `cli_frontend`:

``` powershell
npm run
```

This lists scripts actually declared by the project. Use the script
documented by the repository, then inspect its help output if supported.
Do not assume an unverified script name or CLI argument order.

The integration is intended to support selecting Gitleaks with
`--tools gitleaks`, but the exact complete CLI command must be verified
from the current CLI help/source and documented before this runbook is
considered final.

## G. Check working-tree hygiene

From the repository root:

``` powershell
git status
git diff --check
```

For a staged review:

``` powershell
git diff --cached --check
git diff --cached --stat
```

## Troubleshooting

-   **`gitleaks` is not recognized:** reopen the terminal and verify the
    installation directory is on `PATH`.
-   **Vitest not found:** run `npm ci` from `cli_frontend`.
-   **No dummy secret detected:** verify that the test file was
    committed into the temporary Git repository and inspect Gitleaks
    output/version.
-   **No SARIF report:** verify that the output path is writable and
    inspect the command error.
-   **Full CLI invocation unclear:** use `npm run`, CLI help, and the
    current README/source; do not invent the command.
