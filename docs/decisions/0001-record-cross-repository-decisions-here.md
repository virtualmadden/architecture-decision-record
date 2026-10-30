---
status: accepted
date: 2026-10-30
decision-makers: Jonathan Madden
---

# Record decisions that span repositories here, and the rest in each repository

## Context and Problem Statement

My projects have grown from isolated repositories into one system: an AWS organization, a DNS repository that every site depends on, sites deployed into their own accounts, and shared conventions for CI. Some decisions only make sense across several of those repositories, such as where DNS zones live or how CI reaches AWS. Each repository's README explains how that repository works, but nothing explains why the system is shaped the way it is.

Where should those decisions be written down?

## Decision Drivers

* The reasoning behind cross-repository choices should outlive the conversation it came from.
* A decision about one repository should be reviewed in the same pull request as the code it describes.
* Records have to stay current, so there should be one obvious place for each.

## Considered Options

* Cross-repository decisions here, and single-repository decisions in each repository's `docs/decisions/`
* Every decision here
* Every decision in the repository it most affects
* No records; READMEs only

## Decision Outcome

Chosen option: "Cross-repository decisions here, and single-repository decisions in each repository's `docs/decisions/`", because it is the only option that gives each decision exactly one obvious home while keeping local records next to their code.

A decision belongs here when it affects more than one repository, or sets a convention that every repository follows. Records use [MADR 4](https://adr.github.io/madr/), starting from [`adr-template.md`](adr-template.md).

### Consequences

* Good, because the system's shape can be explained without reading every repository.
* Good, because local decisions stay reviewable with the code they describe, and don't crowd this repository.
* Bad, because a pull request that changes a shared convention needs a second pull request here.
* Bad, because this repository is public, so records describe the reasoning and leave out identifiers such as account IDs, which live in the private organization repository.

### Confirmation

A pull request that changes a convention shared by several repositories links to the record it follows or updates.

## Pros and Cons of the Options

### Every decision here

* Good, because there is one place to look.
* Bad, because records drift away from the code they describe and aren't reviewed with it.

### Every decision in the repository it most affects

* Good, because records are reviewed with their code.
* Bad, because a decision such as "every domain's zone lives in shared services" has no single repository it belongs to.

### No records; READMEs only

* Good, because there is nothing extra to maintain.
* Bad, because READMEs explain the current state, not the alternatives that were rejected or why.
