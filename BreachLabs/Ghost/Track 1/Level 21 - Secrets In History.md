# BreachLabs: Ghost Track Level 21 - Secrets in History

**Challenge:** Level 21 → 22  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Secrets in History introduces git repository forensics and secret scanning. The challenge requires examining git history across all branches and tags to find sensitive credentials that were removed from the main branch but remain in tagged release candidates. Success demonstrates understanding of git log, git tags, and the persistence of secrets in version control history.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost22` user  
**Context:** A deploy repo with a tagged release candidate containing a hardcoded deploy key  
**Constraint:** Must search git history including tags; main branch is clean but RC carries the secret

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 20:
```
Target: 204.168.229.209:2222
User: ghost21
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- A deploy repo pulled off an internal server
- Main is clean now, but a tagged release candidate still carries the deploy key it once hardcoded
- Releases are tagged; RC builds are never shipped

---

## Repository Exploration

### Home Directory Contents

Listed home directory to locate repository:

```bash
ls -alps
```

**Directory Contents:**
```
8 drwxrwxr-x 3 ghost21 ghost21 4096 Aug 17 06:36 repo/
```

**Finding:** Located `repo` directory in home.

### Repository Navigation

Changed to repository directory and examined contents:

```bash
cd repo
ls -alps
```

**Repository Contents:**
```
8 -rw-r--r-- 1 ghost21 ghost21   76 Jun 22 13:41 .env.example
8 drwxr-xr-x 1 ghost21 ghost21 4096 Aug 31 11:44 .git/
8 -rw-r--r-- 1 ghost21 ghost21   11 Jun 22 13:41 .gitignore
8 -rw-r--r-- 1 ghost21 ghost21  135 Jun 22 13:41 README.md
```

**Finding:** Git repository present with .env.example but no active .env file.

### README Examination

Read repository documentation:

```bash
cat README.md
```

**README Content:**
```
# Ghost Deploy

Config lives in .env (untracked; see .env.example).
Releases are tagged — `git tag -l`. RC builds are never shipped.
```

**Finding:** Documentation confirms .env is untracked and release candidates are tagged but never shipped.

---

## Git History Analysis

### Complete History Review

Examined all git commits including tags:

```bash
git log --all
```

**Git Log Output:**
```
commit 2b796476c228c3d9aefd8c8d88814c2ae350dfc1 (HEAD -> main)
Author: KAEL <kael@ghost>
Date:   Mon Jun 22 13:41:54 2026 +0000

    docs: deploy + release notes

commit 3e1c99f798f87e9d860eecbd474b72bd079f7a4d (tag: v1.4.0-rc1)
Author: KAEL <kael@ghost>
Date:   Mon Jun 22 13:41:54 2026 +0000

    rc: hardcode deploy key so CI can ship 1.4.0

commit 580f36d13e257458299c7a569a9d8702bd6fbb66
Author: KAEL <kael@ghost>
Date:   Mon Jun 22 13:41:54 2026 +0000

    chore: deploy config template + ignore .env
```

**Finding:** Identified commit tagged as `v1.4.0-rc1` with message "rc: hardcode deploy key so CI can ship 1.4.0". This is the RC build mentioned in challenge.

### Tagged Commit Examination

Displayed contents of the RC tag commit:

```bash
git show 3e1c99f798f87e9d860eecbd474b72bd079f7a4d
```

**Commit Details:**
```
commit 3e1c99f798f87e9d860eecbd474b72bd079f7a4d (tag: v1.4.0-rc1)
Author: KAEL <kael@ghost>
Date:   Mon Jun 22 13:41:54 2026 +0000

    rc: hardcode deploy key so CI can ship 1.4.0

diff --git a/.env b/.env
new file mode 100644
index 0000000..773f8dd
--- /dev/null
+++ b/.env
@@ -0,0 +1,3 @@
+GHOST_DEPLOY_KEY=[REDACTED_FLAG]
+REGION=eu-1
+DEBUG=true
```

**Finding:** RC commit added .env file with hardcoded deploy key: `[REDACTED_FLAG]`.

---

## Secret Recovery

### Credential Extraction

The RC commit shows the following configuration:
```
GHOST_DEPLOY_KEY=[REDACTED_FLAG]
REGION=eu-1
DEBUG=true
```

**Finding:** Successfully extracted `ghost22` password from git history: `[REDACTED_FLAG]`.

---

## Attack Chain Summary

```
SSH Connection (ghost21, password auth)
         ↓
Navigate to ~/repo directory
         ↓
Challenge Context (deploy repo with tagged RC)
         ↓
Repository Exploration (locate .git)
         ↓
README Review (confirm untracked .env and tagged releases)
         ↓
Git History Examination (git log --all)
         ↓
RC Tag Identification (v1.4.0-rc1)
         ↓
Commit Show (git show with commit hash)
         ↓
Diff Analysis (examine .env addition)
         ↓
Secret Discovery (GHOST_DEPLOY_KEY=)
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Git Log** — `git log --all` shows all commits across all branches and tags
2. **Git Tags** — Tagged commits persist in history even if not on main branch
3. **Release Candidates** — RC tags mark pre-release versions in version control
4. **Git Show** — Displays commit content including diffs
5. **Version Control History** — Deleted files/changes remain accessible in commit history
6. **Hardcoded Secrets** — Configuration with secrets committed to RC builds
7. **Untracked Files** — .gitignore specifies .env as untracked, but RC had it committed
8. **Secret Scanning** — History must be searched to find credentials in past commits

---

## Tools & Commands Used

```bash
# Navigate to repository
cd repo

# List repository contents
ls -alps

# Read repository documentation
cat README.md

# View all git history including tags
git log --all

# Display specific commit contents
git show 3e1c99f798f87e9d860eecbd474b72bd079f7a4d
```

---

## Lessons Learned

1. **Version Control Exposure** — Secrets in RC tags remain accessible even if removed from main
2. **Commit History Forensics** — All commits accessible via git log, including tagged versions
3. **RC Build Risks** — Release candidates often contain debugging code and hardcoded secrets
4. **Git Tag Persistence** — Tags reference commits that may not be on current branch
5. **Configuration Management** — .env files should never be committed, even in RC branches
6. **Secret Removal** — Simply removing secrets from main branch does not eliminate them from history
7. **Repository Access** — Access to .git directory allows full historical reconstruction

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Git Log: https://git-scm.com/docs/git-log
- Git Tag: https://git-scm.com/docs/git-tag
- Git Reflog: https://git-scm.com/docs/git-reflog
- GitHub Secret Scanning: https://docs.github.com/en/code-security/secret-scanning/about-secret-scanning

---

**Status:** ✅ Completed  
**Challenge Type:** Git Repository Forensics & Secret Discovery  
**Key Learning:** Examining version control history across tags to recover hardcoded credentials in release candidate builds
