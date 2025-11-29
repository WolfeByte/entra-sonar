
<img width="1056" height="112" alt="image" src="https://github.com/user-attachments/assets/daa080d4-fff8-4cf9-a8ab-a4e7854f5234" />

Search for Entra tenants using a domain or tenant ID and get information about all verified domains in the tenant including federated IdP information and Microsoft cloud service usage. Works across both public, national, and sovereign Microsoft clouds.

**Site:** [https://entrasonar.com](https://entrasonar.com)

## Overview

Using various search techniques, Entra Sonar will retrieve information about certain aspects of the tenant:

- **Multi-Cloud Support**: Search and analyse tenants across Public Cloud, US Government, and China 21Vianet environments
- **Tenant Details**: Provides information about the tenants location, cloud type, IdP
- **Federation Settings**: Identifies which identity provider (IdP) manages authentication for each verified domain (AD FS, Okta, Ping, etc.)
- **Custom Branding**: Displays any images and other customisations made to the sign-in page of the tenant
- **Verified Domains**: Displays the domains that are verified in the tenant
- **Domain Analysis**:  Performs a scan against each verified domain and attempts to match these to any Microsoft services.
- **Azure AD B2C Detection**: Attempts to identify B2C tenants with a certain degree of accuracy (its flakey).

## How It Works

### Cloud Environment Detection

Entra Sonar automatically identifies which Microsoft cloud environment hosts a tenant by analysing OpenID configuration endpoints. Each cloud environment uses distinct authentication endpoints:

<table>
  <tr>
    <th>Cloud Type</th>
    <th>Authentication Endpoint</th>
    <th>Data Location</th>
  </tr>
  <tr>
    <td>Public Cloud</td>
    <td><code>login.microsoftonline.com</code></td>
    <td style="white-space: nowrap;">Replicated Worldwide, or geo-located in certain regions</td>
  </tr>
  <tr>
    <td>US Government (GCC, GCC High, DoD)</td>
    <td><code>login.microsoftonline.us</code></td>
    <td style="white-space: nowrap;">United States only, secret squirrel stuff </td>
  </tr>
  <tr>
    <td>China 21Vianet</td>
    <td><code>login.partner.microsoftonline.cn</code></td>
    <td style="white-space: nowrap;">China only</td>
  </tr>
</table>

Government cloud subtypes (GCC, GCC High, DoD) are distinguished by analysing tenant_region_scope and tenant_region_sub_scope metadata fields in the OpenID configuration response.

### IdP Discovery

When you query a domain, Entra Sonar uses Microsoft's `GetUserRealm` endpoint at https://login.microsoftonline.com/GetUserRealm.srf?login=foo@contoso.com&extended=1 to determine the authentication configuration of the domain, which is returned in the `NameSpaceType` object, and will contain a value of either `Managed` or `Federated`.

1. **Managed Domain**: Authentication is handled directly by Entra (Password Hash Sync, Pass-through Authentication)
2. **Federated Domain**: Authentication is delegated to an external identity provider using WS-Fed, SAML, or OIDC

For federated domains, Entra Sonar retrieves the federation metadata endpoint and analyses the configuration to identify the IdP based on specific URL patterns unique to each IdP.

X.509 certificate details (validity dates, issuer, subject, key size) are parsed and displayed for federated configurations.

### Verified Domains
Up until recent change, it was possible to query all verified domains that were present in a tenant via the Exchange Online federation endpoint used by AutoDiscover. This is no longer possible, so as we a workaround I am using Micah Van Deusen API at https://micahvandeusen.com/tools/tenant-domains/ to retreive these domains, but there are some limitations, such as subdomains not being returned, or not all domains being listed. 

Credit to **Micah Van Deusen** for creating this API https://github.com/micahvandeusen

**Note:** Domain enumeration when searching using a tenant ID is limited to tenants in the public cloud only. For US Government and China clouds, it is not possible to search by tenant ID and find what domains are verified in the tenant, you will need to know a domain name to find that out.

### Microsoft Service Usage Discovery

The **Detailed Domain Scan** feature performs DNS queries to identify Microsoft services attached to verified domains:

Exchange Online\
SharePoint Online\
Teams\
Intune\
Azure

For email-enabled domains, the scan checks:

MX Records: Mail exchange server configuration\
SPF (Sender Policy Framework): Email sender authentication\
DKIM (DomainKeys Identified Mail): Email signature verification\
DMARC (Domain-based Message Authentication): Email policy enforcement

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

**Maintained by:** [Benjamin Wolfe](https://github.com/WolfeByte)

**Live Application:** [https://entrasonar.com](https://entrasonar.com)
