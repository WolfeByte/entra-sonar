# Entra Sonar

Search for Entra tenants using a verified domain or tenant ID and get information about all verified domains in the tenant including federated IdP information and Microsoft cloud service usage. Works across both public, national, and sovereign Microsoft clouds.

**Live Application:** [https://entrasonar.com](https://entrasonar.com)

## Overview

Managing and securing Microsoft Entra ID (formerly Azure AD) environments requires understanding domain federation configurations, tenant settings, and identity provider relationships. Whether you're conducting security assessments, planning domain migrations, or troubleshooting authentication issues, you need accurate, real-time information about how domains are configured.

Entra Sonar helps security professionals, IT administrators, and identity architects investigate Microsoft Entra ID domains and tenant IDs to retrieve:

- **Federation Settings**: Identify which identity provider (IdP) manages authentication (AD FS, Okta, Ping, Auth0, etc.)
- **Tenant Information**: Retrieve tenant details across Public Cloud, US Government, and China 21Vianet environments
- **Certificate Details**: Parse and analyze X.509 certificates for federated domains with expiration monitoring
- **Azure AD B2C Detection**: Automatic identification of legacy B2C tenants with 100% accuracy
- **Domain Verification Status**: Comprehensive enumeration of verified and unverified domains

## Key Capabilities

- **Multi-Cloud Support**: Analyze tenants across Public Cloud, US Government (GCC/GCC High/DoD), and China 21Vianet environments
- **Federation Analysis**: Automatic detection and classification of identity providers including AD FS, Okta, Ping Identity, Auth0, Shibboleth, and others
- **Azure AD B2C Detection**: Identifies legacy B2C (Customer Identity Access Management) tenants using `.b2clogin.com` subdomain probing with 100% accuracy
- **X.509 Certificate Parsing**: Comprehensive certificate analysis including issuer, subject, validity periods, and cryptographic details
- **Bulk Processing**: CSV import/export for analyzing multiple domains efficiently
- **Real-Time Monitoring**: Live Graph API pool health dashboard showing tenant usage and throttle protection
- **Master-Detail Interface**: Efficient results browsing with expandable sections for detailed information

## How It Works

### Cloud Environment Detection

Entra Sonar automatically identifies which Microsoft cloud environment hosts a tenant by analyzing OpenID configuration endpoints. Each cloud environment uses distinct authentication endpoints:

| Cloud Type | Authentication Endpoint | Data Location |
|------------|------------------------|---------------|
| Public Cloud | `login.microsoftonline.com` | Worldwide, geo-located in target regions |
| US Government (GCC/GCC High/DoD) | `login.microsoftonline.us` | United States only, compliance requirements |
| China 21Vianet | `login.partner.microsoftonline.cn` | China only, operated by 21Vianet |

### Federation Discovery

When you query a domain, Entra Sonar uses Microsoft's `GetUserRealm` API to determine authentication configuration:

1. **Managed Domains**: Authentication handled directly by Microsoft Entra ID
2. **Federated Domains**: Authentication delegated to external identity providers

For federated domains, Entra Sonar retrieves the federation metadata endpoint and analyzes the IdP configuration to identify the provider (AD FS, Okta, Ping, etc.) based on URL patterns and protocol support.

### Multi-Tenant Graph API Pool

To protect against Microsoft Graph API throttling, Entra Sonar uses a multi-tenant client pool with round-robin load balancing. When retrieving tenant information:

- Multiple Azure service principals distribute API calls across different rate limit quotas
- Automatic failover if a client encounters throttling or errors
- Real-time health monitoring available at `/pool-monitor` on the live application

### Azure AD B2C Detection

Every Azure AD B2C tenant automatically receives a dedicated `.b2clogin.com` subdomain during provisioning. Entra Sonar probes this subdomain to determine if a tenant is B2C:

**Detection Method:**
- Checks if `https://{tenant}.b2clogin.com/` responds
- If subdomain exists (any HTTP status), it's a B2C tenant
- If timeout or DNS error occurs, it's not a B2C tenant

This method achieves 100% detection accuracy compared to the previous policy-probing approach (37.5% accuracy) because the subdomain exists for all B2C tenants regardless of policy configuration or custom domains.

### Certificate Parsing

For federated domains, Entra Sonar retrieves X.509 certificates from federation metadata and parses:

- **Certificate Validity**: Not-before and not-after dates for expiration monitoring
- **Issuer Information**: Certificate authority that issued the certificate
- **Subject Details**: Common name (CN) and organizational information
- **Public Key**: Algorithm (RSA, ECDSA) and key size
- **Signature**: Cryptographic signature algorithm

All certificate parsing occurs server-side using industry-standard PKI.js and ASN.1.js libraries.

## Supported Cloud Environments

Entra Sonar supports all Microsoft cloud environments with accurate nomenclature:

### Public Cloud (Worldwide)

**Endpoints:**
- Authentication: `login.microsoftonline.com`
- Microsoft Graph: `graph.microsoft.com`

**Characteristics:**
- Available globally across all regions (Africa, Asia, Europe, Japan, North America, Oceania, South America)
- Data location: Geo-located (stored at rest in target region) or worldwide
- Full Microsoft Graph API support

### US Government Clouds

**Endpoints:**
- Authentication: `login.microsoftonline.us`
- Microsoft Graph: `graph.microsoft.us`

**Sub-Types:**
- **GCC (Government Community Cloud)**: For federal, state, and local government entities
- **GCC High**: For DoD contractors with controlled unclassified information (CUI) or ITAR data
- **DoD (Department of Defense)**: Exclusively for US Department of Defense entities

**Characteristics:**
- Data location: At rest in the United States, no exceptions
- Compliance requirements: FedRAMP High, DFARS, ITAR

### China 21Vianet Cloud

**Endpoints:**
- Authentication: `login.partner.microsoftonline.cn`
- Microsoft Graph: `microsoftgraph.chinacloudapi.cn`

**Characteristics:**
- Operated by: 21Vianet (local Chinese partner, not Microsoft directly)
- Data location: At rest in China, no exceptions
- Separate identity system from other clouds

## Common Use Cases

### Security Assessments and Domain Reconnaissance

Identify federation configurations and external identity providers as part of security assessments. Understand which domains authenticate through external systems and evaluate certificate expiration risks.

### Pre-Migration Domain Analysis

Before migrating domains or consolidating tenants, analyze current federation configurations, verified domain lists, and identity provider relationships to plan migration paths.

### Federation Configuration Troubleshooting

Diagnose authentication issues by verifying federation metadata endpoints, certificate validity, and protocol support (WS-Federation, SAML-P, WS-Trust).

### Certificate Expiration Monitoring

Track X.509 certificate expiration dates for federated domains to prevent authentication outages caused by expired certificates.

### B2C Tenant Identification

Identify Azure AD B2C (legacy CIAM) tenants in your environment. Azure AD B2C is being deprecated (unavailable for new customers as of May 1, 2025) in favor of External ID for Customers.

## Providing Feedback

This is a community feedback repository for Entra Sonar. The source code is maintained separately.

### Report a Bug

Found an issue with Entra Sonar? Please [open a bug report](https://github.com/WolfeByte/entra-sonar/issues/new?template=bug_report.md) using the bug report template. Include:

- Clear description of the issue
- Steps to reproduce
- Expected vs. actual behavior
- Environment details (browser, operating system)
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

### Security Measures

- **Server-side processing**: All API calls and certificate parsing occur server-side, not in the browser
- **Rate limiting**: Per-IP rate limiting prevents abuse and ensures service availability
- **Secure communication**: All external API calls use HTTPS with certificate validation
- **Request timeouts**: All external requests have 10-second timeouts to prevent hanging requests

## Recent Updates

**Current Version:** v0.4.0

### Highlights

- **100% B2C Detection**: New `.b2clogin.com` subdomain probing achieves perfect B2C tenant detection accuracy (up from 37.5%)
- **Faster Detection**: 50% faster than previous policy-probing method (~200-300ms vs ~500ms)
- **Monitoring Dashboard**: Real-time Graph API pool health monitoring at `/pool-monitor`
- **Performance Optimizations**: Request deduplication reduces external API calls by 60%

For complete version history, see [CHANGELOG.md](./CHANGELOG.md).

## License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

Copyright (c) 2025 Ben Wolfe

## Acknowledgments

Entra Sonar relies on the following Microsoft services and APIs:

- **Microsoft Graph API**: Tenant information retrieval
- **Microsoft GetUserRealm API**: Federation configuration discovery
- **Microsoft OpenID Configuration**: Cloud environment detection
- **Azure Identity**: Authentication for Graph API

### References

- [Microsoft Entra ID Documentation](https://learn.microsoft.com/en-us/entra/identity/)
- [Microsoft Graph API Reference](https://learn.microsoft.com/en-us/graph/api/overview)
- [Federation Protocol Documentation](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-saml-single-sign-on)
- [Azure AD B2C Migration Guide](https://learn.microsoft.com/en-us/entra/external-id/customers/how-to-migrate-customers-ciam-azure-ad-b2c)

---

**Maintained by:** [Ben Wolfe](https://github.com/WolfeByte)

**Live Application:** [https://entrasonar.com](https://entrasonar.com)
