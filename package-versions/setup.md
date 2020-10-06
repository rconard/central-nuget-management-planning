---
layout: default
title: Setup
parent: Package Versions
nav_order: 1
---

# Setup and Configure Central Package Version Management

## Create the Central Package Version Management File
In the solution directory, create a `Directory.Packages.props` file to define versions for each package used in your projects.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageVersion Include="foo" Version="1.0.0" />
    </ItemGroup>
</Project>
```

With a `<PackageVersion>` item within an `<ItemGroup>`, the `Version=""` will be used wherever the specified package is called by a `<PackageReference>`.

`<PackageVersion>` is an MSBuild item that allows a project to specify a version for a NuGet package without performing the import.

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

{% raw %}
<script>(function(t,e,s,n){var o,a,c;t.SMCX=t.SMCX||[],e.getElementById(n)||(o=e.getElementsByTagName(s),a=o[o.length-1],c=e.createElement(s),c.type="text/javascript",c.async=!0,c.id=n,c.src="https://widget.surveymonkey.com/collect/website/js/tRaiETqnLgj758hTBazgdzqSORtCawzIPfKe_2BNdVN8xfDfEWR_2FTl_2FOgyhReJwUYh.js",a.parentNode.insertBefore(c,a))})(window,document,"script","smcx-sdk");</script>
{% endraw %}

## What works today?
- Visual Studio 16.7 command line restore operations
- Visual Studio GUI restore operations

## What doesn't work today?
- Adding a package via the NuGet package management interface in Visual Studio does not currently support CPVM. Once a package has been added using a GUI, it is necessary to add a corresponding <PackageVersion> entry within Directory.Packages.props.
- Similarly, modifying a package version using the Visual Studio package management interface will update the project file, and it is necessary to move this Version="x.x.x" attribute to the corresponding <PackageVersion> entry within Directory.Packages.props.
