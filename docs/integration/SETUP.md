# Setup Guide

Step-by-step installation of Claude Content Skills for WordPress/WooCommerce.

## Prerequisites

- **Python 3.10+** - Check with `python --version`
- **Claude Code CLI** - [Installation guide](https://docs.anthropic.com/claude-code)
- **pip** - Python package manager
- **WordPress 6.0+** with admin access

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/your-repo/claude-wpseo-skills.git
cd claude-wpseo-skills
```

### Step 2: Install Python Dependencies

```bash
pip install -r requirements.txt
```

This installs:
- `requests` - HTTP library for GraphQL calls
- `python-dotenv` - Environment variables

### Step 3: Copy Skills to Claude Code

**Global installation** (available in all projects):

```bash
cp -r skills/* ~/.claude/skills/
cp -r shared ~/.claude/skills/
```

**Project-specific installation**:

```bash
mkdir -p .claude/skills
cp -r skills/* .claude/skills/
cp -r shared .claude/skills/
```

### Step 4: Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your credentials:

```bash
# WordPress Configuration
WP_GRAPHQL_URL=https://your-site.com/graphql
WP_USERNAME=your-username
WP_APP_PASSWORD=xxxx xxxx xxxx xxxx xxxx xxxx

# Site Configuration (for Link Analyzer)
SITE_DOMAIN=your-site.com
```

### Step 5: Configure Skills (Optional)

Each skill has a `config.example.json` that can be customized:

```bash
# SEO WordPress Manager
cp skills/seo-wordpress-manager/config.example.json \
   skills/seo-wordpress-manager/config.json

# Link Analyzer
cp skills/link-analyzer/config.example.json \
   skills/link-analyzer/config.json
```

Edit each config with your specific settings.

### Step 6: Verify Installation

Start Claude Code in any project:

```bash
claude
```

Test each skill:

```
You: "Can you check if the SEO WordPress Manager skill is available?"
You: "What link analysis capabilities do you have?"
```

Claude should recognize and describe the skills.

## WordPress Setup (For SEO Skill)

### 1. Install Required Plugins

1. **WPGraphQL**
   - WordPress Admin → Plugins → Add New
   - Search "WPGraphQL"
   - Install and Activate

2. **Rank Math SEO** (if not already installed)
   - Search "Rank Math SEO"
   - Install and Activate

3. **WPGraphQL for Rank Math SEO**
   - Download from: https://github.com/AxeWP/wp-graphql-rank-math
   - Upload and Activate

4. **WPGraphQL WooCommerce** (for WooCommerce product support)
   - Download from: https://github.com/wp-graphql/wp-graphql-woocommerce
   - Upload and Activate

### 2. Enable Mutations

Add to your theme's `functions.php` (or use a code snippets plugin):

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

### 3. Create Application Password

1. WordPress Admin → Users → Your Profile
2. Scroll to "Application Passwords"
3. Name: "Claude SEO Manager"
4. Click "Add New Application Password"
5. Copy the generated password (shown once!)

### 4. Test Connection

```bash
python skills/seo-wordpress-manager/scripts/wp_graphql_client.py --action test
```

Expected output:
```
Connection successful!
Sample post: {...}
```

### 5. Test WooCommerce Products (Optional)

```bash
python skills/seo-wordpress-manager/scripts/wp_graphql_client.py --action products --limit 5
```

## Shoptimizer & CommerceKit Compatibility

This skill is fully compatible with:
- **Shoptimizer theme** - WooCommerce-optimized theme
- **CommerceKit plugin** - Enhanced WooCommerce functionality

The GraphQL queries work independently of theme/plugin styling and interact directly with WordPress post meta.

## Updating Skills

To update to the latest version:

```bash
cd claude-wpseo-skills
git pull origin main

# Re-copy to Claude Code
cp -r skills/* ~/.claude/skills/
cp -r shared ~/.claude/skills/
```

## Uninstalling

```bash
# Remove from Claude Code
rm -rf ~/.claude/skills/seo-wordpress-manager
rm -rf ~/.claude/skills/link-analyzer
rm -rf ~/.claude/skills/shared

# Remove repository
rm -rf claude-wpseo-skills
```

## Troubleshooting

### Connection Failed
- Verify GraphQL URL is correct (usually `https://your-site.com/graphql`)
- Check WPGraphQL plugin is activated
- Ensure your user has admin privileges

### 401 Unauthorized
- Application Password may be incorrect
- Username must match exactly (case-sensitive)
- Re-generate Application Password if needed

### Mutation Not Found
- Add the mutation code to `functions.php`
- Clear any caching plugins
- Check for PHP errors in debug.log

### WooCommerce Products Not Found
- Install WPGraphQL WooCommerce plugin
- Verify products are published
- Check product visibility settings

## Next Steps

- [Configuration Guide](CONFIGURATION.md) - Detailed config options
- [Troubleshooting](TROUBLESHOOTING.md) - Common issues
- [Skills Documentation](../skills/) - Per-skill guides
