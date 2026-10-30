---
status: accepted
date: 2026-10-29
decision-makers: Jonathan Madden
---

# One AWS account per project, in an AWS Organization

## Context and Problem Statement

Projects and shared resources such as Terraform state used to live in one standalone AWS account, reached with long-lived access keys. Every project could reach every other project's resources, the bill couldn't be split by project, and closing a project meant hunting down its resources by hand.

How should AWS accounts be divided between projects?

## Decision Drivers

* A mistake or compromise in one project shouldn't reach another.
* No long-lived credentials, for people or for CI.
* Adding or removing a project should be a small, repeatable change.
* Cost and quotas should be visible per project.

## Considered Options

* One shared account for everything
* One account per project, under AWS Organizations
* One account per project and environment, under AWS Organizations

## Decision Outcome

Chosen option: "One account per project, under AWS Organizations", because the account boundary gives each project its own IAM, quotas, bill and blast radius at no cost, without paying for environments that projects don't use (see [0003](0003-production-only-until-a-project-needs-pre-production.md)).

The organization lives in a private `aws-organization` repository:

* The management account holds the organization, billing and IAM Identity Center. It is applied locally with single sign-on and has no CI, so nothing holds standing credentials to the organization root.
* Accounts are grouped into `shared-services`, `Development` and `Production` organizational units. Shared services holds what every project depends on, such as DNS ([0004](0004-keep-every-domain-zone-in-shared-services.md)).
* People sign in through IAM Identity Center. Member accounts have no root credentials, and a service control policy stops them from creating their own Identity Center instances.
* A per-account baseline gives each account its own Terraform state bucket, an account-wide S3 public access block, and the GitHub roles its repository deploys with ([0005](0005-deploy-from-github-actions-with-oidc.md)).

### Consequences

* Good, because a project can only change resources in its own account.
* Good, because adding a project is a few lines in the organization repository plus a baseline apply, and closing one closes its account.
* Good, because Terraform state is separate per account, with S3 native locking instead of DynamoDB.
* Bad, because shared resources such as DNS records cross an account boundary, and change through another repository.
* Bad, because a closed account stays suspended for 90 days and counts toward the organization's account quota until AWS deletes it.

### Confirmation

Each project's CI can only assume roles in its own account, and its state bucket is the one created by the baseline for that account.

## Pros and Cons of the Options

### One shared account for everything

* Good, because there is nothing to set up.
* Bad, because every project can reach every other project, and cost can't be split.

### One account per project and environment

* Good, because pre-production is isolated from production.
* Bad, because it doubles the accounts, baselines and deploys for projects that have no pre-production users or data.
