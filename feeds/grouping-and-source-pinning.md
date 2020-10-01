---
layout: default
title: Grouping and Source Pinning
parent: Feeds
nav_order: 1
---

# Grouping and Source Pinning

The default NuGet behavior with feeds declared in NuGet.config is to query each feed for every package. For large repositories, this can lead to performance and network bottlenecks.

By specifying a group for a feed, only the direct and transitive dependencies of a package defined with a `<PackageVersion>` in the same group will use this feed. More information about adding a `CentralManagementGroup` to a `<PackageVersion>` can be found in the [documentation for package version grouping](http://localhost:4000/package-versions/grouping-and-overrides/grouping-and-overrides/).

## Global Group

A global group will be automatically created that includes all `<PackageSource>`, `<PackageVersion>`, and `<PackageReference>` that do not have a `CentralManagementGroup` specified.

`<PackageSource>` that belong to the global group will also be queried for `<PackageReference>` that belong to a `CentralManagementGroup` unless specified by [Feed Scoping](/feeds/grouping-and-source-pinning/#feed-scoping).

## Adding Groups

A feed is added to a group by specifying a `CentralManagementGroup` attribute to the `<PackageSource>`.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageSource key="NuGet.org" Feed="https://nuget.org/api/v3/" />
        <PackageSource key="Local Sources" Feed="c:\installed_packages" CentralManagementGroup="A" />
        <PackageVersion Include="foo" Version="2.2.2" />
        <PackageVersion Include="bar" Version="20.0.0" CentralManagementGroup="A" />
    </ItemGroup>
</Project>
```

In this example, the "Local Sources" feed belongs to a `CentralManagementGroup` named "A".

#### project.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Feeds: NuGet.org -->
    <PackageReference Include="foo" />
    <!-- Feeds: NuGet.org and Local Sources -->
    <PackageReference Include="bar" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

When the package `foo` is imported with a `<PackageReference>`, only the NuGet.org feed will be queried because both the `<PackageVersion>` and the `<PackageReference>` for `foo` belong to the global group.

When the package `bar` is imported with a `<PackageReference>`, both the NuGet.org feed and the "Local Sources" feed will be queried because the `<PackageReference>` is in `CentralManagementGroup="A"` which also contains the `<PackageSource>` for "Local Sources."

## Feed Scoping

The items in a `CentralManagementGroup` may need to be isolated from the global group, and Feed Scoping allows the feed behavior within a group to be controlled.

### Append

The default Feed Scoping is to append a feed specified in a `CentralManagementGroup` to the feeds that belong to the global group.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageSource key="NuGet.org" Feed="https://nuget.org/api/v3/" />
        <PackageSource key="Private Feed" Feed="https://contoso.com/nuget/" CentralManagementGroup="A" Scope="append"/>
    </ItemGroup>
</Project>
```

In this example, a `<PackageReference>` that belongs to `CentralManagementGroup="A"` will query both NuGet.org and the "Private Feed."

A Feed Scope of `append` is the default, and the `Scope` attribute does not have to be specified for this behavior.

### Direct

When `Scope="direct"` is applied to a `<PackageSource>` in a `CentralManagementGroup`, then all direct dependencies that belong to the `CentralManagementGroup` will only be queried from `<PackageSource>` that belong to the same group. Transitive dependencies that are imported from direct dependencies that belong to this `CentralManagementGroup` will also query the `<PackageSource>` from the global group.

For example, this can be used when a private NuGet package is accessible only within your network and your private package has dependencies from a public server such as NuGet.org.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageSource key="NuGet.org" Feed="https://nuget.org/api/v3/" />
        <PackageSource key="Private Feed" Feed="https://contoso.com/nuget/" CentralManagementGroup="A" Scope="direct"/>
    </ItemGroup>
</Project>
```

#### project.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Feeds: NuGet.org -->
    <PackageReference Include="foo" />
    <!-- Direct Dependency Feeds: "Private Feed" -->
    <!-- Transitive Dependency Feeds: "Private Feed" and NuGet.org -->
    <PackageReference Include="bar" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

In this example, a `<PackageReference>` that belongs to `CentralManagementGroup="A"` will only query the "Private Feed" for direct dependencies specified in `<PackageReference>`. Transitive dependencies will query both the "Private Feed" and NuGet.org.

### Isolate

When `Scope="isolate"` is applied to a `CentralManagementGroup`, then all direct and transitive dependencies that belong to the `CentralManagementGroup` will only be queried from `<PackageSource>` that belong to the same group. No queries will be made to `<PackageSource>` in the global group.

For example, this can be used to secure the supply chain for all dependencies within a `CentralManagementGroup`.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageSource key="NuGet.org" Feed="https://nuget.org/api/v3/" />
        <PackageSource key="Private Feed" Feed="https://contoso.com/nuget/" CentralManagementGroup="A" Scope="isolate"/>
    </ItemGroup>
</Project>
```

#### project.csproj
```xml
<Project>
  <ItemGroup>
    <!-- Feeds: NuGet.org -->
    <PackageReference Include="foo" />
    <!-- Direct Dependency Feeds: "Private Feed" -->
    <!-- Transitive Dependency Feeds: "Private Feed" -->
    <PackageReference Include="bar" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

In this example, a `<PackageReference>` that belongs to `CentralManagementGroup="A"` will only query the "Private Feed" for any direct or transitive dependency imported with a `<PackageReference>` in the `CentralManagementGroup`.

> Note: If a specific transitive dependency is imported by members of multiple feeds that specify `Scope="isolate"`, a build error will be thrown.

{% raw %}
<script>(function(t,e,s,n){var o,a,c;t.SMCX=t.SMCX||[],e.getElementById(n)||(o=e.getElementsByTagName(s),a=o[o.length-1],c=e.createElement(s),c.type="text/javascript",c.async=!0,c.id=n,c.src="https://widget.surveymonkey.com/collect/website/js/tRaiETqnLgj758hTBazgdzqSORtCawzIPfKe_2BNdVN8wTk6SMU5brX3lXug_2BlQAv3.js",a.parentNode.insertBefore(c,a))})(window,document,"script","smcx-sdk");</script>
{% endraw %}
