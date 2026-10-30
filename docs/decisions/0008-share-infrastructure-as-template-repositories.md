---
status: accepted
date: 2026-10-30
decision-makers: Jonathan Madden
---

# Share infrastructure patterns as template repositories, not modules

## Context and Problem Statement

Hosting a single-page app on AWS takes the same pieces every time: a private bucket, CloudFront, a certificate, DNS records, and a CI pipeline to deploy them. A 2020 repository meant to hold a shared Terraform module for the certificate was never written, and an old React and Terraform starter had fallen years behind.

How should reusable infrastructure be shared between projects?

## Decision Drivers

* Starting a new project should take minutes, with CI and deploys included, not only Terraform.
* There are only a few consumers, all maintained by one person.
* Anything shared has to stay maintained, or it becomes a liability.

## Considered Options

* Published Terraform modules, referenced by version
* Template repositories, copied once per project
* Copying from existing projects by hand

## Decision Outcome

Chosen option: "Template repositories, copied once per project", because a template delivers a complete working repository, including CI, and with only a few consumers the versioning and release work a module needs would cost more than it saves.

[aws-react-terraform-spa](https://github.com/virtualmadden/aws-react-terraform-spa) is the template for single-page apps ([#12](https://github.com/virtualmadden/aws-react-terraform-spa/pull/12)). It is self-contained: its own bootstrap creates a state bucket, GitHub's OIDC provider and scoped plan and deploy roles, so it works in any AWS account, inside this organization or not. Small, single-resource patterns such as a certificate are written inline. If a project needs a real module, a maintained community module such as [terraform-aws-modules](https://github.com/terraform-aws-modules) comes before writing one. The unwritten certificate module repository was deleted.

### Consequences

* Good, because a new project starts as a working, deployable repository.
* Good, because each project can change its copy freely, with nothing to stay compatible with.
* Bad, because fixes to the template don't reach projects already created from it. Each copy keeps itself current through its own Dependabot updates ([0007](0007-repository-baseline.md)).

### Confirmation

New projects of a templated kind start from the template, and there are no single-resource module repositories.

## Pros and Cons of the Options

### Published Terraform modules, referenced by version

* Good, because a fix reaches every consumer that upgrades.
* Bad, because modules need versioning, releases and compatibility, and they cover only the Terraform, not the app or CI.

### Copying from existing projects by hand

* Good, because there is nothing to maintain.
* Bad, because each copy carries over the source project's specifics and mistakes.
