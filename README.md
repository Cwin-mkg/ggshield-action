<a href="https://gitguardian.com/"><img src="https://cdn.jsdelivr.net/gh/gitguardian/ggshield-action/doc/logo.svg"></a>

---

# [GitGuardian Shield](https://github.com/GitGuardian/ggshield) GitHub Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-v1-undefined.svg?logo=github&logoColor=white&style=for-the-badge)](https://github.com/marketplace/actions/gitguardian-shield-action)
[![Docker Image Version (latest semver)](https://img.shields.io/docker/v/gitguardian/ggshield?color=1B2D55&sort=semver&style=for-the-badge&label=ggshield)](https://hub.docker.com/r/gitguardian/ggshield)
[![License](https://img.shields.io/github/license/GitGuardian/ggshield-action?color=%231B2D55&style=for-the-badge)](LICENSE)
![GitHub stars](https://img.shields.io/github/stars/gitguardian/ggshield-action?color=%231B2D55&style=for-the-badge)

Find exposed credentials in your commits using [**GitGuardian Shield**](https://github.com/GitGuardian/ggshield).

The **GitGuardian Shield** (ggshield) is a CLI application that runs in your local environment
or in a CI environment to help you detect more than 200 types of secrets, as well as other potential security vulnerabilities or policy breaks.

**GitGuardian Shield** uses our [public API](https://api.gitguardian.com/doc) through [py-gitguardian](https://github.com/GitGuardian/py-gitguardian) to scan your files and detect potential secrets or issues in your code. **The `/v1/scan` endpoint of the [public API](https://api.gitguardian.com/doc) is stateless. We will not store any files you are sending or any secrets we have detected**.

## Requirements

- Have an account on **GitGuardian**. [**Sign up now**](https://dashboard.gitguardian.com/api/v1/auth/user/github_login/authorize?utm_source=github&utm_medium=gg_shield&utm_campaign=shield1) if you haven't before!
- Create an API key on the [API Section](https://dashboard.gitguardian.com/api) of your dashboard.

### Project secrets

- `GITGUARDIAN_API_KEY` **[Required]**: Necessary to authenticate to GitGuardian's API. You can set the `GITGUARDIAN_API_KEY` value in the "Secrets" page of your repository's settings. You can create your API Key [**here**](https://dashboard.gitguardian.com/api/v1/auth/user/github_login/authorize?utm_source=github&utm_medium=gg_shield&utm_campaign=shield1).

## Usage

Add a new job to your GitHub workflow using the `GitGuardian/ggshield-action` action.

```yaml
name: GitGuardian scan

on: [push, pull_request]

jobs:
  scanning:
    name: GitGuardian scan
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0 # fetch all history so multiple commits can be scanned
      - name: GitGuardian scan
        uses: GitGuardian/ggshield-action@master
        env:
          GITHUB_PUSH_BEFORE_SHA: ${{ github.event.before }}
          GITHUB_PUSH_BASE_SHA: ${{ github.event.base }}
          GITHUB_PULL_BASE_SHA: ${{ github.event.pull_request.base.sha }}
          GITHUB_DEFAULT_BRANCH: ${{ github.event.repository.default_branch }}
          GITGUARDIAN_API_KEY: ${{ secrets.GITGUARDIAN_API_KEY }}
```

Do not forget to add your [GitGuardian API Key](https://dashboard.gitguardian.com/api/v1/auth/user/github_login/authorize?utm_source=github&utm_medium=gg_shield&utm_campaign=shield1) to the `GITGUARDIAN_API_KEY` secret in your project settings.

## Adding extra options to the action

The following options can be added to the action by using action inputs:

```
Options:
  --show-secrets  Show secrets in plaintext instead of hiding them.
  --exit-zero     Always return a 0 (non-error) status code, even if issues
                  are found.The env var GITGUARDIAN_EXIT_ZERO can also be used
                  to set this option.

  --all-policies  Present fails of all policies (Filenames, FileExtensions,
                  Secret Detection). By default, only Secret Detection is
                  shown.

  -v, --verbose   Verbose display mode.
```

Example:

```yaml
name: GitGuardian scan

on: [push, pull_request]

jobs:
  scanning:
    name: GitGuardian scan
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0 # fetch all history so multiple commits can be scanned
      - name: GitGuardian scan
        uses: GitGuardian/ggshield-action@master
        with:
          args: -v --all-policies
        env:
          GITHUB_PUSH_BEFORE_SHA: ${{ github.event.before }}
          GITHUB_PUSH_BASE_SHA: ${{ github.event.base }}
          GITHUB_PULL_BASE_SHA: ${{ github.event.pull_request.base.sha }}
          GITHUB_DEFAULT_BRANCH: ${{ github.event.repository.default_branch }}
          GITGUARDIAN_API_KEY: ${{ secrets.GITGUARDIAN_API_KEY }}
```

## Examples of GitGuardian scanning

![Scan output example](https://cdn.statically.io/gh/GitGuardian/ggshield-action/51c86f8a/doc/example_output.png)

This a sample scan result from **GitGuardian Shield**.

If the secret detected has been revoked and you do not wish to rewrite the git history, you can use a value of the policy break (for example: the value of `|_password_|`) or the ignore SHA displayed in your `.gitguardian.yaml` under `matches-ignore`.

An example of a configuration file is available [here](https://github.com/GitGuardian/ggshield/blob/main/.gitguardian.example.yml).

![Status example](https://cdn.statically.io/gh/GitGuardian/ggshield-action/51c86f8a/doc/status.png)

If there are secret leaks or other security issues in your commit your workflow will be marked as failed.

Be sure to add `GitGuardian scan` to your required status checks in your repository settings to stop pull requests with security issues from being merged.

# License

**GitGuardian shield** is MIT licensed.
