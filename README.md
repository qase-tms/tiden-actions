# tiden-actions

The GitHub Actions workflow that keeps a repository's requirements in sync with its merges in [Tiden](https://tiden.ai).

## What it does

Every merge to your default branch is reported to Tiden. Tiden measures how much of the change no requirement describes, writes the missing requirements, and moves on. Nobody on the team has to do anything after the file below is in place.

## Add it to a repository

Create `.github/workflows/tiden.yml`:

```yaml
name: Tiden
on:
  push:
    branches: [main]
permissions:
  id-token: write
  contents: read
jobs:
  sync:
    uses: qase-tms/tiden-actions/.github/workflows/sync.yml@v1
```

That is the whole file. No secret is stored anywhere: the job proves who it is with the token GitHub signs for each run, and Tiden checks the signature and the repository name against the repository you registered in the Tiden interface.

If Tiden gave you a link code when you registered the repository, add it once — it proves the registration and is harmless after the first successful run:

```yaml
    with:
      link-code: <the code Tiden showed you>
```

## It cannot fail your build

The job and every step are `continue-on-error`. A missing download, a checksum mismatch or an unreachable server is a skipped step with a `[tiden]` line in the log, never a red pipeline.

## What runs

A pinned release of the `tiden` CLI, verified against a checksum written into `sync.yml` itself. Both values change only through a reviewed pull request in this repository. `v1` always points at the latest reviewed release; pin `v1.x.y` if you want the file to never change under you.

## Releasing (maintainers)

Nobody starts a release by hand. Twice an hour the Release workflow compares the latest `tiden` release on `qase-tms/homebrew-tap` with the version `sync.yml` pins; when a newer one exists it downloads the archive, verifies it against the release's `checksums.txt`, and opens a pull request `bump/vX.Y.Z` that rewrites the two literals.

1. Review the two literals in that pull request and merge it.
2. The merge tags the next immutable `v1.x.y`, publishes the GitHub release and moves `v1` to it. Callers on `@v1` pick up the pin on their next merge; callers pinned to `v1.x.y` never move.

Pushing a file under `.github/workflows/` and creating or moving `v*` tags is beyond the workflow's built-in token, so those steps use a dedicated GitHub App — owned by the organization, installed on this repository only, its id and private key in the Actions secrets `TIDEN_ACTIONS_APP_ID` and `TIDEN_ACTIONS_APP_PRIVATE_KEY`, and listed as a bypass actor of the `release tags` ruleset. No personal token is involved. Without the App every such step prints the exact command for a maintainer (a member of the `ai-control-plane` team, or an organization owner) and the run still succeeds.

## Licence

Apache-2.0.
