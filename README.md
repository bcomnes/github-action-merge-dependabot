# Github Action Merge Dependabot

This action automatically approves and merges dependabot PRs.

## Usage

- install the [GitHub App](https://github.com/apps/dependabot-merge-action) on the repositories or organization where you want to use this action. Using a GitHub App is necessary since [this change](https://github.blog/changelog/2021-02-19-github-actions-workflows-triggered-by-dependabot-prs-will-run-with-read-only-permissions/) GitHub introduced which limits the permissions of the provided GITHUB_TOKEN and the availability of secrets in Dependabot pull requests. The source [code of the GitHub App](https://github.com/fastify/dependabot-merge-action-app/) is open source and hosted on Heroku. You can also host your own version of the app and customize the `api-url` input to point to your hosted instance.
- configure this action in your workflows providing the inputs described below

## Inputs

### `github-token`

**Required** A GitHub token. See below for additional information.

### `exclude`

_Optional_ An array of packages that you don't want to auto-merge and would like to manually review to decide whether to upgrade or not.

### `approve-only`

_Optional_ If `true`, the PR is only approved but not merged. Defaults to `false`.

### `merge-method`

_Optional_ The merge method you would like to use (squash, merge, rebase). Default to `squash` merge.

### `merge-comment`

_Optional_ An arbitrary message that you'd like to comment on the PR after it gets auto-merged. This is only useful when you're recieving too much of noise in email and would like to filter mails for PRs that got automatically merged.

### `api-url`

_Optional_ A custom url where the external API which is delegated the task of approving and merging responds.

## Example usage

### Basic example

```yml
name: CI
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # ...

  automerge:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: fastify/github-action-merge-dependabot@v2
        with:
          github-token: ${{secrets.GITHUB_TOKEN}}
```

### Excluding packages

```yml
steps:
  - uses: fastify/github-action-merge-dependabot@v2
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      exclude: ['react']
```

### Approving without merging

```yml
steps:
  - uses: fastify/github-action-merge-dependabot@v2
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      approve-only: true
```

## Notes

- A GitHub token is automatically provided by Github Actions, which can be accessed using `secrets.GITHUB_TOKEN` and supplied to the action as an input `github-token`.
- Only the [GitHub native Dependabot integration](https://docs.github.com/en/github/administering-a-repository/keeping-your-dependencies-updated-automatically) is supported, the old [Dependabot Preview app](https://github.com/marketplace/dependabot-preview) isn't.
- Make sure to use `needs: <jobs>` to delay the auto-merging until CI checks (test/build) are passed.
- If you want to use GitHub's [auto-merge](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/automatically-merging-a-pull-request) feature but still use this action to approve Pull Requests without merging, use `approve-only: true`.
