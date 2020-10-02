---
layout: default
title: Multi-Targeting
parent: Grouping and Overrides
grand_parent: Package Versions
nav_order: 5
---

{% raw %}
<script>(function(t,e,s,n){var o,a,c;t.SMCX=t.SMCX||[],e.getElementById(n)||(o=e.getElementsByTagName(s),a=o[o.length-1],c=e.createElement(s),c.type="text/javascript",c.async=!0,c.id=n,c.src="https://widget.surveymonkey.com/collect/website/js/tRaiETqnLgj758hTBazgdzqSORtCawzIPfKe_2BNdVN8wMe8HNIqE4OpuND_2BN6BzW5.js",a.parentNode.insertBefore(c,a))})(window,document,"script","smcx-sdk");</script>
{% endraw %}

# Multi-Targeting

As exciting new features become available in the .NET ecosystem, your code may need to adapt as package versions sometimes lack proper support.

The examples on this page demonstrate how existing MSBuild features can be combined with grouping to provide powerful multi-targeting capabilities.

## Examples

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <!-- When CentralManagementGroup is unspecified for a <PackageVersion>, the <PackageVersion> is part of the default global group. -->
        <PackageVersion Include="foo" Version="1.1.1" />
        <!-- These three CentralManagementGroup's are used for multi-targeting. -->
        <PackageVersion Include="foo" Version="2.2.2" CentralManagementGroup="A" />
        <PackageVersion Include="foo" Version="3.3.3" CentralManagementGroup="Anetcoreapp3.1" />
        <PackageVersion Include="foo" Version="4.4.4" CentralManagementGroup="Anet472" />
    </ItemGroup>
</Project>
```

In this `Directory.Packages.props` file, there are four `CentralManagementGroup` of `<PackageVersion>` defined.

- The global `CentralManagementGroup` is defined automatically and applies to version 1.1.1 of `foo` where no `CentralManagementGroup` is specified.
- `"A"`, `"Anetcoreapp3.1"`, `"Anet472"` are three different `CentralManagementGroup`. They are named similarly as a convention, but they remain three independent groups. MSBuild does not understand any relationship between these groups.

### Sample Project 11: Inline Multi-Targeting

#### 11.csproj
```xml
<Project>
  <ItemGroup>
    <PackageReference Include="foo" CentralManagementGroup="Anetcoreapp3.1" Condition="'$(TargetFramework)' == 'netcoreapp3.1'" />
    <PackageReference Include="foo" CentralManagementGroup="Anet472" Condition="'$(TargetFramework)' == 'net472'" />
  </ItemGroup>
</Project>
```

During a build targeting .NET Core 3.1, `foo` will be imported with version 3.3.3.

During a build targeting .NET Framework 4.7.2, `foo` will be imported with version 4.4.4.

### Sample Project 12: <PropertyGroup> Multi-Targeting

#### 12.csproj
```xml
<Project>
	<PropertyGroup Condition="'$(TargetFramework)' == 'netcoreapp3.1'">
		<FooPackageGroup>Anetcoreapp3.1</FooPackageGroup>
	</PropertyGroup>
	<PropertyGroup Condition="'$(TargetFramework)' == 'net472'">
		<FooPackageGroup>Anet472</FooPackageGroup>
	</PropertyGroup>

  <ItemGroup>
    <PackageReference Include="foo" CentralManagementGroup="$(FooPackageVersion)" />
  </ItemGroup>
</Project>
```

During a build targeting .NET Core 3.1, `foo` will be imported with version 3.3.3.

During a build targeting .NET Framework 4.7.2, `foo` will be imported with version 4.4.4.

Alternatively this could be simplifed as below so that changing the TFM would surface the need to revisit the groups:

```xml
<Project>
  <ItemGroup>
    <PackageReference Include="foo" CentralManagementGroup="A$(TargetFramework)" />
  </ItemGroup>
</Project>
```
