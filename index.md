---
layout: default
title: Home
nav_order: 1
description: "This is a set of hypothetical documents for planning and user research for Central NuGet Management."
permalink: /
---

# Central NuGet Management
NuGet is one of the most foundational components of the .NET ecosystem, and you will find NuGet packages in projects from the smallest microservices to many of the largest applications on earth.
Some of the concepts that work well for solutions with a small number of projects struggle to perform well at scale, and dependency management is no different than any other tool, you need the right tool for the job. A bicycle is much more useful for traveling across a neighborhood than a continent, and similarly the constructs that we use for managing dependencies when a solution is first created are often not the right tool for the job when the solution grows.

In large companies, you will often find dedicated engineering teams focused on building and maintaining custom infrastructure and tooling for their development process. These teams build continuous integration pipelines, orchestrate cloud infrastructure, direct source code repositories, manage dependencies, and much more. Just within Microsoft, there are numerous such teams that support our many products and services. Over time, many of these teams have developed custom solutions for managing their NuGet packages and feeds across large solutions and across many source code repositories.

To provide an example, our Visual Studio repositories have more than 7000 projects between them with some of the solutions containing thousands of projects. Across this diverse landscape of projects covering features both new and old, you can find nearly every iteration of NuGet configurations from packages.config to the latest features of `<PackageReference>` in SDK-style projects. Visual Studio is one of the most complex desktop applications ever built, and we are proud of our team of engineers who keep our engineering systems running to support this Herculean effort. As Andrew Arnott, a member of this team recently shared, "it would not be possible to ship Visual Studio without the tools and infrastructure that we have built to manage our NuGet dependencies and feeds across our product."

## Package Version Management

[Central Package Version Management](/package-versions/packageversion/) provides tools to control the versions of NuGet dependencies across projects in a solution.

## Feed Management

[Central Feed Management](/feeds/feeds/) provides tools to manage which feeds are available for projects across a solution.

## Maintainer

[Central NuGet Maintainer](/maintainer/maintainer/) automates many of the dependency management tasks required on large repositories.
