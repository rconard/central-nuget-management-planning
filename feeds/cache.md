---
layout: default
title: Cache
parent: Feeds
nav_order: 3
---

# Central Feed Cache

Cache handling for feeds specified by `<PackageSource>` in Central Feed Management is separate from feeds specified in NuGet.config.

## Cache Organization

In order to enforce the security rules of [Feed Scoping](/feeds/grouping-and-source-pinning/#feed-scoping), packages downloaded using Central Feed Management will be stored in a separate cache.

For projects with Central Feed Management enabled, the global package cache will not be utilized.

The Central Feed Management cache organizes a sub-cache for every feed.

#### Cache Storage Locations
```
On Linux:
~/.nuget/packagesource/<feed URL hash>/<packagename>

On Windows:
%userprofile%\.nuget\packagesource\<feed URL hash>\<packagename>
```

Here's an example of how a package may be stored:
```
On Linux:
~/.nuget/packagesource/c48fd7be919f1d622a02ea44ccb5d119/newtonsoft.json/12.0.3/

On Windows:
%userprofile%\.nuget\packagesource\c48fd7be919f1d622a02ea44ccb5d119\newtonsoft.json\12.0.3\
```
