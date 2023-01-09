# SlimFlow Workflow

SlimFlow is heavily inspired by GitFlow and OneFlow,
but strongly enforces some rules which are:

- Uses only one main branch called `main`.
- Has 3 different branch types:
  - Features (`feature/`)
  - Hotfixes (`hotfix/`)
  - Releases (`release/`)
- Primarily use `--squash` merges to keep history slim and streamlined.
  <br/>(This can be changed via the settings.)
- Use semantic versioning for releases and creates
  release tags prefixed with `v` (fe: `v.1.0.0`).

# Install/Uninstall

```bash
# install
curl -sL https://raw.githubusercontent.com/LeaveAirykson/git-slimflow/main/bin/slimflow | bash -s install

# to update slimflow run
slimflow update

# uninstall
slimflow uninstall

```

# Features

Feature branches can be short or long lived branches used to work on
specific features to implement.

## Create

```shell
git feature create [name]
```

Creates new feature branch based on currently check out branch via:  
`git checkout -b feature/[name] main`

## Publish

You can publish a feature to the remote:

```shell
git feature publish [name]
```

Pushes feature branch to origin via:  
`git push origin feature/[name]:feature/[name]`

## Finish

```shell
git feature finish [name]
```

- Squash merges branch into main branch via  
  `git merge --squash feature/[name] main`
- Deletes `feature/[name]` branch locally

# Releases

Release branches are short lived and exist only during creation.
Therefore they have no publish option.

```bash
git release create 2.1.0

# with useNpmVersion 'on' you can use the phrases:
# 'major', 'minor', 'patch' to increment the version.
git release create major
```

Creates new release branch based on currently check out branch via:  
`git checkout -b release/[MAJOR.MINOR.PATCH] main`

## Finish

To finish a release first check out the branch and call `finish`.

```bash
git checkout release/v2.1.0
git release finish
```

- Squash merges branch into `main` via
  <br>`git merge --squash release/[MAJOR.MINOR.PATCH] main`
- Creates a tagged commit in Semver syntax: `v[MAJOR.MINOR.PATCH]`
- If autocreation of the changelog is active, it will trigger during this stage.
- Deletes `release/[MAJOR.MINOR.PATCH]` branch locally.
- Pushes the changes to the remote.

# Hotfixes

Hotfixes are similar to how release branches work -
they create a new version on the main branch.

```bash
# check out the version to hotfix
git checkout v2.1.0
git hotfix create 2.1.1

# with useNpmVersion 'on' you can omit the version
# as it falls back to 'patch' value.
git checkout v2.1.0
git hotfix create
```

Creates new hotfix branch based on currently check out branch via:  
`git checkout -b hotfix/[MAJOR.MINOR.PATCH]`

## Finish

To finish a hotfix first check out the branch and call `finish`.

```bash
git checkout hotfix/v2.1.1
git hotfix finish
```

- Squash merges branch into `main` via
  <br>`git merge --squash release/[MAJOR.MINOR.PATCH] main`
- Creates a tagged commit in Semver syntax: `v[MAJOR.MINOR.PATCH]`
- If autocreation of the changelog is active, it will trigger during this stage.
- Deletes `hotfix/[MAJOR.MINOR.PATCH]` branch locally.
- Pushes the changes to the remote.

# Additional Settings

There are several settings for changing the behaviour of the changelog creation.

To change the setting simply call:

```bash
git config slimflow.[setting] "[value]"

# you can even set them globally
git config --global slimflow.[setting] "[value]"
```

## General settings

| Setting                     | Default | Desc                                                           |
| --------------------------- | ------- | -------------------------------------------------------------- |
| `usenpmversion` `[on\|off]` | `off`   | If active slimflow will use `npm version` to bump the version. |

## Type based settings

| Setting        | Default    | Desc                       |
| -------------- | ---------- | -------------------------- |
| `featuremerge` | `--squash` | Merge strategy of features |
| `releasemerge` | `--squash` | Merge strategy of releases |
| `hotfixmerge`  | `--squash` | Merge strategy of hotfixes |

### Changing merge strategies

The merging strategies can be changed or disabled for every type via:

```bash
git config slimflow.[name]merge "--no-ff"
```

```bash
# sets merge strategy for features to no fast forward
git config slimflow.featuremerge "--no-ff"

# disables merge strategy for features and falls back to default setting
git config slimflow.featuremerge ""

# you can even set it globally
git config --global slimflow.featuremerge ""
```

## Changelog settings

Slimflow can create a changelog file/entry based on your commit history.
It can recognize different flags (commit prefixes) for seperating them into categories.

| Setting                       | Default | Desc                                                                            |
| ----------------------------- | ------- | ------------------------------------------------------------------------------- |
| `createchangelog` `[on\|off]` | `off`   | De-/Activates changelog autocreation                                            |
| `includemisc` `[on\|off]`     | `on`    | Include no flagged commits in Misc category                                     |
| `ticketpattern`               |         | Regex pattern to recognize Ticket names                                         |
| `commiturl`                   |         | Url to detail view of commit where `{commit}`<br>gets replaced with commit hash |
| `ticketurl`                   |         | Url to ticket system where `{ticket}`<br>gets replaced with ticket name         |
| `compareurl`                  |         | Url to repository compare url between versions                                  |

### Notice to the url settings

The settings for `commiturl`, `ticketurl` and `compareurl` are used during
changelog creation to link commit hashes, ticketnames and Release names to their
related detail view in your repository hoster and ticket system.

### Commit flag prefixes

| Commit starts with | Category         |
| ------------------ | ---------------- |
| `[break]`          | Breaking changes |
| `[add]`            | Added            |
| `[change]`         | Changed          |
| `[fix]`            | Bug fixes        |

Commits without any flag prefix will be ignored in output.
This can be changed via:

```shell
git config slimflow.includemisc "on"
```

This will include a Category named **Misc** in the changelog.
