---
layout: default
title: Installation
parent: Maintainer
nav_order: 1
---

# Central NuGet Maintainer Installation

Central NuGet Maintainer is a service provided by NuGet.org that can manage git repositories on GitHub or on your git server.

### Public Central NuGet Maintainer

Provided as a free service for public repositories, the NuGet.org Central NuGet Maintainer provides the easiest option for getting started.

1. Authenticate to NuGet.org with an MSA that is a member of your NuGet organization.
2. Provide access to your git repository using the Central NuGet Maintainer setup form. For some services such as GitHub, Azure DevOps, and GitLab, you will be able to authenticate with an OAuth service. For other git repositories, you will need to provide an SSH Key and git URL.
3. Enable the Central NuGet Maintainer services that you need for your repository.

> Note: The Public Central NuGet Maintainer cannot access authenticated feeds.

### Private Central NuGet Maintainer

For private repositories, authenticated feeds, or secured network access, you will need to install the service on a machine within your network.

The Central NuGet Maintainer runs using the same NuGet application used for development and restore, so the configuration follows the documentation for the NuGet command line tooling. For example, configure authenticated feeds as you [would for a build machine](/feeds/authentication/).
