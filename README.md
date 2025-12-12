<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-Skills-5A67D8?style=for-the-badge&logo=anthropic&logoColor=white" alt="Claude Code Skills" />
</p>

<h1 align="center">Claude Content Skills</h1>

<p align="center">
  <img src="assets/hero-claude-skills.webp" alt="Claude Content Skills" width="600" />
</p>

<p align="center">
  <strong>Production-ready skills for dynamic content management, AI-assisted SEO optimization, and internal / external links management</strong>
</p>

<p align="center">
  <a href="https://github.com/dragosroua/claude-content-skills/stargazers"><img src="https://img.shields.io/github/stars/dragosroua/claude-content-skills?style=social" alt="GitHub Stars" /></a>
  <a href="https://github.com/sponsors/dragosroua"><img src="https://img.shields.io/badge/Sponsor-GitHub-ea4aaa?logo=github" alt="Sponsor" /></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="MIT License" /></a>
  <img src="https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white" alt="Python 3.10+" />
</p>

<p align="center">
  <a href="#-quick-start">Quick Start</a> |
  <a href="#-skills-overview">Skills</a> |
  <a href="docs/README.md">Documentation</a> |
  <a href="https://dragosroua.com">Blog</a>
</p>

---

> **Built with ADD-Supercharged Claude**
>
> These skills were developed using Claude Code enhanced with the [Assess-Decide-Do (ADD) Framework](https://github.com/dragosroua/claude-assess-decide-do-mega-prompt) - a human cognitive framework integration that creates more aligned, flow-aware AI interactions. The ADD framework taught Claude to recognize exploration vs. decision vs. execution phases, resulting in cleaner architecture, a more thoughtful implementation and significant token savings.

---

## What This Is

A collection of **five production-ready Claude Code skills** that automate / enhance common content management tasks:

| Skill | Purpose | Time Saved |
|-------|---------|------------|
| **SEO WordPress Manager** | Batch update Yoast SEO metadata via GraphQL | Hours per week |
| **Astro CTA Injector** | Intelligently inject CTAs into static site content | Manual work eliminated |
| **Link Analyzer** | Find broken links, orphan pages, and linking issues | Comprehensive site audits |
| **GSC Assistant** | Track indexing status, manage submissions, process false positives | Indexing workflow streamlined |
| **Pre-publish Post Assistant** | Classify posts with categories/tags, generate SEO metadata | Pre-publish workflow optimized |

These aren't toy examples. They emerged from real-world needs managing a 15+ years old, 1,300+ posts blog migration and optimization project.

## Why Skills Matter

Claude Code skills are **model-invoked capabilities** - Claude automatically detects when to use them based on your conversation context. Unlike slash commands that require explicit invocation, skills let Claude seamlessly apply specialized knowledge when relevant.

```
You: "My WordPress posts have terrible meta descriptions, can you help optimize them?"

Claude: [Automatically activates SEO WordPress Manager skill]
        "I'll help you batch-update your Yoast SEO metadata. Let me first
        fetch your posts via GraphQL and show you a preview of the changes..."
```

## Quick Start

### Installation (2 minutes)

```bash
# Clone the repository
git clone https://github.com/dragosroua/claude-content-skills.git
cd claude-content-skills

# Install Python dependencies
pip install -r requirements.txt

# Copy skills to Claude Code (global installation)
cp -r skills/* ~/.claude/skills/
cp -r shared ~/.claude/skills/
```

### Configuration

```bash
# Copy example configs
cp .env.example .env

# Edit with your credentials (only needed for skills you use)
# - WordPress GraphQL URL and Application Password (SEO WordPress Manager)
# - Content path for your Astro site (CTA Injector)
# - Dist folder path for built site (Link Analyzer - works on local files)
```

### Verify Installation

Start Claude Code - the skills should now appear when relevant to your requests.

## Skills Overview

### 1. SEO WordPress Manager

Batch update Yoast SEO fields (titles, meta descriptions, focus keyphrases) via WordPress GraphQL API.

**Key Features:**
- Preview changes before applying (dry-run by default)
- Progress tracking with resume capability
- Batch processing with rate limiting
- Backup of original values

**Requirements:**
- WordPress with WPGraphQL plugin
- Yoast SEO + WPGraphQL Yoast extension
- Application Password for authentication

**Example Usage:**
```
"Update meta descriptions for all posts in the tutorials category"
"Fix SEO titles that are too long"
"Add focus keyphrases to posts missing them"
```

[Full Documentation](docs/skills/SEO_WORDPRESS_MANAGER.md)

---

### 2. Astro CTA Injector

Inject Call-to-Action blocks into Astro site content with intelligent placement and relevance scoring.

**Key Features:**
- Multiple placement strategies (end, after 50%, after 60%, after heading)
- Content-based relevance scoring
- Template system for CTA HTML
- Backup and rollback capability

**Placement Strategies:**
| Strategy | Best For |
|----------|----------|
| `end` | Non-intrusive, general CTAs |
| `after-paragraph-50%` | Newsletter signups (highest conversion) |
| `after-paragraph-60%` | Product recommendations |
| `after-heading` | Important announcements |

**Example Usage:**
```
"Add newsletter CTAs to my productivity articles"
"Inject product promotion into posts scoring above 7"
"Preview CTA placements before applying"
```

[Full Documentation](docs/skills/ASTRO_CTA_INJECTOR.md)

---

### 3. Link Analyzer

Comprehensive link analysis for static sites - find broken links, orphan pages, and internal linking issues.

**Key Features:**
- Internal/external link extraction and validation
- HTTP checking with false-positive filtering
- **Link graph metrics** (the real value):
  - Orphan pages (zero inbound links - critical SEO issue)
  - Under-linked pages (missed ranking opportunities)
  - Over-linked pages (link equity dilution)
  - Link sinks (receive but don't pass links)

**Example Usage:**
```
"Analyze my site's internal linking structure"
"Find orphan pages that aren't linked from anywhere"
"Check for broken external links"
"Show me pages that receive links but don't pass them"
```

[Full Documentation](docs/skills/LINK_ANALYZER.md)

---

### 4. GSC Assistant

Track Google Search Console indexing status, manage submission workflows, and process false positives.

**Key Features:**
- Compare sitemap against GSC indexed pages export
- Track two index states: Public (GSC export) and Lag (URL Inspection confirmed)
- Prioritize pages for submission by category
- Process false positives (move to indexed when confirmed)
- Track submission dates and progress

**Tracking Files:**
| File | Purpose |
|------|---------|
| `indexed.md` | All indexed pages (Public + Lag tables) |
| `to-index.md` | Pages awaiting indexing, prioritized |

**Example Usage:**
```
"Compare my sitemap with GSC export and generate tracking files"
"Process false positives and update indexed list"
"Show indexing progress statistics"
```

[Full Documentation](docs/skills/GSC_ASSISTANT.md)

---

### 5. Pre-publish Post Assistant

Intelligent pre-publish assistant for new blog posts. Suggests categories, tags, and SEO metadata with transparent rationale.

**Key Features:**
- Suggest categories from existing taxonomy (1-2 max)
- Suggest tags from existing tags (3-5 max, no pollution)
- Generate SEO title, meta description, focus keyphrase
- Show rationale for every suggestion
- Distribution-aware (avoids oversized categories, orphan tags)

**Best Practices Enforced:**
| Practice | Description |
|----------|-------------|
| No tag pollution | Only suggests existing tags with 3+ posts |
| Balanced distribution | Warns if category would become oversized |
| SEO compliance | Enforces character limits (60/160) |
| Transparent reasoning | Every suggestion includes rationale |

**Example Usage:**
```
"Classify this post: /drafts/morning-routine.md"
"What tags should I use for a post about Bitcoin ETFs?"
"Generate SEO metadata for my productivity article"
```

[Full Documentation](docs/skills/PRE_PUBLISH_POST_ASSISTANT.md)

---

## Repository Structure

```
claude-content-skills/
├── README.md                    # You are here
├── LICENSE                      # MIT License
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment template
│
├── docs/                        # Documentation
│   ├── README.md               # Documentation index
│   ├── skills/                 # Per-skill deep dives
│   ├── integration/            # Setup and troubleshooting
│   └── philosophy/             # Design decisions
│
├── shared/                      # Shared utilities
│   ├── config_loader.py        # Configuration management
│   └── utils.py                # Progress tracking, JSON helpers
│
└── skills/                      # The skills themselves
    ├── seo-wordpress-manager/
    │   ├── SKILL.md            # Skill definition (required)
    │   ├── reference.md        # Detailed reference
    │   ├── config.example.json
    │   └── scripts/
    │       ├── wp_graphql_client.py
    │       ├── preview_changes.py
    │       └── yoast_batch_updater.py
    │
    ├── astro-cta-injector/
    │   ├── SKILL.md
    │   ├── reference.md
    │   ├── config.example.json
    │   ├── templates/
    │   │   ├── newsletter.html
    │   │   └── product.html
    │   └── scripts/
    │       ├── score_posts.py
    │       ├── preview_injection.py
    │       └── inject_ctas.py
    │
    ├── link-analyzer/
    │   ├── SKILL.md
    │   ├── reference.md
    │   ├── config.example.json
    │   └── scripts/
    │       ├── analyze.py          # Main orchestrator
    │       ├── outbound_links.py
    │       ├── internal_links.py
    │       ├── http_checker.py
    │       └── link_graph.py       # Orphans, sinks, metrics
    │
    ├── gsc-assistant/
    │   └── SKILL.md                # Skill definition
    │
    └── pre-publish-post-assistant/
        └── SKILL.md                # Skill definition
```

## Requirements

| Requirement | Version | Notes |
|-------------|---------|-------|
| Python | 3.10+ | For running analysis scripts |
| Claude Code | Latest | CLI tool from Anthropic |
| WordPress | 5.0+ | With WPGraphQL (for SEO skill) |
| Yoast SEO | Latest | With GraphQL extension (for SEO skill) |

## How Skills Work

Each skill has a `SKILL.md` file with YAML frontmatter that tells Claude:

```yaml
---
name: link-analyzer
description: Comprehensive link analysis for static sites. Use when the user
  wants to analyze links, find broken links, identify orphan pages...
---
```

Claude reads these descriptions and automatically activates the skill when your request matches. The skill's documentation and scripts then guide Claude's responses.

## Contributing

Contributions welcome! Areas of interest:

- [ ] Additional CTA placement strategies
- [ ] More link graph visualizations
- [ ] WordPress REST API alternative to GraphQL
- [ ] Support for other static site generators (Hugo, Jekyll)

## Related Projects

- **[ADD Framework Mega-Prompt](https://github.com/dragosroua/claude-assess-decide-do-mega-prompt)** - The cognitive framework used to develop these skills
- **[addTaskManager](https://addtaskmanager.com)** - iOS/macOS productivity app built on ADD principles

## Author

**Dragos Roua**

- Blog: [dragosroua.com](https://dragosroua.com)
- GitHub: [@dragosroua](https://github.com/dragosroua)
- Twitter: [@dragosroua](https://twitter.com/dragosroua)

## Support

If these skills save you time, consider:

- Starring the repository
- [Sponsoring on GitHub](https://github.com/sponsors/dragosroua)
- Sharing with others who manage content at scale

## License

MIT License - see [LICENSE](LICENSE) for details.

---

<p align="center">
  <sub>Built with cognitive alignment using the <a href="https://github.com/dragosroua/claude-assess-decide-do-mega-prompt">ADD Framework</a></sub>
</p>
