<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-Skills-5A67D8?style=for-the-badge&logo=anthropic&logoColor=white" alt="Claude Code Skills" />
</p>

<h1 align="center">Claude Content Skills</h1>

<p align="center">
  <strong>Production-ready skills for WordPress/WooCommerce SEO optimization and link management with Rank Math SEO</strong>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="MIT License" /></a>
  <img src="https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white" alt="Python 3.10+" />
  <img src="https://img.shields.io/badge/WordPress-6.0+-21759B?logo=wordpress&logoColor=white" alt="WordPress 6.0+" />
  <img src="https://img.shields.io/badge/WooCommerce-Compatible-96588A?logo=woocommerce&logoColor=white" alt="WooCommerce Compatible" />
</p>

---

## What This Is

A collection of **production-ready Claude Code skills** for WordPress/WooCommerce sites using Rank Math SEO:

| Skill | Purpose | Time Saved |
|-------|---------|------------|
| **SEO WordPress Manager** | Batch update Rank Math SEO metadata via GraphQL | Hours per week |
| **Link Analyzer** | Find broken links, orphan pages, and linking issues | Comprehensive site audits |

These skills are optimized for **WordPress + WooCommerce** sites running themes like **Shoptimizer** with **CommerceKit**.

## Why Skills Matter

Claude Code skills are **model-invoked capabilities** - Claude automatically detects when to use them based on your conversation context.

```
You: "My WooCommerce product descriptions have terrible meta descriptions, can you help optimize them?"

Claude: [Automatically activates SEO WordPress Manager skill]
        "I'll help you batch-update your Rank Math SEO metadata for products. Let me first
        fetch your products via GraphQL and show you a preview of the changes..."
```

## Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/your-repo/claude-wpseo-skills.git
cd claude-wpseo-skills

# Install Python dependencies
pip install -r requirements.txt

# Copy skills to Claude Code (global installation)
cp -r skills/* ~/.claude/skills/
cp -r shared ~/.claude/skills/
```

### Configuration

```bash
# Copy example config
cp .env.example .env

# Edit with your WordPress credentials
WP_GRAPHQL_URL=https://your-site.com/graphql
WP_USERNAME=your-username
WP_APP_PASSWORD=xxxx xxxx xxxx xxxx
```

## Skills Overview

### 1. SEO WordPress Manager

Batch update Rank Math SEO fields (titles, meta descriptions, focus keyphrases) for posts and WooCommerce products via WordPress GraphQL API.

**Key Features:**
- Support for both **posts** and **WooCommerce products**
- Preview changes before applying (dry-run by default)
- Progress tracking with resume capability
- Batch processing with rate limiting
- Backup of original values
- SEO score tracking

**Requirements:**
- WordPress 6.0+ with WPGraphQL plugin
- Rank Math SEO + WPGraphQL for Rank Math extension
- WPGraphQL WooCommerce (for product support)
- Application Password for authentication

**Example Usage:**
```
"Update meta descriptions for all products in the electronics category"
"Fix SEO titles that are too long"
"Add focus keyphrases to posts missing them"
"Analyze SEO scores for all WooCommerce products"
```

[Full Documentation](docs/skills/SEO_WORDPRESS_MANAGER.md)

---

### 2. Link Analyzer

Comprehensive link analysis for WordPress sites - find broken links, orphan pages, and internal linking issues.

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

## WordPress Setup

### Required Plugins

| Plugin | Purpose | Download |
|--------|---------|----------|
| WPGraphQL | GraphQL API for WordPress | [wordpress.org](https://wordpress.org/plugins/wp-graphql/) |
| Rank Math SEO | SEO plugin | [wordpress.org](https://wordpress.org/plugins/seo-by-rank-math/) |
| WPGraphQL for Rank Math | GraphQL + Rank Math integration | [GitHub](https://github.com/AxeWP/wp-graphql-rank-math) |
| WPGraphQL WooCommerce | GraphQL + WooCommerce | [GitHub](https://github.com/wp-graphql/wp-graphql-woocommerce) |

### Enable Mutations

Add to your theme's `functions.php`:

```php
add_action('graphql_register_types', function() {
    register_graphql_mutation('updatePostSeo', [
        'inputFields' => [
            'postId' => ['type' => 'Int'],
            'title' => ['type' => 'String'],
            'description' => ['type' => 'String'],
            'focusKeyword' => ['type' => 'String'],
        ],
        'outputFields' => [
            'success' => ['type' => 'Boolean'],
            'post' => ['type' => 'Post'],
        ],
        'mutateAndGetPayload' => function($input) {
            $post_id = absint($input['postId']);

            if (!current_user_can('edit_post', $post_id)) {
                throw new \GraphQL\Error\UserError('Permission denied');
            }

            if (isset($input['title'])) {
                update_post_meta($post_id, 'rank_math_title',
                    sanitize_text_field($input['title']));
            }
            if (isset($input['description'])) {
                update_post_meta($post_id, 'rank_math_description',
                    sanitize_textarea_field($input['description']));
            }
            if (isset($input['focusKeyword'])) {
                update_post_meta($post_id, 'rank_math_focus_keyword',
                    sanitize_text_field($input['focusKeyword']));
            }

            return ['success' => true, 'post' => get_post($post_id)];
        }
    ]);
});
```

### Create Application Password

1. WordPress Admin → Users → Your Profile
2. Scroll to "Application Passwords"
3. Name: "Claude SEO Manager"
4. Click "Add New Application Password"
5. Copy the generated password (shown once!)

## Repository Structure

```
claude-wpseo-skills/
├── README.md                    # You are here
├── LICENSE                      # MIT License
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment template
│
├── docs/                        # Documentation
│   ├── skills/                 # Per-skill deep dives
│   └── integration/            # Setup and troubleshooting
│
├── shared/                      # Shared utilities
│   ├── config_loader.py        # Configuration management
│   └── utils.py                # Progress tracking, JSON helpers
│
└── skills/                      # The skills themselves
    ├── seo-wordpress-manager/
    │   ├── SKILL.md            # Skill definition
    │   ├── reference.md        # Rank Math fields reference
    │   ├── config.example.json
    │   └── scripts/
    │       ├── wp_graphql_client.py
    │       ├── analyze_seo.py
    │       ├── preview_changes.py
    │       └── rankmath_batch_updater.py
    │
    └── link-analyzer/
        ├── SKILL.md
        ├── reference.md
        └── scripts/
            ├── analyze.py
            ├── outbound_links.py
            ├── internal_links.py
            └── link_graph.py
```

## Requirements

| Requirement | Version | Notes |
|-------------|---------|-------|
| Python | 3.10+ | For running analysis scripts |
| Claude Code | Latest | CLI tool from Anthropic |
| WordPress | 6.0+ | With WPGraphQL |
| Rank Math SEO | 1.0.201+ | With WPGraphQL extension |
| WooCommerce | Latest | Optional, for product SEO |

## Rank Math SEO Meta Keys

| Field | Meta Key |
|-------|----------|
| SEO Title | `rank_math_title` |
| Meta Description | `rank_math_description` |
| Focus Keyword | `rank_math_focus_keyword` |
| Canonical URL | `rank_math_canonical_url` |
| Robots | `rank_math_robots` |

## License

MIT License - see [LICENSE](LICENSE) for details.
