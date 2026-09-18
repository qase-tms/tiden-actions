# tiden-actions

Archived — Tiden reads repositories through its GitHub App; no job is needed

Installing the [Tiden GitHub App](https://app.tiden.ai) on a repository is what connects it: Tiden registers the repository, polls its default branch every fifteen minutes and describes every landed merge itself. The reusable workflow this repository published (`sync.yml`) reported the same merges from a CI job; that channel is retired (TIDEN-41).

## If a repository still carries `.github/workflows/tiden.yml`

Delete the file. Until then the job keeps working the way it always did: it pins one `tiden` release by version and checksum, can never fail your build, and prints one `[tiden]` line when the server no longer accepts what it sends. The `v1` tag stays where it is so existing callers resolve; no new release of this workflow will be published.

## What replaced it

- Automatic registration when the App is installed, when GitHub reports new repositories, when a component's repository is saved, and when a repository page is opened (tiden-app #516).
- The GitHub App poll as the only channel; the CI-token exchange, link codes and the delivered workflow are removed (tiden-app #517, tiden-cli #140).
