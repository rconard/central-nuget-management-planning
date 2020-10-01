---
layout: default
title: Package Versions
nav_order: 2
has_children: true
---

# Central Package Version Management
Central Package Version Management allows every project within a solution to source their NuGet package versions from a single `Directory.Packages.props` file at the solution level.

## Managing Package Versions at the Solution Level

Managing dependencies across many projects, solutions, and repositories can be very difficult at scale as `<PackageReference>` can be defined in project files, *.props files, and *.targets files throughout the code.

By centrally managing package versions, solutions can maintain more consistency in their NuGet dependencies across projects.

## Transitive Dependencies

If any packages are imported as transitive dependencies from a `<PackageReference>` where the `Version` is controlled by a `<PackageVersion>` in `Directory.Packages.props`, then the version of the transitive dependency will be controlled by CPVM.

If a `<PackageVersion>` has been defined for the package that is imported transitively, then that version will be imported across the solution.

## How to Use Central Package Version Management
In the solution directory, create a `Directory.Packages.props` file to define versions for each package used in your projects.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageVersion Include="Microsoft.CodeAnalysis.PublicApiAnalyzers" Version="2.9.4" />
    </ItemGroup>
    <ItemGroup>
        <PackageVersion Include="Newtonsoft.Json" Version="12.0.2" />
        <PackageVersion Include="Moq" Version="4.14.5" />
        <PackageVersion Include="Serilog" Version="2.9.0" />
    </ItemGroup>
</Project>
```

In this example, we have two `<ItemGroup>` blocks that define versions for each of the packages used in the project. Versions for both direct and transitive dependencies can be defined in this file.

### Sample Project 1: PackageReference Versions Handled Centrally
#### 1.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Version 12.0.2 defined in Directory.Packages.props -->
    <PackageReference Include="Newtonsoft.Json" />
    <!-- Version 4.14.5 defined in Directory.Packages.props -->
    <PackageReference Include="Moq" />
    <!-- Version 2.9.0 defined in Directory.Packages.props -->
    <PackageReference Include="Serilog" />
  </ItemGroup>
</Project>
```

### Sample Project 2: PackageReference Version Redefined

To ensure that projects managed by CPVM work within the CPVM model, any `Version="x.x.x"` defined in a `<PackageReference>` within an MSBuild file (e.g., a project file) will throw an error and not build. This auditing feature is particularly important for compliance and reproducible builds in large solutions.

#### 2.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Version 12.0.2 defined in Directory.Packages.props -->
    <PackageReference Include="Newtonsoft.Json" />
    <!-- ❗ Throws an Error - Project Will Not Build ❗ -->
    <PackageReference Include="Moq" Version="4.10.0" />
    <!-- ❗ Throws an Error - Project Will Not Build ❗ -->
    <PackageReference Include="Serilog" Version="2.8.0" />
  </ItemGroup>
</Project>
```

## Disable Central Package Version Management

If you do not want to use CPVM for a specific project within a solution that has a `Directory.Packages.props` defined, each project may opt-out through a setting in `<PropertyGroup>`.

### Sample Project 3: Disable CPVM for a Project
#### 3.csproj
```xml
<Project>
  <PropertyGroup>
    <ManagePackageVersionsCentrally>false</ManagePackageVersionsCentrally>
  </PropertyGroup>
  <ItemGroup>
    <!-- Earliest version of Newtonsoft.Json will be used -->
    <PackageReference Include="Newtonsoft.Json" />
    <!-- Version 4.10.0 defined in the Version attribute -->
    <PackageReference Include="Moq" Version="4.10.0" />
    <!-- Version 2.8.0 defined in the Version attribute -->
    <PackageReference Include="Serilog" Version="2.8.0" />
  </ItemGroup>
</Project>
```
