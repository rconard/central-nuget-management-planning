---
layout: default
title: Exceptions
parent: Grouping and Overrides
grand_parent: Package Versions
nav_order: 4
---

# Exceptions

The sample projects on this page cover two scenarios that are less common for Central Package Version Management. When the `Directory.Packages.props` file is missing information or cannot be edited, these examples demonstrate the developer experience.

## Examples

#### Directory.Packages.props

```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <!-- When CentralManagementGroup is unspecified for a <PackageVersion>, the <PackageVersion> is part of the default global group. -->
    <ItemGroup>
        <PackageVersion Include="Newtonsoft.Json" Version="12.0.2" />
        <PackageVersion Include="foo" Version="1.1.1" />
    </ItemGroup>
    <!-- Each <PackageVersion> within this <ItemGroup> belongs to the "A" group as specified in their CentralManagementGroup attributes. -->
    <ItemGroup>
        <PackageVersion Include="Newtonsoft.Json" Version="11.0.2" CentralManagementGroup="A" />
        <PackageVersion Include="foo" Version="2.2.2" CentralManagementGroup="A" />
    </ItemGroup>
    <!-- Each <PackageVersion> within this <ItemGroup> belongs to the "B" group as specified in their CentralManagementGroup attributes. -->
    <ItemGroup>
        <PackageVersion Include="foo" Version="3.3.3" CentralManagementGroup="B" />
    </ItemGroup>
</Project>
```

### Sample Project 9: Undefined CentralManagementGroup Member

#### 9.csproj

```xml
<Project>
  <ItemGroup>
    <!-- No entry for Newtonsoft.Json in the "B" group -->
    <!-- ❗ Throws an Error - Project Will Not Build ❗ -->
    <PackageReference Include="Newtonsoft.Json" CentralManagementGroup="B" />
  </ItemGroup>
</Project>
```

Because there is no entry for `Newtonsoft.Json` in `CentralManagementGroup="B"`, the project will throw an error and fail to build.

### Sample Project 10: Project Package Version Override Without Modifying Directory.Packages.props

> **Note:** This is an anti-pattern, but it is described here for the benefit of developers who are unable to modify the Directory.Packages.props for their solution. Once the package version discrepancy is resolved or the Directory.Packages.props file can be updated, this implementation should be removed from your project.

#### 10.csproj

```xml
<Project>
    <ItemGroup>
        <!-- Remove version defined in Directory.Packages.props, if any --> 
        <PackageVersion Remove="Newtonsoft.Json" /> 
        <!-- Define PackageVersion which applies only to this project --> 
        <PackageVersion Include="Newtonsoft.Json" Version="12.0.3" />
        <!-- Version 12.0.3 -->
        <PackageReference Include="Newtonsoft.Json" />
    </ItemGroup>
</Project>
```
