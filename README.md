<p align="center">
  <img width="482" height="108" alt="image" src="https://github.com/user-attachments/assets/83a56d58-64c7-44b7-ad6a-31219af253a0" />
</p>

# Entra Sonar

Search for Entra tenants using a verified domain or tenant ID and get information about all verified domains in the tenant including federated IdP information and Microsoft cloud service usage. Works across both public, national, and sovereign Microsoft clouds.

**Live Application:** [https://entrasonar.com](https://entrasonar.com)

## Overview

Using various search techniques, Entra Sonar will retrieve information about certain aspects of the tenant:

- **Multi-Cloud Support**: Search and analyse tenants across Public Cloud, US Government (GCC/GCC High/DoD), and China 21Vianet environments
- **Tenant Details**: Provides information about the tenants location, cloud type, verified domains, and any custom branding 
- **Federation Settings**: Identifies which identity provider (IdP) manages authentication for each verified domain (AD FS, Okta, Ping, etc.)
- **Azure AD B2C Detection**: Attempts to identify B2C tenants with a certain degree of accuracy (its flakey).
- **Domain Analysis**:  Perform a scan against each verified domain in the tenant and attempts to match these to any Microsoft services, such as Office 365, Azure, Intune.

## How It Works

### Cloud Environment Detection

Entra Sonar automatically identifies which Microsoft cloud environment hosts a tenant by analysing OpenID configuration endpoints. Each cloud environment uses distinct authentication endpoints:

| Cloud Type | Authentication Endpoint | Data Location |
|------------|------------------------|---------------|
| Public Cloud | `login.microsoftonline.com` | Worldwide, or geo-located in certain regions for data residency |
| US Government (GCC/GCC High/DoD) | `login.microsoftonline.us` | United States only, secret squirel stuff |
| China 21Vianet | `login.partner.microsoftonline.cn` | China only, operated by 21Vianet |

### Federation Discovery

When you query a domain, Entra Sonar uses Microsoft's `GetUserRealm` API to determine authentication configuration:

1. **Managed Domain**: Authentication is handled directly by Entra
2. **Federated Domain**: Authentication is delegated to an external identity provider

For federated domains, Entra Sonar retrieves the federation metadata endpoint and analyses the IdP configuration to identify the IdP based on URL patterns and protocol support.

### Report a Bug

Found an issue with Entra Sonar? Please [open a bug report](https://github.com/WolfeByte/entra-sonar/issues/new?template=bug_report.md) using the bug report template. Include:

- Clear description of the issue
- Steps to reproduce
- Expected vs. actual behavior
- Domain or tenant ID (if safe to share publicly)

### Request a Feature

Have an idea for improving Entra Sonar? Please [open a feature request](https://github.com/WolfeByte/entra-sonar/issues/new?template=feature_request.md) using the feature request template. Describe:

- The feature or enhancement
- Your use case
- Expected behavior
- Any additional context or examples

### General Questions

For general questions or discussions about Entra Sonar, please use [GitHub Discussions](https://github.com/WolfeByte/entra-sonar/discussions).

## Privacy & Security

### Data Processing

Entra Sonar processes the following information when you submit a query:

- **Domain names**: Used to query Microsoft GetUserRealm and OpenID configuration endpoints
- **Tenant IDs**: Used to query Microsoft Graph API for tenant information

### What is NOT Stored

- **No user credentials**: Entra Sonar does not require or store any user authentication credentials
- **No query logs**: Queries are not logged or stored persistently
- **No personal information**: No personal data is collected from users

**Maintained by:** [Benjamin Wolfe](https://github.com/WolfeByte)

**Live Application:** [https://entrasonar.com](https://entrasonar.com)
