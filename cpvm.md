---
layout: default
title: Package Versions
nav_order: 2
---

# Central Package Version Management
Central Package Version Management allows every project within a solution to source their NuGet package versions from a single `.props` file at the solution level.

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

If you do not want to use CPVM for a specific project within a solution that has a `Directory.Packages.props` defined, each project may opt-out through a setting in `<PropertyGroup>`.

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

## Enable Central Package Version Management
Once a `Directory.Packages.props` file has been created, add a `<ManagePackageVersionsCentrally>` item set to `true` in the `<PropertyGroup>` in a `Directory.Build.props` file.

#### Directory.Build.props
```xml
<Project>
  <PropertyGroup>
    <ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>
  </PropertyGroup>
</Project>
```

To date, our engineering efforts for CPVM have been focused on supporting restore operations for development and CI/CD pipelines. In the future, we are planning to add additional tooling for Visual Studio.

## Runtime Targeting with .NET 5 SDK
Updates to CPVM will only be included in the .NET 5 SDK. You can target .NET Core and .NET Framework from the .NET 5 SDK.

.NET Core runtimes and SDK's are decoupled, but they share the same major and minor versions. Any version of the .NET Core or .NET 5 SDK can build any .NET Framework app or any .NET Core app that is equal to or lower than the version of the SDK installed if the relevant Targeting Pack is installed.

Currently, using the .NET 5 SDK with Visual Studio requires installing the preview version of Visual Studio. In the future when .NET 5 is released, it will be possible to install the .NET 5 SDK in the General Availability channel of Visual Studio version 16.8.

## What works today?
- Visual Studio 16.7 command line restore operations
- Visual Studio GUI restore operations

## What doesn't work today?
- Adding a package via the NuGet package management interface in Visual Studio does not currently support CPVM. Once a package has been added using a GUI, it is necessary to add a corresponding <PackageVersion> entry within Directory.Packages.props.
- Similarly, modifying a package version using the Visual Studio package management interface will update the project file, and it is necessary to move this Version="x.x.x" attribute to the corresponding <PackageVersion> entry within Directory.Packages.props.
