---
layout: guide
title: "Interactive Navigation Tree & Quick Controls"
description: "Configure your documentation page name in docs.config.ts and test the interactive quick control navigation tree."
category: "Getting Started"
difficulty: "Intermediate"
read_time: "3 min"
last_updated: "October 2026"
tags: ["Navigation Tree", "Quick Controls", "Config File", "Interactive Testing"]
---

# Interactive Navigation Tree & Quick Controls

Configure your documentation page name in docs.config.ts and test the interactive quick control navigation tree.

## Configuring Documentation Page Name in docs.config.ts

The documentation site name (previously hardcoded as DOCUCRAFT DOCS) is now fully user-configurable via `src/docs.config.ts`. You can customize the primary name, monogram/short abbreviation, tagline, header badge, and version string.

Changes to `docs.config.ts` automatically reflect across the top brand header, browser tab `<title>`, navigation sidebar, export scripts, and GitHub Pages deployments.

```typescript
import { DocsConfig } from './types/docs';

export const docsConfig: DocsConfig = {
  // Configurable documentation page name:
  name: 'DocuCraft Docs', // Change this to your custom site name!
  shortName: 'DC',        // 2-3 char logo monogram
  tagline: 'Enterprise Technical Guides',
  headerBadge: 'ENTERPRISE',
  version: 'v2.4 LTS',
  pageTitleTemplate: '%s – %name%',
  description: 'Enterprise technical documentation platform.',
  organization: 'Engineering Team',
  links: {
    github: 'https://github.com/my-org/docs',
  },
  // Quick Control Tree default options:
  navigationTree: {
    defaultExpanded: true,
    showCounters: true,
    showIcons: true,
    showBadges: true,
    compactDensity: false,
    quickFilterEnabled: true,
  },
};

export default docsConfig;
```

## Quick Control Tree Features

The Quick Control Tree navigation bar provides immediate control over complex documentation structures:

- **Expand / Collapse All**: Instantly toggle all category folder branches with one click.
- **Real-Time Topic Filtering**: Filter guides and subsections instantaneously without page reloads.
- **Compact vs Cozy Density**: Switch between comfortable reading mode and dense monorepo navigation.
- **Badge Toggles**: Toggle visibility of status and difficulty badges (CORE, REST, HOT, QUICKSTART).
- **Deep Navigation Rails**: Automatically reveal deep anchor links for the active guide.



## Interactive Tree Testing Playground

Use the interactive console below to test configuring the documentation title and exercising the quick control tree:


