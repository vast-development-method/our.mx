# our.mx

[![Bingo](https://github.com/vast-development-method/our.mx/actions/workflows/bingo.yml/badge.svg?branch=main)](https://github.com/vast-development-method/our.mx/actions/workflows/bingo.yml)

Daily email-server checks using [OctoMailTest](https://github.com/octoleo/octomailtest).
Servers are identified publicly by aliases. Bingo keeps the detailed test
messages and findings while private mode anonymizes domains, hosts, mailbox
addresses, port numbers, and other identifying values in logs, summaries, and reports.

## Bingo

[Bingo workflow](.github/workflows/bingo.yml) runs on `main` every day at
**02:17 UTC / 04:17 Namibia time**, and can also be started manually. GitHub
may delay scheduled runs. The badge above shows the latest workflow result
on `main`; click it to read the logs and job summary or download `Bingo-report`.

[Bingo test history](octomailtest-history/Bingo/README.md) is created by the
first diagnostic run. It includes a cumulative run counter, passed/failed
totals, UTC timestamps, and links to the **latest 10** detailed reports.
The action commits successful and failed diagnostic results before failing
the workflow when critical failures are found. Older reports are removed
from the current files but remain accessible in earlier Git commits.

### Repository secrets

Add these under **Settings → Secrets and variables → Actions → New repository
secret**. Only host, email, and password are required. The action derives the
mail domain from the email address and tests its standard ports automatically.
Keep actual server values in secrets.

| Secret | Value |
| --- | --- |
| `BINGO_HOST` | Mail-server hostname (required) |
| `BINGO_EMAIL` | Mailbox address used for authentication and the send-to-self test (required) |
| `BINGO_PASS` | Mailbox password (required) |
| `BINGO_DOMAIN` | Optional mail-domain override for DNS checks |
| `BINGO_SMTP_RELAY_PORT` | Optional SMTP relay-port override |
| `BINGO_SMTP_PORT` | Optional SMTP submission-port override |
| `BINGO_SMTPS_PORT` | Optional secure SMTP submission-port override |
| `BINGO_IMAP_PORT` | Optional IMAP-port override |
| `BINGO_IMAPS_PORT` | Optional secure IMAP-port override |
| `BINGO_SIEVE_PORT` | Optional ManageSieve-port override |

Every tested port is mapped from an optional secret. Set these to the ports
your server uses, or leave them unset to use OctoMailTest's standard defaults.
The action detects the connection mode and reports each endpoint's features
and outcomes. It tests both SMTP alternatives and both IMAP alternatives.
The secure SMTP and IMAP endpoints must pass. Unavailable normal alternatives
produce warnings. Credentials are sent only after TLS is established.
Private logs identify the protocol and test without exposing the port number.

Once the workflow is merged and the secrets are set, open **Actions → Bingo →
Run workflow**, select **main**, and start the first run. Other branches are
skipped. Missing required secrets fail the configuration check before any
diagnostics run or history is recorded.

The workflow pins the action to a fixed commit and runs both diagnostic scripts.
Private mode is enabled and quiet mode is disabled. Failure notifications
follow your GitHub Actions notification settings.

### History and additional servers

`history-keep` controls the retained report count, and `history-dir` controls
the base directory. The generated per-alias README and `history.json` track
the cumulative counters even after older reports are pruned. Downloadable
Actions artifacts follow the repository's separate artifact-retention setting.

History commits use the workflow's `GITHUB_TOKEN` with `contents: write`;
repository rules must allow those commits to `main`. No personal access token
is needed. The action handles checkout, recording, and committing the history.

To add another server, copy the Bingo workflow, choose a new unrelated public
alias, and use a matching secret prefix, report path, and artifact name. Add
its workflow badge and history link here. Keep the same
`octomailtest-history` concurrency group in every history-writing workflow
so their commits are serialized. The workflows run only on the schedule or
manual dispatch, so history commits do not trigger more tests.
