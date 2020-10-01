---
layout: default
title: Maintainer
has_children: true
nav_order: 4
---

# Central NuGet Maintainer

With large and complex repositories, there are advantages to managing dependencies as a central function rather than distributing these tasks across manual processes.

The Central NuGet Maintainer provides a number of functions that assist repository owners with managing, updating, and coordinating dependencies in their repositories.

## Centrally Generated Lock Files

The Central NuGet Maintainer will compute lock files for each project in your repository and submit a pull request to your repository each time the `Directory.Packages.props` file is updated or the `<PackageReference>` items in your projects change.

[Learn more here.](/maintainer/centrally-generated-lock-files/)

## Updater

When NuGet packages on your feeds are updated, the Central NuGet Maintainer will submit a pull request for the updated version of the package.

[Learn more here.](/maintainer/updater/)

## Vulnerabilities

When NuGet.org issues a package vulnerability warning, the Central NuGet Maintainer will submit a pull request for a patched version of the package.

[Learn more here.](/maintainer/vulnerabilities/)

## Sync

If your team manages a `Directory.Packages.props` file across multiple repositories, Central NuGet Management will submit a pull request when the file changes in any of the repositories being monitored.

[Learn more here.](/maintainer/sync/)
