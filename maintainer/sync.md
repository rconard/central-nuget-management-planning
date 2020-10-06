---
layout: default
title: Sync
parent: Maintainer
nav_order: 5
---

# Central NuGet Sync

Products with multiple repositories may need to maintain a central `Directory.Packages.props` across every repository. This allows a synchronization of package versions and feeds across repositories.

## Repository Groups

In the configuration for Central NuGet Maintainer where the list of repositories is managed, each repository can be added to a group.

Within each group, an update to the `Directory.Packages.props` file will trigger a new branch and pull request to be created on each of the other repositories.

## Duplicate Updater Branches

The [Central NuGet Updater](/maintainer/updater/) will create a new branch and pull request for each updated package for each repository.

This allows each project to individually test version updates in branches.

Unfortunately, this can also lead to significant duplication.

When a pull request for a `Directory.Packages.props` file is synchronized to a solution, all of the branches and pull requests for lower package versions that were addressed by the update will automatically be closed.
