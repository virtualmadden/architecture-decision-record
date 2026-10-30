---
status: accepted
date: 2026-10-30
decision-makers: Jonathan Madden
---

# Receive domain mail through Porkbun forwarding, and send it through SES

## Context and Problem Statement

virtualmadden.dev needed a public contact address that doesn't expose my personal mailbox, which the site has always kept off the page. Mail sent to the address should reach my existing inbox, and replies should come from the address itself, never from the personal one.

How should a domain with no mail server receive and send mail?

## Decision Drivers

* The personal address must never appear, in mail received or sent.
* Mail sent as the domain has to pass DMARC, which stays at `p=reject` ([0006](0006-domains-refuse-mail-unless-they-send-it.md)).
* No mail server or forwarding code to run.
* As much as possible in Terraform, in my own accounts.

## Considered Options

* Porkbun forwarding to receive, and SES to send
* SES for both, with a Lambda that forwards received mail
* Porkbun email hosting
* A dedicated mail provider

## Decision Outcome

Chosen option: "Porkbun forwarding to receive, and SES to send", because each side uses a service built for it: Porkbun forwards incoming mail for free with no code, and SES signs outgoing mail with the domain's own DKIM key for a fraction of a cent.

* **Receiving.** The domain is listed in `mail_domains` in [dns-configuration](https://github.com/virtualmadden/dns-configuration), with MX records pointing at Porkbun's forwarders ([#4](https://github.com/virtualmadden/dns-configuration/pull/4)). Forwards exist only for `hello@`, `postmaster@` and `abuse@`; Porkbun has no catch-all.
* **Sending.** SES has an identity for the domain, with 2048-bit DKIM and a `mail.` MAIL FROM subdomain, so mail is SPF- and DKIM-aligned ([#5](https://github.com/virtualmadden/dns-configuration/pull/5)). A mail client such as Gmail's **Send mail as** connects to SES over SMTP.

### Consequences

* Good, because the personal mailbox is never visible to senders or recipients.
* Good, because DMARC stays at `p=reject` for everyone except SES sending as the domain with its DKIM key.
* Bad, because SES SMTP requires an IAM user's access key, a long-lived credential and an exception to [0005](0005-deploy-from-github-actions-with-oidc.md). It is limited to `ses:SendRawEmail` as the listed addresses, created outside Terraform so the secret never reaches state, and rotated by creating a new key before deleting the old one.
* Bad, because SES needs production access, a manual review by AWS, before it sends to anyone unverified.
* Bad, because the forwards themselves are configured in Porkbun's dashboard, not in Terraform.

### Confirmation

Mail sent from the client as the address arrives with SPF, DKIM and DMARC passing for the domain, and without the personal address in its headers.

## Pros and Cons of the Options

### SES for both, with a Lambda that forwards received mail

* Good, because everything lives in Terraform, in my accounts.
* Bad, because forwarding code has to rewrite senders correctly, or the forwarded mail fails DMARC and is silently junked or rejected.

### Porkbun email hosting

* Good, because one service handles a real mailbox, with webmail and SMTP.
* Bad, because it costs a yearly fee per mailbox, and the mailbox isn't in Terraform.

### A dedicated mail provider

* Good, because it has the best deliverability and its own apps.
* Bad, because a few dollars a month is more than one contact address needs.
