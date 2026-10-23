# Gitleaks
Project: ReleaseShield AI | Team Algorithm | LNCT Bhopal
Role: Secret detection runner (Sprint 1)

## 1. Overview

Gitleaks is an open-source tool that scans source code and git history for
hardcoded secrets such as passwords, API keys and access tokens.

## 2. Role in ReleaseShield AI

Within ReleaseShield AI, Gitleaks provides the secret detection layer. In
Sprint 1, a runner module will execute Gitleaks on a target repository,
export the findings as SARIF v2.1.0, and report a status ("ok",
"issues_found" or "failed") together with duration and error details.

## 3. Installation

Gitleaks was installed on Windows using the Windows Package Manager (winget):

powershell
winget install gitleaks.gitleaks
Found an existing package already installed. Trying to upgrade the installed package...
No available upgrade found.
No newer package versions are available from the configured sources.

The installation was verified by checking the installed version:
powershell
gitleaks version
8.30.1
Figure 1: Gitleaks version output confirming a successful installation.
<img width="920" height="205" alt="Screenshot 2026-10-23 152553" src="https://github.com/user-attachments/assets/9a281942-8418-4859-9e8a-8be7de1280da" />

## 4. Commands

Verify the installation:

gitleaks version
8.30.1
Scan the repository and its git history with verbose output:

    ○
    │╲
    │ ○
    ○ ░
    ░    gitleaks

Finding:     GITHUB_TOKEN = "ghp_x7Kq9LmZ2vB4nR8tY1cW6eH3jD5fG0sA9pUo
Secret:      ghp_x7Kq9LmZ2vB4nR8tY1cW6eH3jD5fG0sA9pUo
RuleID:      github-pat
Entropy:     5.221928
File:        secrets.py
Line:        1
Commit:      3957d6ddcc469e0133ab10f973c293b6da785d30
Author:      Adarsh-Choubey
Email:       your-email@example.com
Date:        2026-10-23T10:12:34Z
Fingerprint: 3957d6ddcc469e0133ab10f973c293b6da785d30:secrets.py:github-pat:1

3:47PM INF 2 commits scanned.
3:47PM INF scanned ~159 bytes (159 bytes) in 211ms
3:47PM WRN leaks found: 1

The --source option sets the folder to scan, and the -v option prints the details of each finding.
Scan with secret redaction:

Gitleaks exits with code 0 when no secrets are found and with code 1 when at least one secret is found. An exit code of 1 therefore indicates findings, not a tool failure.

## 5. Output

A test repository containing dummy credentials was created outside the team repository. Gitleaks scanned the commit history and reported each finding with its rule ID, file, line number, commit and author.

Screenshot of the scan output:
<img width="1367" height="751" alt="Screenshot 2026-10-23 154404" src="https://github.com/user-attachments/assets/7c58184f-509c-4020-aeff-750f69077689" />

The first test used a token with a predictable pattern, and Gitleaks did not flag it. A second test used a token with a random-looking value, and Gitleaks detected it. This shows that Gitleaks evaluates the randomness of a value and not only its format.

## 6. SARIF Structure

Gitleaks exports its findings in SARIF v2.1.0 using the following command:

    ○
    │╲
    │ ○
    ○ ░
    ░    gitleaks

3:56PM INF 3 commits scanned.
3:56PM INF scanned ~159 bytes (159 bytes) in 264ms
3:56PM WRN leaks found: 1

The output file contains a list called runs, with one entry per tool. Each run has two main parts. The tool section describes the scanner and lists all of its rules. The results section lists the findings. A shortened version of the output is shown below.
{
  "version": "2.1.0",
  "runs": [
    {
      "tool": {
        "driver": {
          "name": "gitleaks",
          "rules": [ ... ]
        }
      },
      "results": [
        {
          "ruleId": "github-pat",
          "message": {
            "text": "github-pat has detected secret for file secrets.py at commit 3957d6d..."
          },
          "locations": [
            {
              "physicalLocation": {
                "artifactLocation": { "uri": "secrets.py" },
                "region": { "startLine": 1, "startColumn": 17, "endLine": 1, "endColumn": 56 }
              }
            }
          ],
          "partialFingerprints": {
            "commitSha": "3957d6d...",
            "author": "Adarsh-Choubey",
            "commitMessage": "Add secrets file with dummy keys"
          }
        }
      ]
    }
  ]
}
The main fields used by ReleaseShield AI are listed below.
ruleId identifies the detection rule that matched.
locations gives the file path, line and column of the finding.
partialFingerprints contains the commit, author and date of the finding.
message describes the finding in plain text.

The rule list under tool.driver.rules contains every rule available in Gitleaks, not only the rules that matched. Findings are found only under results.

## 7. Runner Code



## 8. Issues and Resolutions

_To be documented._

## 9. Limitations

By default, the SARIF output includes the detected secret in the snippet field. The --redact option should be used so that secrets are not stored or forwarded in clear text.

The fingerprint information provided by Gitleaks includes the commit identifier and date. These values change between commits, so ReleaseShield AI will need its own stable fingerprint for deduplication.

Detection depends on both the format and the randomness of a value. A token with a predictable pattern was not detected in testing, while a random-looking token was detected.

## Status

- [ ] Installation
- [ ] Scan executed
- [ ] SARIF output generated
- [ ] Runner implemented
- [ ] Unit tests written
