---
layout: default
title: Multiple Groups
parent: Grouping and Overrides
grand_parent: Package Versions
nav_order: 2
---

{% raw %}
<script>(function(t,e,s,n){var o,a,c;t.SMCX=t.SMCX||[],e.getElementById(n)||(o=e.getElementsByTagName(s),a=o[o.length-1],c=e.createElement(s),c.type="text/javascript",c.async=!0,c.id=n,c.src="https://widget.surveymonkey.com/collect/website/js/tRaiETqnLgj758hTBazgdzqSORtCawzIPfKe_2BNdVN8xOmlvDpjVPJr1OGV1A0yQQ.js",a.parentNode.insertBefore(c,a))})(window,document,"script","smcx-sdk");</script>
{% endraw %}

# Multiple Named Groups

To limit the number of groups that are necessary to manage your solution, multiple groups can be used to provide granular control over package versions in each project.

For example, a bar can serve many drinks by combining a small number of bottles of alcohol and simple ingredients on the counter. Each group is like a bottle that when mixed with other groups can cover a wide range of scenarios with minimal duplication.

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

### Sample Project 6: Multiple Named Groups

#### 6.csproj

```xml
<Project>
  <ItemGroup>
    <!-- global group -->
    <!-- Version 2.9.0 -->
    <PackageReference Include="Serilog"/>
    <!-- "A" group -->
    <!-- Version 11.0.2 -->
    <PackageReference Include="Newtonsoft.Json" CentralManagementGroup="A" />
    <!-- "B" group -->
    <!-- Version 3.3.3 -->
    <PackageReference Include="foo" CentralManagementGroup="B" />
  </ItemGroup>
</Project>
```

This project uses three  `CentralManagementGroup`, `"A",` `"B"`, and the global group.

All `<PackageReference>` dependencies are included between these `CentralManagementGroup` and are directly imported.

### Sample Project 7: Multiple Named Groups with Transitive Dependencies

#### 7.csproj

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
    <!-- "B" group -->
    <!-- Version 3.3.3 -->
    <PackageReference Include="foo" CentralManagementGroup="B" />
  </ItemGroup>
</Project>
```

This project uses three  `CentralManagementGroup`, `"A",` `"B"`, and the global group.

`bar` has three dependencies. These are transitive dependencies for the project.

- `foo` is imported with version 3.3.3 from **`CentralManagementGroup="B"` because `foo` is directly imported**.
- `qux` is imported with version 2.0.0 from `CentralManagementGroup="A"` because `bar` is imported from the same group.
- `xyzzy` is imported with version 2.0.0 from `CentralManagementGroup="A"` because `bar` is imported from the same group.
