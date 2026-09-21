# Lab 3 - Secure Git

## Task 1 

- gpg.format - ssh;
- user.signingkey - ~/.ssh/id_ed25519.pub
- commit.gpgsign - true


commit 1085b6e8e3816cf5c57b0c6ac5c71b5860df4950 (HEAD -> feature/lab3)
Good "git" signature for os6844223@gmail.com with ED25519 key SHA256:REDACTED
Author: coilgung <os6844223@gmail.com>
Date:   Mon Sep 21 08:37:19 2026 +0300

    test: first signed commit


verified commit link: https://github.com/coilgung/DevSecOps-Intro/commit/1085b6e8e3816cf5c57b0c6ac5c71b5860df4950


With the forged author line I can deny it was me who, for instance, leaked secrets in a commit. If the commits are signed, they are linked to the key and to the github account, creating NON-REPUDIATION, which is a much better word than just repudiation. Repudiation means plain denial, but non-repudiation means an assurance that the action cannot later be denied by parties involved. One means refusal, which is too broad, the other means enforcement of accountability, and that's so much more concise! And so in the stride it should be named Lack of Non-repudiation.


## Task 2

.pre-commit-config.yaml:
```yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.1
    hooks:
      - id: gitleaks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: detect-private-key
      - id: check-added-large-files
```
outputs:
```shell
git commit -m "test: should be blocked"
Detect hardcoded secrets.................................................Failed
- hook id: gitleaks
- exit code: 1

○
    │╲
    │ ○
    ○ ░
    ░    gitleaks

Finding:     printf 'GH_PAT=REDACTED\n'
Secret:      REDACTED
RuleID:      github-pat
Entropy:     4.143943
File:        submissions/leak-attempt.txt
Line:        1
Fingerprint: submissions/leak-attempt.txt:github-pat:1

8:54AM INF 0 commits scanned.
8:54AM INF scanned ~58 bytes (58 bytes) in 35.8ms
8:54AM WRN leaks found: 1

detect private key.......................................................Passed
check for added large files..............................................Passed
```
still first commit:
```shell
git log --oneline -1
1085b6e (HEAD -> feature/lab3, origin/feature/lab3) test: first signed commit
```

An allowlist entry in `.gitleaks.toml` should match only a reviewed, deliberately fake example as narrowly as possible. It becomes unsafe if a broad expression also matches usable credentials or if an allowlisted value is later used as a real credential; an `AKIA` prefix alone is not a safe exception.

Excluding all of `docs/` suppresses scanning for any credential accidentally placed there, including copied commands, logs, and configuration examples. It becomes unsafe as soon as real secrets can enter that directory; narrowly allowlisting a known fake example retains more protection for other content.
