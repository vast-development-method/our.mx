# our.mx

[![Bingo](https://github.com/vast-development-method/our.mx/actions/workflows/bingo.yml/badge.svg?branch=main)](https://github.com/vast-development-method/our.mx/actions/workflows/bingo.yml)

Daily email-server checks using [OctoMailTest](https://github.com/octoleo/octomailtest).
Servers are identified publicly by aliases. Bingo keeps the detailed test
messages and findings while private mode anonymizes domains, hosts, mailbox
addresses, and other identifying values in logs, summaries, and reports.

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
secret**. Keep the actual values in secrets, including the domain and host.

| Secret | Value |
| --- | --- |
| `BINGO_DOMAIN` | Mail domain used for DNS checks (required) |
| `BINGO_HOST` | Mail-server hostname (required) |
| `BINGO_EMAIL` | Mailbox address used for authentication and the send-to-self test (required) |
| `BINGO_PASS` | Mailbox password (required) |
| `BINGO_IMAP_PORT` | Optional IMAPS port; defaults to `993` |
| `BINGO_SIEVE_PORT` | Optional ManageSieve port; defaults to `4190` |

Bingo uses OctoMailTest's default SMTP submission port, `587`, with STARTTLS.
The diagnostic script separately checks implicit TLS (SMTPS) on port `465`.
`BINGO_SMTP_PORT` is no longer read by this workflow; an existing secret with
that name can be removed. This prevents an SMTP port override from directing
the STARTTLS authentication and send tests to the implicit-TLS port.

Once the workflow is merged and the secrets are set, open **Actions → Bingo →
Run workflow**, select **main**, and start the first run. Other branches are
skipped. Missing required secrets fail the configuration check before any
diagnostics run or history is recorded.

The workflow uses the merged privacy/history action at a fixed commit. It
runs both existing diagnostic scripts with their original tests and pass/fail
rules. Private mode is enabled and quiet mode is disabled. Failure notifications
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
