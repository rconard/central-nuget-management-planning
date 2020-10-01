---
layout: default
title: Updater
parent: Maintainer
nav_order: 3
---

# Central NuGet Updater

Updating package versions can be difficult to manage within a single package, and the challenge is even greater when a version upgrade can affect many projects.

The Central NuGet Updater monitors the packages used within the `Directory.Packages.props` file for updates and submits pull requests.

## Monitoring for Updates

Central NuGet Maintainer will monitor each of the packages and feeds for new package versions to be published.

## Pull Requests

For each package that is updated, Central NuGet Maintainer will create a new branch and submit a pull request to update the `Directory.Packages.props` file.

To support testing and development by your team, each pull request originates from a new branch.

## Central Management

When the pull request is created, the system will take note of each project that depends on the updated package.

For GitHub repos, a comment will be made on the pull request including a list of affected projects.

For other git repos, the commit message will be created with a record of each project in the solution that relies on the updated package.
