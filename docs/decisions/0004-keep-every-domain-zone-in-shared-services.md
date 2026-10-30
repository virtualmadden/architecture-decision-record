---
status: accepted
date: 2026-10-29
decision-makers: Jonathan Madden
---

# Keep every registered domain's zone in shared services

## Context and Problem Statement

Domains are registered at Porkbun, which points each one at a Route53 hosted zone's nameservers. A new zone gets new nameservers, so if a domain's zone lives in a project account and that account is closed or the zone is recreated, the registrar has to be updated or the domain stops resolving. Projects come and go more often than domains do. This happened: `virtualmadden.dev` stopped resolving because the account holding its zone was retired, while the registrar still pointed at that zone's nameservers.

Which account should own each domain's hosted zone?

## Decision Drivers

* A domain's nameservers at the registrar should never have to change.
* Projects should be able to come and go without touching the registrar.
* Each record should have exactly one owner.

## Considered Options

* Each domain's zone in the account of the project that uses it
* Every domain's zone in shared services, managed by one repository

## Decision Outcome

Chosen option: "Every domain's zone in shared services, managed by one repository", because a zone that is never recreated keeps the same nameservers, so the registrar is set once.

[dns-configuration](https://github.com/virtualmadden/dns-configuration) runs in the shared services account. It creates each domain's zone, manages its records, and sets its nameservers at Porkbun. Projects go live by pull request to it:

* A project serving a domain's apex or `www` gets alias records to its CloudFront distribution or load balancer, plus its certificate's validation records, in the shared zone. Alias records work across accounts.
* A project serving a subdomain can own that subdomain's zone in its own account, delegated with NS records from the shared zone.
* A project that must own a whole domain's zone can still do so, as a listed exception.

### Consequences

* Good, because closing or rebuilding a project account never changes a domain's nameservers.
* Good, because every domain's records can be read in one place.
* Bad, because a project's DNS changes go through a second repository, in another account.
* Bad, because a project's Terraform can't create its own records in the shared zone, so its outputs, such as its CloudFront domain, are copied into the DNS repository by hand.

### Confirmation

Every registered domain appears in dns-configuration, either as a shared zone or as a listed exception.

## Pros and Cons of the Options

### Each domain's zone in the account of the project that uses it

* Good, because a project owns everything it needs and can change its records in one apply.
* Bad, because recreating the project's zone or account changes the domain's nameservers and takes it offline until the registrar is updated.

## More Information

Introduced in [dns-configuration#1](https://github.com/virtualmadden/dns-configuration/pull/1) and [#2](https://github.com/virtualmadden/dns-configuration/pull/2). Domains that don't send mail also publish records refusing it ([0006](0006-domains-refuse-mail-unless-they-send-it.md)).
