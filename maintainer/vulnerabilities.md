---
layout: default
title: Vulnerabilities
parent: Maintainer
nav_order: 4
---

# Vulnerability Management

NuGet.org is continually adding new functionality to improve your products' supply chain security.

As part of these efforts, NuGet.org can help your repositories update packages with known vulnerabilities.

## Severity

Central NuGet Maintainer can be configured to address vulnerabilities based on their severity.

### Pull Request Threshold

Each repository can be configured to create branches and pull requests depending on the severity of the vulnerability.

For example, a repository can be configured to only initiate vulnerability pull requests for "Moderate" or higher vulnerabilities and ignore "Low" severity vulnerabilities.

### Push Threshold

Each repository can be configured to automatically be patched for vulnerabilities depending on the severity level of the vulnerability.

For example, a repository could be configured to automatically push a commit that patches a "Severe" vulnerability.
