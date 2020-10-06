---
layout: default
title: Transitives in Groups
parent: Grouping and Overrides
grand_parent: Package Versions
nav_order: 3
---

# Transitive Dependency Handling in Groups

Managing transitive dependencies within groups is similar to [how NuGet resolves package dependencies for `<PackageReference>`](https://docs.microsoft.com/en-us/nuget/concepts/dependency-resolution).

## Global Group

When no `CentralManagementGroup` is defined, the `<PackageVersion>` from the global group is applied.

![global group transitive handling](/assets/images/global-group.png){: .transitive-diagram }

In this example, a package imported from the global group will import a transitive dependency from the global group.

## Single Group

When a direct dependency imports a transitive dependency that is also defined within the same group, that version will be used.

![single group transitive handling](/assets/images/single-group.png){: .transitive-diagram }

In this example, a direct dependency within `CentralManagementGroup="A"` imports a transitive dependency that is also found within `CentralManagementGroup="A"`, so that version is used.

## Single Group with Global Fallback

If no `<PackageVersion>` can be found for a package within the same `CentralManagementGroup`, the transitive dependency will fallback to the global group.

![single group with global fallback transitive handling](/assets/images/single-group-with-global-fallback.png){: .transitive-diagram }

In this example, there is no package version available within the same group as the direct dependency; however, a package version has been specified within the global group for this package. In this case, the project will import the transitive dependency with the version found in the global group.

## Multiple Groups with a Single Group Defined

When more than one `CentralManagementGroup` imports the same package as a transitive dependency but only one `CentralManagementGroup` has a `<PackageVersion>` defined for the transitive package, that version will be used.

![global group transitive handling](/assets/images/multiple-groups-with-single-group-defined.png){: .transitive-diagram }

In this example, two different packages in two different `CentralManagementGroup` import the same transitive dependency. Because `CentralManagementGroup="A"` has a `<PackageVersion>` for the transitive dependency but `CentralManagementGroup="B"` does not, the version for `CentralManagementGroup="A"` will be used.

## Multiple Groups with a Conflict

When more than one `CentralManagementGroup` imports the same package as a transitive dependency and **both** `CentralManagementGroup` have a `<PackageVersion>` defined for the transitive dependency, the `<PackageVersion>` defined by the first instance of the `<PackageReference>` will be imported..

![global group transitive handling](/assets/images/multiple-groups-with-conflict.png){: .transitive-diagram }

In this example, two different packages in two different `CentralManagementGroup` import the same transitive dependency. Because both `CentralManagementGroup="A"` and `CentralManagementGroup="B"` have a `<PackageVersion>` for the transitive dependency, the version for `CentralManagementGroup="A"` will be used because `foo` was imported first.

{% raw %}
<script>(function(t,e,s,n){var o,a,c;t.SMCX=t.SMCX||[],e.getElementById(n)||(o=e.getElementsByTagName(s),a=o[o.length-1],c=e.createElement(s),c.type="text/javascript",c.async=!0,c.id=n,c.src="https://widget.surveymonkey.com/collect/website/js/tRaiETqnLgj758hTBazgdzqSORtCawzIPfKe_2BNdVN8znk_2Bq_2F8UXMX4DniHct26QY.js",a.parentNode.insertBefore(c,a))})(window,document,"script","smcx-sdk");</script>
{% endraw %}

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
        <!-- bar has a dependency on foo 202.0.0 -->
        <!-- bar has a dependency on qux 202.0.0 -->
        <!-- bar has a dependency on xyzzy 202.0.0 -->
        <PackageVersion Include="bar" Version="20.0.0" CentralManagementGroup="A" />
    </ItemGroup>
    <!-- Each <PackageVersion> within this <ItemGroup> belongs to the "B" group as specified in their CentralManagementGroup attributes. -->
    <ItemGroup>
        <PackageVersion Include="foo" Version="3.3.3" CentralManagementGroup="B" />
        <PackageVersion Include="qux" Version="3.0.0" CentralManagementGroup="B" />
        <!-- baz has a dependency on foo 303.0.0 -->
        <!-- baz has a dependency on qux 303.0.0 -->
        <!-- baz has a dependency on xyzzy 303.0.0 -->
        <PackageVersion Include="baz" Version="30.0.0" CentralManagementGroup="B" />
    </ItemGroup>
</Project>
```

In this `Directory.Packages.props` file, there are three `CentralManagementGroup` of `<PackageVersion>` defined.

1. The global `CentralManagementGroup` is automatically created, and all `<PackageVersion>` that do not have the `CentralManagementGroup` attribute defined belong to this group.
2. `CentralManagementGroup="A"` defines a `CentralManagementGroup` with `<PackageVersion>` for 5 packages.
3. `CentralManagementGroup="B"` defines a `CentralManagementGroup` with `<PackageVersion>` for 3 packages.

*Example Package to Demonstrate Version Override*

There are two `<PackageVersion>` defined for `Newtonsoft.Json` to demonstrate using both the global and a named `CentralManagementGroup`.

- If a `<PackageReference>` does not specify a `CentralManagementGroup`, it will import version 12.0.2 from the global group.
- If `CentralManagementGroup=A` is defined, it will import 11.0.2 from group A.

*Example Packages to Demonstrate Transitive Import Behavior*

Package `foo` has three `<PackageVersion>` defined.

- `foo` is defined in both the global group and two named `CentralManagementGroup`.
- `foo` is a transitive dependency of `bar` and `baz` which are defined in two different `CentralManagementGroup`.

Package `qux` has two `<PackageVersion>` defined in two different `CentralManagementGroup`.

- `qux` **is not** defined in the global group.
- `qux` is a transitive dependency of `bar` and `baz` which are defined in two different `CentralManagementGroup`.

Package `xyzzy` is only defined within one named `CentralManagementGroup`.

- `xyzzy` **is not** defined in the global group.
- `xyzzy` is a transitive dependency of `bar` which is defined in the same group.
- `xyzzy` is a transitive dependency of `baz`, but it does not have a `<PackageVersion>` defined in the same group as `baz`.


### Sample Project 3: Transitive Dependency Resolution A

#### 3.csproj

```xml
<Project>
  <ItemGroup>
    <!-- "A" group -->
    <!-- Version 20.0.0 -->
    <PackageReference Include="bar" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

This project uses a single `CentralManagementGroup="A"`.

`bar` has three dependencies. These are transitive dependencies for the project.

- `foo` is imported with version 2.2.2 from `CentralManagementGroup="A"` because `foo` has a `<PackageVersion>` defined for group `"A"`.
- `qux` is imported with version 2.0.0 from `CentralManagementGroup="A"` because `qux` has a `<PackageVersion>` defined for group `"A"`.
- `xyzzy` is imported with version 2.0.0 from `CentralManagementGroup="A"` because `xyzzy` has a `<PackageVersion>` defined for group `"A"`.

### Sample Project 4: Transitive Dependency Resolution B

#### 4.csproj

```xml
<Project>
  <ItemGroup>
    <!-- "B" group -->
    <!-- Version 30.0.0 -->
    <PackageReference Include="baz" CentralManagementGroup="B" />
  </ItemGroup>
</Project>
```

This project uses a single `CentralManagementGroup="B"`.

`baz` has three dependencies. These are transitive dependencies for the project.

- `foo` is imported with version 3.3.3 from `CentralManagementGroup="B"` because `foo` has a `<PackageVersion>` defined for group `"B"`.
- `qux` is imported with version 3.0.0 from `CentralManagementGroup="B"` because `qux` has a `<PackageVersion>` defined for group `"B"`.
- `xyzzy` is imported with version 303.0.0 because `baz` has a direct dependency on `xyzzy` version 303.0.0. There is no `<PackageVersion>` defined for `xyzzy` in group `"B"`.

### Sample Project 5: Single Named Group with Transitive Dependencies

#### 5.csproj

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
    <!-- Version 20.0.0 -->
    <PackageReference Include="bar" CentralManagementGroup="A" />
  </ItemGroup>
</Project>
```

This project uses two  `CentralManagementGroup`, `"A"` and the global group.

`bar` has three dependencies. These are transitive dependencies for the project.

- `foo` is imported with version 2.2.2 from `CentralManagementGroup="A"` because `bar` is imported from the same group.
- `qux` is imported with version 2.0.0 from `CentralManagementGroup="A"` because `bar` is imported from the same group.
- `xyzzy` is imported with version 2.0.0 from `CentralManagementGroup="A"` because `bar` is imported from the same group.
