---
status: accepted
date: 2026-10-29
decision-makers: Jonathan Madden
---

# Domains refuse mail unless they send it

## Context and Problem Statement

None of the registered domains send or receive mail. A domain with no mail records can still be used as the sender of spoofed or phishing mail, and receivers have nothing to check it against.

What mail records should a domain that doesn't use mail publish?

## Decision Drivers

* Nobody should be able to send mail that passes as one of these domains.
* Protection shouldn't depend on remembering to add it for each new domain.
* A domain that starts using mail should be able to opt out cleanly.

## Considered Options

* No mail records
* Mail-refusing records added by hand where remembered
* Mail-refusing records on every domain by default, with an opt-out list

## Decision Outcome

Chosen option: "Mail-refusing records on every domain by default, with an opt-out list", because it protects every domain from the moment its zone exists.

[dns-configuration](https://github.com/virtualmadden/dns-configuration) ([0004](0004-keep-every-domain-zone-in-shared-services.md)) publishes three records on every domain not listed in `mail_domains`:

* A null MX record (`0 .`, [RFC 7505](https://www.rfc-editor.org/rfc/rfc7505.html)): the domain accepts no mail.
* SPF `v=spf1 -all`: no server may send mail as the domain.
* DMARC `p=reject`: receivers should reject mail that claims to come from it.

A domain that needs mail is added to `mail_domains`, and gets its own MX, SPF and DMARC records instead.

### Consequences

* Good, because every new domain is protected without any extra step.
* Good, because receivers can reject spoofed mail outright, instead of guessing.
* Bad, because turning on mail, even simple forwarding, needs a pull request that opts the domain out first, or its records clash.

### Confirmation

Every domain not in `mail_domains` resolves a null MX, `v=spf1 -all` and a `p=reject` DMARC record.

## Pros and Cons of the Options

### No mail records

* Good, because there is nothing to maintain.
* Bad, because the domains can be spoofed, and receivers can't tell.

### Mail-refusing records added by hand where remembered

* Good, because each domain is handled deliberately.
* Bad, because the domain that gets forgotten is the one left unprotected.
