---
layout: default
title: Authenticated Feeds
parent: Feeds
nav_order: 2
---

# Authenticated Feeds

Managing NuGet feeds centrally requires all of the information necessary for authentication to be contained within the source code repository.

To improve security, NuGet will use similar paradigms to cloud services for authentication.

> Note: Central Feed Management does not support `<packageSourceCredentials>` from NuGet.config.

#### Directory.Packages.props
```xml
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <PackageSource Feed="https://contoso.com/api/v3/" credentials="contoso" />
        <PackageSource Feed="https://fabrikam.com/nuget/feed/" credentials="fabrikam" />
    </ItemGroup>
</Project>
```

Use the `credentials` attribute to specify a key that NuGet will match to a credential source in a credentials file, in Azure Key Vault, or with a credential provider.

## Credential File Authentication

Where the file system is appropriate for storing credentials, such as on a development machine, store your credentials in a text file on disk.

#### credentials File Storage Locations
```
On Linux:
~/.nuget/credentials

On Windows:
%userprofile%\.nuget\credentials
```

#### credentials File Contents
```
[contoso]
api_key=C86E6D8247C85BE39345FE6919424
api_secret=IFLsz2lDDMOohvTcrRoKFXO5235bi7KEdGIg7cn8tATrVmF6P0VVHfIa3OVJcQW8

[fabrikam]
username=netdev
password=password123
```
> Note: Wherever possible, it is better for services to issue revocable API keys rather than requiring passwords.

## Azure Key Vault

NuGet can check an [Azure Key Vault](https://azure.microsoft.com/en-us/services/key-vault/) for credentials matching the `credentials=` key from the `<PackageSource>`.

Azure Key Vault is a tool that allows your application to store credentials for other services. More information can be found here:

[https://docs.microsoft.com/en-us/azure/key-vault/general/developers-guide](https://docs.microsoft.com/en-us/azure/key-vault/general/developers-guide)

Use the standard methods for authenticating your dev machine or CI/CD build machine to Azure Key Vault:

[https://docs.microsoft.com/en-us/azure/key-vault/general/authentication](https://docs.microsoft.com/en-us/azure/key-vault/general/authentication)

Once Azure Key Vault has authenticated, NuGet will retrieve the credentials from the vault and provide them to the authenticated feed.

## Credential Providers

The best method for authenticating to a feed is to use a credential provider from the service.

Instructions for supporting each credential provider can be found in that vendor's support materials.

Azure provides a credential provider for Azure Artifacts and other NuGet repositories through [NuGet Authenticate](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/package/nuget-authenticate?view=azure-devops).
