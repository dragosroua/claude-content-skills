# Setup Guide

Step-by-step installation of Claude Content Skills.

## Prerequisites

- **Python 3.10+** - Check with `python --version`
- **Claude Code CLI** - [Installation guide](https://docs.anthropic.com/claude-code)
- **pip** - Python package manager

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/dragosroua/claude-content-skills.git
cd claude-content-skills
```

### Step 2: Install Python Dependencies

```bash
pip install -r requirements.txt
```

This installs:
- `requests` - HTTP library
- `beautifulsoup4` - HTML parsing
- `python-dotenv` - Environment variables
- `rich` - Terminal formatting (optional but recommended)
- `tqdm` - Progress bars

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
# WordPress Configuration (for SEO skill)
WP_GRAPHQL_URL=https://your-site.com/graphql
WP_USERNAME=your-username
WP_APP_PASSWORD=xxxx xxxx xxxx xxxx xxxx xxxx

# Site Configuration (for Link Analyzer)
SITE_DOMAIN=your-site.com
DIST_PATH=./dist
```

### Step 5: Configure Individual Skills (Optional)

Each skill has a `config.example.json` that can be customized:

```bash
# SEO WordPress Manager
cp skills/seo-wordpress-manager/config.example.json \
   skills/seo-wordpress-manager/config.json

# Astro CTA Injector
cp skills/astro-cta-injector/config.example.json \
   skills/astro-cta-injector/config.json

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
   - Download from GitHub or WordPress plugin repository
   - Upload and Activate

### 2. Enable Mutations

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

## Updating Skills

To update to the latest version:

```bash
cd claude-content-skills
git pull origin main

# Re-copy to Claude Code
cp -r skills/* ~/.claude/skills/
cp -r shared ~/.claude/skills/
```

## Uninstalling

```bash
# Remove from Claude Code
rm -rf ~/.claude/skills/seo-wordpress-manager
rm -rf ~/.claude/skills/astro-cta-injector
rm -rf ~/.claude/skills/link-analyzer
rm -rf ~/.claude/skills/shared

# Remove repository
rm -rf claude-content-skills
```

## Next Steps

- [Configuration Guide](CONFIGURATION.md) - Detailed config options
- [Troubleshooting](TROUBLESHOOTING.md) - Common issues
- [Skills Documentation](../skills/) - Per-skill guides
