# Archiving & Abandoning Repositories

This is a checklist of tasks to complete for abandoning and archiving repositories.

## Open a Pull Request with the following changes

### Update the README

- Remove any CI badges
- Prepend a notice to the top of the README similar to the following passage:
  ```markdown
  > [!CAUTION]
  > This package is **abandoned** and will receive no further development.
  >
  > See the Technical Steering Committee [meeting minutes](url to the minutes).
  >
  > Optionally a sentence about an actively maintained alternative library.
  ```
- Check for and remove any content from the README that is now irrelevant.

### Update `composer.json`

Add the `"abandoned":true` to `composer.json` at the top-level of the file and run `composer update nothing` to refresh the lockfile.

### Remove `renovate.json`

Remove the renovate configuration file - this is possibly unnecessary, as once archived, renovate is unlikely to open new PRs.

### Update `mkdocs.yml`

In `mkdocs` configuration, alter the `site_name` variable, for example:

```diff
- site_name: Some Name
+ site_name: Some Name (Abandoned)
```

## Close all existing PRs and Issues

Once the pull request has been merged, close all open issues and PRs, and ideally, leave a comment with a reason for the closure such as: "This library/project has been abandoned".

Waiting for the PR to be merged will prevent renovate opening new PRs automatically before you've managed to hit the archive button.

## Update Repository Custom Properties

There are several custom properties that will require updating so that the website is updated with the status of the repository. These are:

- `maintenance-mode` which should be set to "discontinued"
- `maintenance-mode-date` which should be the date the decision was made to abandon the project (i.e. TSC meeting date) in ISO format _YYYY-MM-DD_
- `maintenance-mode-minutes` this should be set to the file name of the meeting minutes, i.e. `2024-08-14-TSC-minutes.md`
- `eol-date` if unset, should be set to the same value as `maintenance-mode-date`

## Manually Re-Build Documentation

If necessary, rebuild and deploy the project docs by manually triggering the `docs build` action.

## Archive the Repository

In the repository settings, go to the "Danger Zone" and press the archive button.

## Update Documentation Elsewhere

Depending on the project, it may not have its own documentation, so you may need to open PRs in other repositories to add notices, or remove content to ensure that users are sufficiently aware of the project status.
