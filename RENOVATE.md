# Renovate

This repository contains the shared configuration for Renovate, a tool to automate dependency updates via a
`renovate-config.json` file which would be available as a
[shareable preset](https://docs.renovatebot.com/config-presets/).

## Goals

This configuration has the following goals for a shared preset:

- a single PR for anything non-major that doesn't fit the current version constraints. (e.g. coding standards)
- a PR for any new majors grouped by org.
- otherwise, the lockfile is updated and automatically merged once tests pass, opening a PR only if those fail.

## Next steps for downstream maintainers

Once the WhiteSource Renovate GitHub app is enabled for a repository, a new `Configure Renovate` PR will be opened
containing a basic `renovate.json` file with the following contents:

```json
{
    "extends": [
        "local>laminas/.github:renovate-config"
    ]
}
```

In order to be fully compatible with this Renovate configuration, you must ensure the following criteria are met:
1. CI actions are enabled for `push` events on branches with the prefix `renovate/*`.
2. The lockfile must have been generated using Composer with a version `>=2.2`.
3. Lastly, for Renovate to detect the correct version of PHP to use for lockfile maintenance, the PHP version must be
   set in `composer.json` under the key [`config.platform.php`](https://getcomposer.org/doc/06-config.md#platform).

## Configuration

By calling the file `renovate-config.json` and placing it in this repository, we can take advantage of Renovate
detecting this, allowing easy
[onboarding](https://docs.renovatebot.com/getting-started/installing-onboarding/#repository-onboarding) for
repositories.

### Presets

Using primarily the inbuilt shared presets, this allows us to somewhat overcome the need to upgrade our configuration as
often when Renovate updates a major version. Take for example: `":automergeDisabled"`, this will add the configuration
`"automerge": false`. Should Renovate update this in a major version bump, they will also update the preset. So where
possible presets have been chosen.

```json
"extends": [
    ":dependencyDashboard",
    ":ignoreModulesAndTests",
    ":automergeMinor",
    ":automergeBranch",
    ":prNotPending",
    ":rebaseStalePrs",
    ":semanticCommitsDisabled",
    ":separateMajorReleases",
    ":combinePatchMinorReleases",
    ":enableVulnerabilityAlerts",
    ":timezone(UTC)",
    ":gitSignOff",
    ":label(renovate)",
    "group:allNonMajor"
],
```

**[:dependencyDashboard](https://docs.renovatebot.com/presets-default/#dependencydashboard)** - This will open a
persistent issue in each repository to allow easy tracking of all Renovate updates.
**[:ignoreModulesAndTests](https://docs.renovatebot.com/presets-default/#ignoremodulesandtests)** - This ensures
Renovate does not try to update `composer.json` inside `tests/`, `vendor/` etc.
**[:automergeMinor](https://docs.renovatebot.com/presets-default/#automergeminor)** - Automatically merge non-major
updates of updates by default.
**[:automergeBranch](https://docs.renovatebot.com/presets-default/#automergebranch)** - But set the default automatic
merge type to be `branch` meaning a PR is only opened on failure.
**[:prNotPending](https://docs.renovatebot.com/presets-default/#prnotpending)** - PRs will also only be created AFTER
all checks have finished running.
**[:rebaseStalePrs](https://docs.renovatebot.com/presets-default/#rebasestaleprs)** - Any PRs previously opened by
Renovate will be automatically rebased should they fall behind.
**[:semanticCommitsDisabled](https://docs.renovatebot.com/presets-default/#semanticcommitsdisabled) - Disable semantic
prefixes for commit messages and PR titles.
**[:separateMajorReleases](https://docs.renovatebot.com/presets-default/#separatemajorreleases)** - Any new major
releases for a package will be separated into its own update.
**[:combinePatchMinorReleases](https://docs.renovatebot.com/presets-default/#combinepatchminorreleases)** - Patch and
minor releases for a single package will be combined to a single update.
**[:enableVulnerabilityAlerts](https://docs.renovatebot.com/presets-default/#enablevulnerabilityalerts)** - Open a PR
should the repository have any vulnerability alerts (see below).
**[:timezone(UTC)](https://docs.renovatebot.com/presets-default/#timezonearg0)** - Not strictly necessary but ensures
schedules use the UTC timezone.
**[:gitSignOff](https://docs.renovatebot.com/presets-default/#gitsignoff)** - DCO requires all commits to be signed off.
**[:label(renovate)](https://docs.renovatebot.com/presets-default/#labelarg0)** - Add the label `renovate` to any PRs.
**[group:allNonMajor](https://docs.renovatebot.com/presets-group/#groupallnonmajor)** - Any non-major updates will be
grouped into a single update.

#### Vulnerability Alerts

From https://docs.renovatebot.com/configuration-options/#vulnerabilityalerts:

> Renovate can read from GitHub's Vulnerability Alerts and customize Pull Requests accordingly. For this to work, you
> must first ensure you have enabled "Dependency graph" and "Dependabot alerts" under the "Security & analysis" section
> of the repository's "Settings" tab.
>
> Additionally, if you are running Renovate in app mode then you must make sure that the app has been granted the
> permissions to read "Vulnerability alerts". If you are the account admin, browse to the app (e.g.
> https://github.com/apps/renovate), select "Configure", and then scroll down to the "Permissions" section and verify
> that read access to "vulnerability alerts" is mentioned.
>
> Once the above conditions are met, and you have received one or more vulnerability alerts from GitHub for this
> repository, then Renovate will attempt to raise fix PRs accordingly.

### Other Settings

```json
"commitBodyTable": true,
"enabledManagers": ["composer", "dockerfile", "docker-compose", "github-actions"],
"lockFileMaintenance": {"enabled": true, "extends": ["schedule:daily"]},
"platformAutomerge": true,
"prFooter": "[Read more information](https://github.com/laminas/.github/blob/main/RENOVATE.md) about the use of [Renovate Bot](https://github.com/renovatebot/renovate) within Laminas.",
"rangeStrategy": "replace",
"rollbackPrs": true,
"vulnerabilityAlerts": {"rangeStrategy": "bump}
```

**[commitBodyTable](https://docs.renovatebot.com/configuration-options/#commitbodytable)** - Adds a table to the commit
message describing all updates in the commit.
**[enabledManagers](https://docs.renovatebot.com/configuration-options/#enabledmanagers)** - To begin with, this is
enabled for `composer`, `dockerfile`, `docker-compose` and `github-actions`. Other managers (such as `npm` etc) are also
available.
**[lockFileMaintenance](https://docs.renovatebot.com/configuration-options/#lockfilemaintenance)** - Lockfile
maintenance should be performed during the scheduled run.
**[platformAutomerge](https://docs.renovatebot.com/configuration-options/#platformautomerge)** - Use GitHub's merging
features, falling back to Renovate's own merging methods.
**[prFooter](https://docs.renovatebot.com/configuration-options/#prfooter)** - The text here will be set as the footer
to any PR opened by Renovate. 
**[rangeStrategy](https://docs.renovatebot.com/configuration-options/#rangestrategy)** - Setting this to `replace`
ensures that PRs are only created once the new release falls outside the version constraints inside `composer.json`.
**[rollbackPrs](https://docs.renovatebot.com/configuration-options/#rollbackprs)** - A rare occurrence, but should a
package become revoked, a PR to downgrade the package will be created.
**[vulnerabilityAlerts](https://docs.renovatebot.com/configuration-options/#vulnerabilityalerts)** - Vulnerability alerts, enabled by a previous preset, will use a `rangeStrategy` of `update-lockfile` by default. This ensures that `composer.json` files are updated also.

### Package Rules

```json
"packageRules": [
    {"matchDepTypes": ["require"], "rangeStrategy": "widen"},
    {"matchPackagePatterns": ["^laminas/"], "groupSlug": "laminas", "groupName": "all Laminas packages"},
    {"matchPackagePatterns": ["^laminas-api-tools/"], "groupSlug": "laminas-api-tools", "groupName": "all Laminas API Tools packages"},
    {"matchPackagePatterns": ["^mezzio/"], "groupSlug": "mezzio", "groupName": "all Mezzio packages"}
]
```

The first of these package rules will ensure that non-development dependency version constraints are widened when a
newer version is available outside them. Widening the range of a development dependency makes little sense.
 - `replace` Replace the range with a newer one if the new version falls outside it, and update nothing otherwise.
 - `widen` Widen the range with newer one, e.g. `^1.0.0` -> `^1.0.0 || ^2.0.0`.

Although not necessary, this will group updates from each of these organizations into a single update. Combined with the
rules from above, this will usually only occur for new major releases.

## Links

[Renovate on GitHub](https://github.com/renovatebot/renovate)
[Renovate Documentation](https://docs.renovatebot.com)
[WhiteSource Renovate website](https://www.whitesourcesoftware.com/free-developer-tools/renovate/)
[WhiteSource Renovate GitHub app](https://github.com/marketplace/renovate)
