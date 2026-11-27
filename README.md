# makr-code Community Health

This public repository contains default community health files for public
repositories owned by [@makr-code](https://github.com/makr-code). GitHub uses
supported defaults when a repository does not provide its own version.

These defaults help keep contribution and support guidance consistent across
projects. A repository-specific file takes precedence over the matching file
here.

## Project status badges

| Project | Workflow status (default branch) |
| --- | --- |
| [ThemisDB](https://github.com/makr-code/ThemisDB) | [![ThemisDB: Build Clang Fast on develop](https://github.com/makr-code/ThemisDB/actions/workflows/build-clang-fast.yml/badge.svg?branch=develop)](https://github.com/makr-code/ThemisDB/actions/workflows/build-clang-fast.yml?query=branch%3Adevelop) |
| [GalaxyQuest](https://github.com/makr-code/GalaxyQuest) | [![GalaxyQuest: CI/CD Pipeline on develop](https://github.com/makr-code/GalaxyQuest/actions/workflows/ci.yml/badge.svg?branch=develop)](https://github.com/makr-code/GalaxyQuest/actions/workflows/ci.yml?query=branch%3Adevelop) |

These native GitHub badges update without README commits. Each represents
only the named workflow, not all project checks, release readiness, or
security status. A workflow without runs on the selected branch can show
no status. Update the links if a workflow or default branch is renamed.
No build/test badge is shown for SyncTool (only a dependency graph workflow
was found) or chimera (no Actions workflows were found).

## Shared community defaults

- [Contribution guidance](CONTRIBUTING.md)
- [Code of conduct](CODE_OF_CONDUCT.md)
- [Support guidance](SUPPORT.md)
- [Security reporting policy](SECURITY.md)
- Bug report and feature request forms, plus a pull request template
- GitHub Sponsors configuration in `.github/FUNDING.yml`; [FUNDING.md](FUNDING.md)
  remains the human-readable sponsorship guide

Default community files are not repository settings. They do not enable
private vulnerability reporting, enforce branch protection, grant permissions,
or copy workflows into other repositories. Issue templates are inherited only
when the receiving repository has no issue templates of its own. Project
READMEs, licenses, CODEOWNERS, and Dependabot configurations are not inherited
community defaults.

## CI/CD: combine repository states and information

The **Repository state report** workflow runs weekly on Monday at 08:17 UTC
and can also be started manually from Actions. It combines metadata for
`makr-code/.github`, `makr-code/makr-code`, `makr-code/ThemisDB`, and
`makr-code/GalaxyQuest` into one Actions job summary:

- Default branch and archived status
- Open issues and pull requests combined
- Status/conclusion and link for the most recent Actions run
- Explicit unavailable-data indicators when an API request fails

This is a read-only snapshot, not a Git branch merge, synchronization of source
files, or a claim that every required check passed. It never modifies the
reported repositories. A failed fetch produces a partial summary and fails
the job rather than silently reporting success.

Use the manual `repositories` input to select up to 20 space-separated
`makr-code` repositories. The built-in token has repository-scoped permissions:
cross-repository reads rely on public visibility. Private repositories require
a separately designed, narrowly scoped access arrangement; do not place
credentials in workflow inputs or files. Schedules run from the default branch
and may be delayed or disabled by GitHub after prolonged repository inactivity.

Other repositories can explicitly call this reusable workflow:

```yaml
name: Repository overview
on:
  workflow_dispatch:
  schedule:
    - cron: '17 8 * * 1'
permissions:
  contents: read
  actions: read
jobs:
  overview:
    uses: makr-code/.github/.github/workflows/repository-state.yml@main
    with:
      repositories: makr-code/.github makr-code/makr-code makr-code/ThemisDB makr-code/GalaxyQuest
```

For reproducible production use, replace `@main` with a reviewed full commit
SHA. Reusable workflows run in the caller's context and cannot increase the
permissions granted by the caller.

## Human-in-the-loop documentation updates

The **Documentation review** workflow is manually runnable here and reusable
by other repositories. It opens an issue requesting a review of README,
CTEST, BENCHMARKS, and CHANGELOG documents where present. An existing open issue
with the same title is reused, preventing repeated scheduled requests from
creating duplicates.

It does not generate updates, run benchmarks, create a PR, or merge changes.
A contributor or separately authorized automation opens a PR with evidence;
a maintainer reviews and approves it. Enable periodic review in a project
by adding a caller workflow:

```yaml
name: Documentation maintenance
on:
  workflow_dispatch:
  schedule:
    - cron: '43 8 1 * *'
permissions:
  contents: read
  issues: write
jobs:
  review:
    uses: makr-code/.github/.github/workflows/documentation-review.yml@main
    with:
      documents: README.md, CTEST.md, BENCHMARKS.md, CHANGELOG.md
```

Pin the workflow to a reviewed full commit SHA for production. Issues must
be enabled in the calling repository. No additional token is required, and
this workflow intentionally has no permission to write code or pull requests.

## Settings to apply separately in project repositories

The following settings are recommendations for `makr-code/makr-code` and
other projects; adding these files here does **not** apply them remotely:

- Maintain a project or profile README with purpose, entry points, status,
  and relevant project links. For the profile repository `makr-code/makr-code`,
  avoid presenting it as a software project if it only contains the profile.
- Override contribution, support, or security guidance when a project needs
  more specific instructions.
- Protect the default branch with a ruleset requiring pull requests, blocking
  force pushes and deletion, and limiting bypasses. Require human review
  where reviewer availability permits it. Require only checks that actually
  exist and run on every relevant PR; restrict direct pushes through the
  pull-request requirement.
- Set default Actions token permissions to read-only; grant narrowly scoped
  writes per workflow. Restrict allowed Actions and prefer reviewed SHA pins.
- Configure Dependabot per project for its actual dependency ecosystems and
  GitHub Actions. Do not add update entries for nonexistent manifests.
- Enable secret scanning, push protection, private vulnerability reporting,
  and code scanning where available. Configure CodeQL only for supported
  languages present in a code repository, not a documentation-only profile.
- Select project-specific labels and issue types where supported; enable
  Discussions only if someone will maintain that support channel.

## Validation

This repository has no existing build, test, or lint suite. Review the issue
forms and templates in GitHub, then manually run both workflows after merge.
For documentation review, run twice and confirm that the second run links to
the existing issue. For repository reporting, verify the configured repository rows and
try an unavailable repository to confirm that failure is visible.

## Support open-source work

If you find the projects useful, you can support their development through
[GitHub Sponsors](https://github.com/sponsors/makr-code).
