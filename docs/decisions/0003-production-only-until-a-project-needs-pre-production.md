---
status: accepted
date: 2026-10-29
decision-makers: Jonathan Madden
---

# Production only, until a project needs pre-production

## Context and Problem Statement

With one account per project ([0002](0002-one-aws-account-per-project.md)), the first projects each got a `-dev` and a `-prod` account. For a personal site and a DNS repository, a dev environment doubles the infrastructure and deploys, has to be kept in step with production, and protects no users or data.

Should every project get a pre-production environment?

## Decision Drivers

* Changes should be checked before they reach production.
* Every environment has to be kept in step with production, or it stops being useful.
* Most projects here are static sites with no users' data to protect.

## Considered Options

* A dev account for every project
* Production only, with pull requests planned against production
* Production only, with no checks before merge

## Decision Outcome

Chosen option: "Production only, with pull requests planned against production", because it keeps the useful part of a dev environment, seeing exactly what a change will do, without a second environment to maintain.

Pull requests run the project's checks and a `terraform plan` against production through a read-only role ([0005](0005-deploy-from-github-actions-with-oidc.md)). Merging to `main` deploys. A project gets a `-dev` account in the `Development` organizational unit only when it needs a hosted pre-production environment, for example once it has users or data that a bad deploy could harm.

The `shared-services-dev` and `virtualmadden-dev-dev` accounts were closed when this was adopted ([virtualmadden-dev#18](https://github.com/virtualmadden/virtualmadden-dev/pull/18)).

### Consequences

* Good, because each project has one environment to keep current, and nothing drifts.
* Good, because every infrastructure change is reviewed as a plan against the real production state.
* Bad, because a change that plans cleanly but behaves badly is first exercised in production. For static sites behind CloudFront, the fix is a revert and a redeploy.

### Confirmation

Each production repository requires its plan check to pass before merge, and the organization repository has no `-dev` account without a stated need.

## Pros and Cons of the Options

### A dev account for every project

* Good, because changes run somewhere real before production.
* Bad, because it doubles accounts and deploys, and an unused environment drifts until it proves nothing.

### Production only, with no checks before merge

* Good, because it is the least work.
* Bad, because infrastructure changes would only be seen when they apply.
