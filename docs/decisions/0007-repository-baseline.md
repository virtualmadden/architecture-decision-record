---
status: accepted
date: 2026-10-29
decision-makers: Jonathan Madden
---

# A standard baseline for every active repository

## Context and Problem Statement

Repositories had drifted apart: actions referenced by mutable tags, dependency updates arriving as dozens of separate pull requests or not at all, branch protection rules from 2020 that a single maintainer couldn't satisfy, and merged branches left behind. Each repository was set up differently, so each one had to be understood separately.

What should every active repository have?

## Decision Drivers

* A compromised or retagged action shouldn't run in CI unnoticed.
* Dependencies should stay current with little effort.
* `main` should only change through a pull request whose checks pass.
* The rules have to work for a single maintainer.

## Considered Options

* Set up each repository as needed
* A standard baseline for every active repository

## Decision Outcome

Chosen option: "A standard baseline for every active repository", because the same small set of rules covers supply chain, updates and review everywhere, and makes every repository work the same way.

Every active repository has:

* **Pinned actions.** Every action is pinned to a full commit SHA, with its version in a comment.
* **Dependabot.** Weekly, with one grouped pull request per ecosystem. Any ignore rule has a comment saying why, and when to lift it.
* **A ruleset on `main`.** It requires a pull request and the repository's named checks, and blocks deletion and force pushes. It requires no approving review, since there is one maintainer.
* **Least-privilege workflows.** The default token is read-only, and jobs ask only for the permissions they use, such as an OIDC token ([0005](0005-deploy-from-github-actions-with-oidc.md)).
* **Tidy branches.** Merged branches are deleted automatically, and the default branch is `main`.
* **A private commit address.** Commits use the GitHub `noreply` address, never a personal email.

### Consequences

* Good, because a retagged or compromised action can't change what CI runs.
* Good, because updates arrive as one reviewable pull request per ecosystem, and a failing one points at a real compatibility problem.
* Bad, because grouped updates fail together: one incompatible package blocks the whole group until it is held back or fixed.
* Bad, because rulesets aren't available on private repositories on GitHub's free plan, so private repositories rely on legacy branch protection instead.

### Confirmation

A new or revived repository gets the baseline in its first pull request. Dependabot pull requests that keep failing are investigated, not left open.

## Pros and Cons of the Options

### Set up each repository as needed

* Good, because each repository gets only what it needs.
* Bad, because the gaps are invisible until they matter, and every repository has to be learned separately.
