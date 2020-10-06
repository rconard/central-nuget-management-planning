---
layout: default
title: Basic Groups
parent: Grouping and Overrides
grand_parent: Package Versions
nav_order: 1
---

# Basic Usage of Groups

For projects that have different package version requirements from the `Directory.Packages.props`, groups allow a `<PackageReference>` to have custom package versions that are different than other projects.

The sample projects on this page include the most common usage of Central Package Version Management groups.

## Examples

#### Directory.Packages.props

```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <!-- When CentralManagementGroup is unspecified for a <PackageVersion>, the <PackageVersion> is part of the default global group. -->
    <ItemGroup>
        <PackageVersion Include="Serilog" Version="2.9.0" />
        <PackageVersion Include="Newtonsoft.Json" Version="12.0.2" />
        <PackageVersion Include="foo" Version="1.1.1" />
    </ItemGroup>
    <!-- Each <PackageVersion> within this <ItemGroup> belongs to the "A" group as specified in their CentralManagementGroup attributes. -->
    <ItemGroup>
        <PackageVersion Include="Newtonsoft.Json" Version="11.0.2" CentralManagementGroup="A" />
        <PackageVersion Include="foo" Version="2.2.2" CentralManagementGroup="A" />
        <PackageVersion Include="qux" Version="2.0.0" CentralManagementGroup="A" />
        <PackageVersion Include="xyzzy" Version="2.0.0" CentralManagementGroup="A" />
        <PackageVersion Include="bar" Version="20.0.0" CentralManagementGroup="A" />
    </ItemGroup>
</Project>
```

In this `Directory.Packages.props` file, there are three `CentralManagementGroup` of `<PackageVersion>` defined.

1. The global `CentralManagementGroup` is automatically created, and all `<PackageVersion>` that do not have the `CentralManagementGroup` attribute defined belong to this group.
2. `CentralManagementGroup="A"` defines a `CentralManagementGroup` with `<PackageVersion>` for 5 packages.

### Sample Project 1: Global Group

#### 1.csproj

```xml
<Project>
  <ItemGroup>
    <!-- global group -->
    <!-- Version 2.9.0 -->
    <PackageReference Include="Serilog" />
    <!-- global group -->
    <!-- Version 12.0.2 -->
    <PackageReference Include="Newtonsoft.Json" />
    <!-- global group -->
    <!-- Version 1.1.1 -->
    <PackageReference Include="foo" />
  </ItemGroup>
</Project>
```

This project file represents the simple case where all `<PackageReference>` use versions from the global `CentralManagementGroup`.

There are no project package version overrides necessary for this project.

### Sample Project 2: Single Named Group

#### 2.csproj

```xml
<Project>
  <ItemGroup>
    <!-- global group -->
    <!-- Version 2.9.0 -->
    <PackageReference Include="Serilog"/>
    <!-- "A" group -->
    <!-- Version 11.0.2 -->
    <PackageReference Include="Newtonsoft.Json" CentralManagementGroup="A" />
    <!-- "A" group -->
    <!-- Version 2.2.2 -->
    <PackageReference Include="foo" CentralManagementGroup="A" />
    <!-- "A" group -->
    <!-- Version 2.0.0 -->
    <PackageReference Include="qux" CentralManagementGroup="A" />
    <!-- "A" group -->
    <!-- Version 2.0.0 -->
    <PackageReference Include="xyzzy" CentralManagementGroup="A" />
    <!-- "A" group -->
    <!-- Version 20.0.0 -->
    <PackageReference Include="bar" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

This project uses two  `CentralManagementGroup`, `"A"` and the global group.

All `<PackageReference>` dependencies are included in this `CentralManagementGroup` and are directly imported.
