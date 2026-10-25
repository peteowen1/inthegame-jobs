# inthegame-jobs

Scheduled data jobs for [inthegame.blog](https://inthegame.blog), kept in a
public repo because public repos get free GitHub Actions minutes. The site's
own repo is private, so every scheduled run there is billed (its October 2026
bill was ~3,700 minutes).

**No site code lives here.** Each run checks out the latest commit of the
private `peteowen1/inthegame-blog` (no history) with a read-only token and runs
that repo's scripts, exactly as the same workflows did there. Only the
workflow files are in this repo.

| workflow | what it publishes | schedule (UTC) |
|---|---|---|
| `tennis-tour-draws.yml` | `tennis/tour-draws.json` on R2 | every 3 hours at :41 |
| `player-index.yml` | `afl/player-index.json`, `football/player-index/*` on R2 | every 6 hours at :23 |
| `tennis-draw.yml` | `tennis/draw.json` on R2 (the Grand Slam in progress) | hourly at :17, in Jan and May-Sep only |
| `daily-deck.yml` | `football/daily-deck/*`, `afl/daily-deck/*` on R2 (never overwrites a day) | daily 00:05 |
| `afl-sim-snapshot.yml` | `afl/sim-snapshot.json` on R2 | every 6 hours at :41 |
| `afl-turnover-points.yml` | `afl/turnover-points-<season>.json` on R2 | daily 21:23 |
| `afl-live-chains.yml` | `afl/live-chains.parquet`, `afl/live-ratings.json`, `afl/live-xg.json` on R2 | every 15 min in AFL match windows, Mar-Sep |
| `keepalive.yml` | nothing: re-enables the workflows above, since GitHub disables a public repo's schedules after 60 days without activity | 1st and 15th, 03:07 |

## Secrets

| secret | what it is |
|---|---|
| `ITG_READ_TOKEN` | Fine-grained PAT, repository `peteowen1/inthegame-blog` only, **Contents: Read-only**. Expires about 2027-10-24; when it does, every checkout fails red. Make a new one with the same settings and replace the secret. |
| `CLOUDFLARE_API_TOKEN` | Cloudflare API token with **Workers R2 Storage: Edit** on the inthegame.blog account |
| `CLOUDFLARE_ACCOUNT_ID` | The inthegame.blog Cloudflare account id |

Secrets are never given to pull requests from forks, and no workflow here has
a `pull_request` trigger.

## Rules for this repo

- **Logs are public.** Never print file contents, environment or tokens. The
  scripts print feed names and counts only.
- **No artifacts.** They would be public, and the checked-out code is private.
- **Never add a self-hosted runner.** On a public repo it lets anyone's code
  run on that machine.
- Change a script in `inthegame-blog`, not here. A workflow change here should
  match the copy that used to live in `inthegame-blog/.github/workflows/`.
