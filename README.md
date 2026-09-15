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

1. `Actions → Release → Run workflow` with the tiden CLI tag. It opens a pull request that rewrites the two literals in `sync.yml`.
2. Review and merge it. Tag the merge commit `vX.Y.Z` and push the tag — only an organization admin can, by repository rule.
3. The Release workflow publishes the GitHub release and prints the command that moves `v1` to it. Run it. Callers on `@v1` pick up the new pin on their next merge.

## Licence

Apache-2.0.
