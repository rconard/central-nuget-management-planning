---
layout: default
title: Grouping and Overrides
parent: Package Versions
has_children: true
nav_order: 2
---

# Grouping and Overrides

Managing package versions across large numbers of complex projects may benefit from organizing `<PackageVersion>` into named groups of packages.

Additionally, the usage of named groups of `<PackageVersion>` can allow individual projects to use different versions than the primary versions used globally.

## Introduction

In the most simple case, a `<PackageVersion>` assigned to a named group can be used to separate all of the versions in a project.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageVersion Include="foo" Version="1.1.1" />
        <PackageVersion Include="foo" Version="2.2.2" CentralManagementGroup="A" />
    </ItemGroup>
</Project>
```

#### project.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Version 2.2.2 -->
    <PackageReference Include="foo" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

In this case, version 2.2.2 of `foo` is imported because it belongs to `CentralManagementGroup="A"`.

## Managing `<PackageVersion>` Across Projects

In order to preserve central management, `<PackageVersion>` should only be defined within your `Directory.Packages.props` file.

While MSBuild will allow you to define `<PackageVersion>` within any MSBuild compatible file, this should be considered an anti-pattern.

Through the usage of groups in your `Directory.Packages.props` file, you should be able to accomplish project-specific versioning from the solution level of your repo.

## CentralManagementGroup

The `CentralManagementGroup` attribute must be defined on both the `<PackageVersion>` item within the `Directory.Packages.props` file and on the `<PackageReference>` within your project.

Only one `CentralManagementGroup` may be defined for a given `<PackageReference>`, but different `<PackageReference>` within the same project may have different `CentralManagementGroup` specified.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageVersion Include="foo" Version="1.1.1" />
        <PackageVersion Include="foo" Version="2.2.2" CentralManagementGroup="bar" />
    </ItemGroup>
</Project>
```

#### project.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Version 2.2.2 -->
    <PackageReference Include="foo" CentralManagementGroup="bar" />
  </ItemGroup>
</Project>
```

In this example, the package `foo` appears twice in the `Directory.Packages.props` file. Within the `project.csproj` file, it appears once with `CentralManagementGroup="bar"`. This project will use version 2.2.2, the version designated in the `CentralManagementGroup` named "bar".

## Global CentralManagementGroup

When no `CentralManagementGroup` is defined, the `<PackageVersion>` or `<PackageReference>` automatically belongs to a "global" group.

You do not have to define the global group as it is automatically created for every `Directory.Packages.props` file.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageVersion Include="foo" Version="1.1.1" />
        <PackageVersion Include="foo" Version="2.2.2" CentralManagementGroup="bar" />
    </ItemGroup>
</Project>
```

#### project.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Version 1.1.1 -->
    <PackageReference Include="foo" />
  </ItemGroup>
</Project>
```

In this example, the package `foo` appears twice in the `Directory.Packages.props` file. Within the `project.csproj` file, it appears without a `CentralManagementGroup`. `foo` will be imported with the version found in the global group, 1.1.1.
