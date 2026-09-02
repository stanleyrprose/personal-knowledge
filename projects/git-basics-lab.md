# Git Basics Lab

> Purpose: a small, safe repository exercise to demonstrate Git concepts using this repository.

## Epistemic Status

- **Fact:** This file was created on a dedicated Git branch rather than directly on `main`.
- **Fact:** Git records the change as a commit with a unique commit SHA.
- **Fact:** A Pull Request can expose the diff between this branch and `main` before merge.

## Experiment

The exercise walks through:

1. `main` as the current canonical branch.
2. Create `lab/git-basics-2026-09-02` from a known `main` commit.
3. Add this file and commit it on the lab branch.
4. Inspect the branch diff and commit history.
5. Open a Pull Request from the lab branch to `main`.
6. Merge the Pull Request.
7. Verify that `main` now contains the new commit/content.

## Mental Model

```text
main:        A
              \
lab branch:    B  <- add this file
                \
                 PR -> review diff -> merge -> main
```

**INTENTIONAL ERROR FOR REVERT LAB:** A Git branch creates a complete independent duplicate of every project file, so each branch is effectively a separate repository.

## Safety Boundary

This lab intentionally adds only this Markdown file. It does not modify PKS protocol files, existing knowledge nodes, credentials, secrets, or deployment configuration.
