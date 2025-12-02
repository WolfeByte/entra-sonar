
<img width="1056" height="112" alt="image" src="https://github.com/user-attachments/assets/daa080d4-fff8-4cf9-a8ab-a4e7854f5234" />

Have you ever tried to locate a Microsoft Entra ID tenant ID when all you have is a domain name? Maybe you already know the tenant ID and want to look the other way to see which domains are verified in that tenant. You may also want to identify the federated IdP a tenant uses, understand how each domain uses the various Microsoft services, or check the tenant’s location and any custom branding. You need all of this to work across public, national, and sovereign Microsoft cloud environments.

That is exactly what Entra Sonar handles. It searches for Entra tenants using either a domain or a tenant ID, and reveals verified domains, IdP's, and Microsoft cloud service usage across every Microsoft cloud environment.

**Site:** [https://entrasonar.com](https://entrasonar.com)

## Overview

Entra Sonar uses various techniques to pull together information about Microsoft Entra ID tenants:

- **Multi-Cloud Support**: Search and analyse tenants across Public Cloud, US Government, and China 21Vianet environments
- **Tenant Details**: Get information about the tenants location, cloud type, and IdP configuration
- **Federation Settings**: See detailed IdP information for each verified domain (Entra, AD FS, Okta, Ping, and others)
- **Custom Branding**: View any images and customisations applied to the tenant's sign-in page
- **Verified Domains**: Display all domains verified in the tenant
- **Domain Analysis**: Scan each verified domain and attempt to match them to Microsoft services
- **Azure AD B2C Detection**: Identify B2C tenants (though this can be a bit hit and miss)

## How It Works

### Cloud Environment Detection

Entra Sonar automatically identifies which Microsoft cloud environment hosts a tenant by analysing OpenID configuration endpoints.

Each Microsoft cloud environment uses distinct authentication endpoints:

<table>
  <tr>
    <th>Cloud Type</th>
    <th>Authentication Endpoint</th>
    <th>Data Location</th>
  </tr>
  <tr>
    <td>Public Cloud (M365 Commercial)</td>
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

Government cloud subtypes (GCC, GCC High, DoD) are distinguished by analysing `tenant_region_scope` and `tenant_region_sub_scope` metadata fields in the OpenID configuration response.

<img width="889" height="333" alt="image" src="https://github.com/user-attachments/assets/bb16780c-d976-40db-b43f-f2a79655a82d" />

### IdP Discovery
When you query a domain, Entra Sonar uses Microsoft's GetUserRealm endpoint to determine the authentication configuration. 

This endpoint returns a `NameSpaceType` value of either Managed or Federated:

1. **Managed Domain**: Authentication is handled directly by Entra (Password Hash Sync, Pass-through Authentication)
2. **Federated Domain**: Authentication is delegated to an external identity provider using WS-Fed, SAML, or OIDC

For federated domains, Entra Sonar will also retrieve the federation metadata endpoint and identify the IdP based on URL patterns unique to each provider. 

X.509 certificate details (validity dates, issuer, subject, key size) are parsed and displayed for federated domains.

<img width="888" height="568" alt="image" src="https://github.com/user-attachments/assets/2859902f-1999-4f09-8ce0-f76b0c488302" />

### Microsoft Service Usage Discovery

The **Detailed Domain Scan** feature performs DNS queries to identify Microsoft services attached to each verified domain:

- **Exchange Online**
- **SharePoint Online**
- **Teams**
- **Intune**
- **Azure**

For email-enabled domains, the scan checks:

- **MX Records:** Mail exchange server configuration
- **SPF (Sender Policy Framework):** Email sender authentication
- **DKIM (DomainKeys Identified Mail):** Email signature verification
- **DMARC (Domain-based Message Authentication):** Email policy enforcement

<img width="894" height="505" alt="image" src="https://github.com/user-attachments/assets/9083c9f9-6097-42bc-a384-6b619426bf57" />

### Custom Branding

If the tenant has custom branding in place, it will show up in the Custom Branding card, including any images and text.

<img width="894" height="1210" alt="image" src="https://github.com/user-attachments/assets/d425bfc2-71f1-4852-88f6-f974f42eea04" />

### Discussions

For feedback, ideas, or questions about Entra Sonar, please use [GitHub Discussions](https://github.com/WolfeByte/entra-sonar/discussions).

**Maintained by:** [Benjamin Wolfe](https://github.com/WolfeByte)

**Live Application:** [https://entrasonar.com](https://entrasonar.com)
