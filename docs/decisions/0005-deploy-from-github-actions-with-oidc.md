---
status: accepted
date: 2026-10-29
decision-makers: Jonathan Madden
---

# Deploy from GitHub Actions with OIDC, through separate plan and deploy roles

## Context and Problem Statement

CI deployed to AWS with long-lived access keys stored as repository secrets. Anyone who could read or exfiltrate those secrets could change production, the keys never expired, and pull request workflows could use them as easily as `main` could.

How should GitHub Actions authenticate to AWS?

## Decision Drivers

* No long-lived AWS credentials anywhere.
* Only `main` can change production.
* Pull requests should see a real plan against production without being able to change it.

## Considered Options

* Access keys in repository secrets
* GitHub OIDC with one role per account
* GitHub OIDC with a deploy role for `main` and a read-only plan role for pull requests

## Decision Outcome

Chosen option: "GitHub OIDC with a deploy role for `main` and a read-only plan role for pull requests", because it is the only option where a pull request can plan against production but can't change it.

Each account's baseline ([0002](0002-one-aws-account-per-project.md)) creates two roles that trust only the project's repository:

* **Deploy** trusts only workflow runs on `main` in production accounts. It has administrator access inside its own account; the account boundary is the isolation.
* **Plan** trusts only the repository's pull requests and has `ReadOnlyAccess`. Plans run with `-lock=false`, since a read-only role can't take the state lock.

Workflows request an OIDC token only in the jobs that assume a role, and the default workflow token is read-only. Repositories store role ARNs, never keys.

### Consequences

* Good, because credentials last one job and can't be reused outside GitHub Actions.
* Good, because a compromised pull request can at most read production, not change it.
* Neutral, because pull requests from forks and from Dependabot get no OIDC token, so they run checks but skip the plan.
* Bad, because the deploy role's administrator access relies on the account boundary. The [aws-react-terraform-spa](https://github.com/virtualmadden/aws-react-terraform-spa) template can't assume a dedicated account, so its deploy role gets a policy scoped to the resources it manages instead ([0008](0008-share-infrastructure-as-template-repositories.md)).

### Confirmation

Repositories have no AWS keys among their secrets, and each production repository requires its plan check before merge.

## Pros and Cons of the Options

### Access keys in repository secrets

* Good, because it works anywhere with no setup in AWS.
* Bad, because the keys never expire and any workflow, on any branch, can use them.

### GitHub OIDC with one role per account

* Good, because there are no long-lived keys.
* Bad, because pull requests either can't plan against production or can change it.
