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

## 5. Output

A test repository containing dummy credentials was created outside the team repository. Gitleaks scanned the commit history and reported each finding with its rule ID, file, line number, commit and author.

Screenshot of the scan output:
<img width="1367" height="751" alt="Screenshot 2026-10-23 154404" src="https://github.com/user-attachments/assets/7c58184f-509c-4020-aeff-750f69077689" />

The first test used a token with a predictable pattern, and Gitleaks did not flag it. A second test used a token with a random-looking value, and Gitleaks detected it. This shows that Gitleaks evaluates the randomness of a value and not only its format.

## 6. SARIF Structure

_To be documented._

## 7. Runner Code

_To be documented._

## 8. Issues and Resolutions

_To be documented._

## 9. Limitations

_To be documented._

## Status

- [ ] Installation
- [ ] Scan executed
- [ ] SARIF output generated
- [ ] Runner implemented
- [ ] Unit tests written
