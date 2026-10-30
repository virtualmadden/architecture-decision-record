---
status: accepted
date: 2026-10-30
decision-makers: Jonathan Madden
---

# Retire repositories by archiving them, or deleting them if they hold nothing

## Context and Problem Statement

Several repositories had gone dormant for years, collecting Dependabot pull requests and security alerts for code nobody runs. A dormant repository still looks maintained from the outside, and it is unclear whether its code is safe to use.

What should happen to a repository that is no longer used?

## Decision Drivers

* A repository's state should be obvious to anyone who finds it.
* Work worth reading should stay available.
* Dormant repositories shouldn't generate maintenance noise.

## Considered Options

* Leave dormant repositories as they are
* Archive every dormant repository
* Archive repositories with something worth keeping, and delete empty ones

## Decision Outcome

Chosen option: "Archive repositories with something worth keeping, and delete empty ones", because archiving keeps useful work readable while making its state clear, and an empty repository has nothing to keep.

Before archiving, a repository gets a README note saying it is archived, and why, with a link to whatever replaced it. When the idea is still useful but the code is not, the repository may be rebuilt as a small current example first, then archived. A repository with no real content is deleted.

Examples:

* `initialize-terraform-s3-state` was archived, with a note pointing to the account baseline that replaced it ([0002](0002-one-aws-account-per-project.md)).
* `new-relic-custom-events` was rebuilt as [dotnet-otel-custom-events](https://github.com/virtualmadden/dotnet-otel-custom-events), an OpenTelemetry example, then archived.
* `terraform-aws-acm-certificate-module`, which held only a README, was deleted ([0008](0008-share-infrastructure-as-template-repositories.md)).

### Consequences

* Good, because archived repositories are read-only and clearly marked, and stop generating Dependabot pull requests.
* Good, because the profile shows only maintained work and labeled history.
* Bad, because deleting is permanent, so it is reserved for repositories with nothing in them.

### Confirmation

Every repository is either maintained under the baseline ([0007](0007-repository-baseline.md)) or archived with a note.

## Pros and Cons of the Options

### Leave dormant repositories as they are

* Good, because it takes no effort.
* Bad, because they look maintained, keep collecting alerts, and their state is unclear.

### Archive every dormant repository

* Good, because nothing is ever lost.
* Bad, because empty repositories stay visible with nothing to show.
