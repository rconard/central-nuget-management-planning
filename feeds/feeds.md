---
layout: default
title: Feeds
nav_order: 3
has_children: true
---

# Central Feed Management

Package sources are currently added to NuGet via NuGet.config files. This is highly configurable, but it can create challenges for restoring large and complex solutions. For example, a NuGet.config file stored on a developer's computer within their user directory may cause their development environment to behave differently from other developers' or from CI/CD environments.

By configuring feeds centrally, package sources can be managed for security and repeatable builds.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageSource key="NuGet.org" Feed="https://nuget.org/api/v3/" />
        <PackageSource key="Local Sources" Feed="c:\installed_packages" />
    </ItemGroup>
</Project>
```

In this example, both a server-based and local feed have been added using `PackageSource`.

## Enable Central Feed Management
Once a `Directory.Packages.props` file has been created, add a `<ManagePackageFeedsCentrally>` item set to `true` in the `<PropertyGroup>` in a `Directory.Build.props` file.

#### Directory.Build.props
```xml
<Project>
  <PropertyGroup>
    <ManagePackageFeedsCentrally>true</ManagePackageFeedsCentrally>
  </PropertyGroup>
</Project>
```

## NuGet.config Automatically Disabled

NuGet.config files follow a directory-based hierarchy within a repository and on a user's machine. This can lead to issues with build reproducibility when local configurations differ from those downloaded as part of source code repositories.

Once central feed management has been enabled for a solution in `Directory.Build.props`, NuGet will no longer check for the presence of NuGet.config files or use any parameters from these files.