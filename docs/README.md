# Documentation

Welcome to the Claude Content Skills documentation for WordPress/WooCommerce.

## Quick Navigation

### Getting Started
- [Installation Guide](integration/SETUP.md) - Step-by-step installation
- [Configuration](integration/CONFIGURATION.md) - Setting up credentials and options
- [Troubleshooting](integration/TROUBLESHOOTING.md) - Common issues and solutions

### Skills Documentation
- [SEO WordPress Manager](skills/SEO_WORDPRESS_MANAGER.md) - Batch Rank Math SEO updates for posts and WooCommerce products
- [Link Analyzer](skills/LINK_ANALYZER.md) - Comprehensive link analysis

---

## About Claude Code Skills

Claude Code skills are a mechanism for extending Claude's capabilities in specific domains. Unlike slash commands (which require explicit user invocation), skills are **model-invoked** - Claude automatically detects when to use them based on conversation context.

### Skill Structure

Each skill follows this structure:

```
skill-name/
├── SKILL.md           # Required: Skill definition with YAML frontmatter
├── reference.md       # Optional: Detailed reference documentation
├── config.example.json # Optional: Configuration template
└── scripts/           # Optional: Supporting scripts
```

The `SKILL.md` file is the entry point:

```yaml
---
name: skill-name
description: Description that helps Claude know when to use this skill.
  Include trigger phrases and use cases.
---

# Skill Documentation

Detailed instructions for Claude when this skill is active...
```

### How Claude Uses Skills

1. User makes a request
2. Claude scans available skills' descriptions
3. If a skill matches the request context, Claude activates it
4. Claude follows the skill's documentation to respond appropriately
5. Claude uses the skill's scripts and tools as needed

---

## WordPress/WooCommerce Focus

These skills are designed for WordPress/WooCommerce sites with:
- **Rank Math SEO** plugin
- **WPGraphQL** for API access
- Compatible with **Shoptimizer theme** and **CommerceKit**

### Key Capabilities

| Skill | Capability |
|-------|------------|
| SEO WordPress Manager | Batch update SEO titles, descriptions, focus keywords for posts and products |
| Link Analyzer | Find broken links, orphan pages, internal linking issues |

---

## Development Philosophy

### Assessment-First Design
Each skill includes preview/dry-run modes. You always see what will happen before it happens.

### Clear Decision Points
Skills present explicit choices (batch size, thresholds) rather than making assumptions.

### Execution Safety
Skills include progress tracking, backups, and resume capability. Interruptions don't mean starting over.

---

## Contributing to Documentation

Documentation improvements are always welcome. When contributing:

1. Match the existing tone (direct, practical, no fluff)
2. Include real examples where possible
3. Document edge cases you discover
