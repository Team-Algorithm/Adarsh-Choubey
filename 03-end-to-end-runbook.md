# 3. End-to-End Runbook --- ReleaseShield AI with Gitleaks

## Goal

Run the project from a clean checkout, verify the Gitleaks executable,
run the focused wrapper test, and then run ReleaseShield using Gitleaks
once the repository's exact CLI invocation has been confirmed.

## Step 1 --- Verify prerequisites

``` powershell
git --version
node --version
npm --version
gitleaks version
```

All four commands should print version information. Install any missing
prerequisite before continuing.

## Step 2 --- Get the integration branch

``` powershell
git clone https://github.com/Team-Algorithm/ReleaseSheild_EPAM-Project.git
Set-Location "ReleaseSheild_EPAM-Project"
git checkout AdarshChoubey-Gitleaks
git pull origin AdarshChoubey-Gitleaks
```

If you already have a checkout, change to that repository instead of
cloning again.

## Step 3 --- Install JavaScript dependencies

``` powershell
Set-Location "cli_frontend"
npm ci
```

## Step 4 --- Run the focused unit test

``` powershell
npx vitest run packages/core/test/gitleaks.test.ts
```

Save the full output. Do not mark this step passed if it fails or cannot
start.

## Step 5 --- Run the standalone Gitleaks smoke test

Use the synthetic-data block in `02-setup-and-test-commands.md`. It
verifies Gitleaks itself and SARIF file creation; it does not prove that
ReleaseShield invokes or parses Gitleaks correctly.

## Step 6 --- Find the exact ReleaseShield CLI invocation

From `cli_frontend`:

``` powershell
npm run
```

Review the available scripts and the repository README/CLI help. The
integration branch is intended to support the `--tools gitleaks` option.
However, the exact full command (including the entry script, target
path, and any required flags) was not captured in the work evidence
available for this document, so it is intentionally not fabricated here.

Before running the scan, record:

-   The exact npm script or executable used to launch the CLI.
-   The exact syntax for selecting the target repository.
-   The exact syntax for `--tools gitleaks`.
-   The expected report/output directory.
-   Whether the command needs to be run from `cli_frontend` or the
    repository root.

Add the verified command below after confirming it from current CLI
help/source:

``` powershell
# Replace this comment with the exact verified ReleaseShield command.
# It must target a disposable/test repository and select --tools gitleaks.
```

## Step 7 --- Verify end-to-end output

Use a disposable test repository containing a synthetic secret, not a
real credential. Confirm all of the following:

1.  ReleaseShield accepts the Gitleaks selection.
2.  The Gitleaks runner is invoked.
3.  A SARIF report is generated at the configured output path.
4.  The scan pipeline reads the report without a parse error.
5.  The final ReleaseShield output includes the expected finding.
6.  Exit status and severity/counts match the project's documented
    conventions.

Capture the terminal output and sanitized report summary. Avoid
committing the raw test secret or generated reports unless the
repository specifically requires them.

## Step 8 --- Record the result honestly

Use a small verification table in your notes:

  -----------------------------------------------------------------------
  Check                   Status                  Evidence
  ----------------------- ----------------------- -----------------------
  Gitleaks installed      Pending / Pass / Fail   `gitleaks version`
                                                  output

  Focused unit test       Pending / Pass / Fail   Vitest output

  Standalone dummy scan   Pending / Pass / Fail   Gitleaks output + SARIF
                                                  existence

  ReleaseShield           Pending / Pass / Fail   exact command +
  end-to-end                                      sanitized output
  -----------------------------------------------------------------------

Do not call the integration end-to-end tested until Step 7 has been
completed.
