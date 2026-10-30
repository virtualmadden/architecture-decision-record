# Architecture decisions

Decisions that shape more than one of my repositories: how AWS accounts are divided, where DNS lives, how CI reaches AWS, and the conventions every repository follows. Each record explains the problem, the options considered, and why one was chosen.

A decision about a single repository lives in that repository's `docs/decisions/` instead, so it's reviewed with the code it describes ([0001](docs/decisions/0001-record-cross-repository-decisions-here.md)).

## Decisions

| # | Decision | Status |
| --- | --- | --- |
| [0001](docs/decisions/0001-record-cross-repository-decisions-here.md) | Record decisions that span repositories here, and the rest in each repository | Accepted |
| [0002](docs/decisions/0002-one-aws-account-per-project.md) | One AWS account per project, in an AWS Organization | Accepted |
| [0003](docs/decisions/0003-production-only-until-a-project-needs-pre-production.md) | Production only, until a project needs pre-production | Accepted |
| [0004](docs/decisions/0004-keep-every-domain-zone-in-shared-services.md) | Keep every registered domain's zone in shared services | Accepted |
| [0005](docs/decisions/0005-deploy-from-github-actions-with-oidc.md) | Deploy from GitHub Actions with OIDC, through separate plan and deploy roles | Accepted |
| [0006](docs/decisions/0006-domains-refuse-mail-unless-they-send-it.md) | Domains refuse mail unless they send it | Accepted |
| [0007](docs/decisions/0007-repository-baseline.md) | A standard baseline for every active repository | Accepted |
| [0008](docs/decisions/0008-share-infrastructure-as-template-repositories.md) | Share infrastructure patterns as template repositories, not modules | Accepted |
| [0009](docs/decisions/0009-retire-repositories-by-archiving-or-deleting.md) | Retire repositories by archiving them, or deleting them if they hold nothing | Accepted |

## Adding a decision

A decision belongs here if it affects more than one repository, or sets a convention that every repository follows.

1. Copy [`adr-template.md`](docs/decisions/adr-template.md) to `docs/decisions/NNNN-title-with-dashes.md`, using the next number.
2. Fill in what applies and delete the optional sections that don't.
3. Add it to the table above, and open a pull request. Link it from the pull requests in other repositories that put it into practice.

Records aren't edited to reverse a decision. A new record replaces it, and the old one's status becomes `superseded by NNNN`.

The records follow [MADR 4](https://adr.github.io/madr/). This repository is public, so records explain the reasoning but leave out identifiers such as account IDs.
