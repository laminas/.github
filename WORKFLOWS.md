# Reusable Workflows

In addition to GitHub community health templates, this repository defines reusable workflows for the organization.

## Continuous Integration

To use the CI workflow:

```yaml
# In a package repository, in the file .github/workflows/continuous-integration.yml:
name: "Continuous Integration"

on:
  pull_request:
  push:
    branches:
    tags:

jobs:
  ci:
    uses: laminas/.github/.github/workflows/continuous-integration.yml@ci-v1
```
