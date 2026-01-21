# Power Pages Site Builder Plugin (v3)

Create and deploy Power Pages sites using modern development approaches.

**Currently supported**: Code Sites (SPAs) with React, Angular, Vue, or Astro
**Coming soon**: Standard sites, Enhanced data model sites, Templates

## Overview

This plugin enables makers to:

1. **Create** a Power Pages code site using their preferred frontend framework
2. **Design** production-grade UI with the frontend-design skill
3. **Upload** the site to Power Pages using PAC CLI
4. **Activate** the website via Power Pages REST API
5. **Verify** deployment and get the live URL

## Quick Start

Run the skill:
```
/create-code-site
```

The skill will guide you through:
- Choosing a frontend framework (React, Angular, Vue, Astro)
- Defining site features and design preferences
- Building and deploying the site

## Prerequisites

- **PAC CLI** (v1.44+) installed and authenticated
- **Azure CLI** installed for API authentication
- **Node.js** (v18+) for building frontend projects
- Power Pages environment with admin privileges

## Workflow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  1. Design  │ ──▶ │  2. Build   │ ──▶ │  3. Upload  │ ──▶ │ 4. Activate │
│   & Create  │     │   Project   │     │  (Inactive) │     │  (Go Live)  │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

## Available Skills

| Skill | Description |
|-------|-------------|
| `create-code-site` | Full workflow to create, upload, and activate a Power Pages code site |

## Supported Frameworks

| Framework | Recommended For | Build Output |
|-----------|-----------------|--------------|
| **React** (Recommended) | Complex interactive UIs | `/build` |
| **Angular** | Enterprise applications | `/dist/<project>` |
| **Vue** | Balanced simplicity/power | `/dist` |
| **Astro** | Content-focused sites | `/dist` |

## Key Commands

### PAC CLI - Upload Code Site
```bash
pac pages upload-code-site \
  --rootPath "./my-site" \
  --compiledPath "./build" \
  --siteName "My Site"
```

### Power Pages API - Activate Website
```
POST https://api.powerplatform.com/powerpages/environments/{envId}/websites
```

### Power Pages API - Check Status
```
GET https://api.powerplatform.com/powerpages/environments/{envId}/websites
```

## Next Steps After Deployment

After your site is live, consider:

1. **Web API Integration** - Connect to Dataverse data
2. **Authentication** - Enable Microsoft Entra ID sign-in
3. **Table Permissions** - Configure data access security

## Documentation

- [Power Pages Code Sites](https://learn.microsoft.com/en-us/power-pages/configure/create-code-sites)
- [Power Pages REST API](https://learn.microsoft.com/en-us/rest/api/power-platform/powerpages/websites)
- [PAC CLI Reference](https://learn.microsoft.com/en-us/power-platform/developer/cli/reference/pages)

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit [Contributor License Agreements](https://cla.opensource.microsoft.com).

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft
trademarks or logos is subject to and must follow
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.

