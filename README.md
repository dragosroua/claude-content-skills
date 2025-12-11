# Claude SEO Skills for WordPress & WooCommerce

**Automate your Rank Math SEO optimization with AI-powered batch updates**

<p align="center">
  <img src="https://img.shields.io/badge/Rank_Math_SEO-Compatible-4CAF50?style=for-the-badge" alt="Rank Math Compatible" />
  <img src="https://img.shields.io/badge/WooCommerce-Supported-96588A?style=for-the-badge&logo=woocommerce&logoColor=white" alt="WooCommerce Supported" />
  <img src="https://img.shields.io/badge/Claude_Code-Skills-5A67D8?style=for-the-badge&logo=anthropic&logoColor=white" alt="Claude Code Skills" />
</p>

---

## What is this?

This is a **Claude Code skill** that lets you batch-update SEO metadata on your WordPress/WooCommerce site using AI. Instead of manually editing hundreds of product titles and meta descriptions one by one, just tell Claude what you want:

```
You: "Update all my product meta descriptions to be more compelling and include a call-to-action"

Claude: I'll fetch your products, analyze their current SEO, generate optimized descriptions,
        show you a preview, and apply the changes after your approval.
```

### The Problem It Solves

- **Manual SEO updates are tedious** - Updating 500 product descriptions takes days
- **Rank Math bulk edit is limited** - No AI-powered content generation
- **Consistency is hard** - Keeping SEO format consistent across all products
- **Missing SEO fields** - Easy to forget focus keywords or leave descriptions empty

### The Solution

This skill connects Claude directly to your WordPress site via GraphQL, allowing you to:

1. **Analyze** - Scan all posts/products and find SEO issues (missing descriptions, titles too long, no focus keywords)
2. **Generate** - Let Claude create optimized SEO content based on your product/post content
3. **Preview** - See before/after comparison before any changes are made
4. **Apply** - Batch update with progress tracking and the ability to resume if interrupted

---

## Who is this for?

- **WooCommerce store owners** with hundreds of products needing SEO optimization
- **WordPress site managers** wanting to improve blog post SEO at scale
- **SEO professionals** managing multiple WordPress sites
- **Anyone using Rank Math SEO** who wants AI-powered batch optimization

### Compatible With

- **Rank Math SEO** (required)
- **WooCommerce** (optional, for product SEO)
- **Shoptimizer theme** & **CommerceKit**
- Any WordPress theme using standard post meta

---

## Quick Start

### 1. Install Required WordPress Plugins

| Plugin | Purpose |
|--------|---------|
| [WPGraphQL](https://wordpress.org/plugins/wp-graphql/) | Enables GraphQL API |
| [Rank Math SEO](https://wordpress.org/plugins/seo-by-rank-math/) | Your SEO plugin |
| [WPGraphQL for Rank Math](https://github.com/AxeWP/wp-graphql-rank-math) | Connects them |
| [WPGraphQL WooCommerce](https://github.com/wp-graphql/wp-graphql-woocommerce) | For products (optional) |

### 2. Add the GraphQL Mutation

Add this to your theme's `functions.php`:

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
                update_post_meta($post_id, 'rank_math_title', sanitize_text_field($input['title']));
            }
            if (isset($input['description'])) {
                update_post_meta($post_id, 'rank_math_description', sanitize_textarea_field($input['description']));
            }
            if (isset($input['focusKeyword'])) {
                update_post_meta($post_id, 'rank_math_focus_keyword', sanitize_text_field($input['focusKeyword']));
            }
            return ['success' => true, 'post' => get_post($post_id)];
        }
    ]);
});
```

### 3. Create Application Password

1. WordPress Admin → Users → Your Profile
2. Scroll to "Application Passwords"
3. Name: "Claude SEO Manager"
4. Click "Add New" and copy the password

### 4. Install the Skill

```bash
# Clone this repo
git clone https://github.com/huge8888/claude-wpseo-skills.git
cd claude-wpseo-skills

# Install dependencies
pip install -r requirements.txt

# Copy to Claude Code
cp -r skills/* ~/.claude/skills/
cp -r shared ~/.claude/skills/

# Configure your credentials
cp .env.example .env
# Edit .env with your WordPress GraphQL URL and app password
```

### 5. Use It

Open Claude Code and just ask:

```
"Analyze SEO issues for all my WooCommerce products"
"Fix meta descriptions that are too short"
"Add focus keywords to posts missing them"
"Update product SEO titles to include the brand name"
```

---

## Features

### SEO Analysis
- Find posts/products with missing SEO titles
- Detect meta descriptions that are too long (>160 chars) or too short (<120 chars)
- Identify missing focus keywords
- Track Rank Math SEO scores

### Batch Updates
- Update hundreds of posts/products in one go
- Preview all changes before applying
- Dry-run mode by default (safe!)
- Progress tracking with resume capability
- Automatic backup of original values

### WooCommerce Support
- Fetch products by category
- Support for Simple and Variable products
- Product-specific SEO recommendations

---

## Example Workflow

```bash
# 1. Analyze your site's SEO issues
python skills/seo-wordpress-manager/scripts/analyze_seo.py --all --output analysis.json

# 2. Review the analysis with Claude and generate improvements

# 3. Preview the changes
python skills/seo-wordpress-manager/scripts/preview_changes.py --input changes.json

# 4. Apply the changes (after confirmation)
python skills/seo-wordpress-manager/scripts/rankmath_batch_updater.py --input changes.json --apply
```

---

## Rank Math Meta Keys Reference

| Field | WordPress Meta Key |
|-------|-------------------|
| SEO Title | `rank_math_title` |
| Meta Description | `rank_math_description` |
| Focus Keyword | `rank_math_focus_keyword` |
| Canonical URL | `rank_math_canonical_url` |
| Robots | `rank_math_robots` |

---

## Requirements

- Python 3.10+
- WordPress 6.0+
- Rank Math SEO 1.0.201+
- WPGraphQL + WPGraphQL for Rank Math
- Claude Code CLI

---

## License

MIT License - Use it however you want!

---

## Need Help?

- Check the [full documentation](docs/README.md)
- See [troubleshooting guide](docs/integration/TROUBLESHOOTING.md)
- Open an issue on GitHub
