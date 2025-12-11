---
name: seo-wordpress-manager
description: Batch update Rank Math SEO metadata (titles, descriptions, focus keyphrases) for WordPress posts and WooCommerce products via GraphQL. Use when the user wants to update SEO metadata, optimize titles, fix meta descriptions, or manage Rank Math SEO fields across multiple posts or products. Supports preview mode, progress tracking, and resume capability.
---

# SEO WordPress Manager Skill

## Purpose

This skill manages Rank Math SEO metadata in WordPress/WooCommerce sites via the WPGraphQL API. It enables batch updates of:
- SEO titles
- Meta descriptions
- Focus keyphrases
- Open Graph metadata

Supports both **posts** and **WooCommerce products**.

## When to Use This Skill

- User asks to "update SEO titles" or "fix meta descriptions"
- User wants to batch process WordPress posts or WooCommerce products for SEO
- User mentions Rank Math SEO optimization
- User needs to update SEO metadata across multiple posts/products
- User wants to analyze SEO scores

## Prerequisites

### WordPress Setup Required
1. **WPGraphQL plugin** installed and activated
2. **WPGraphQL for Rank Math SEO** extension installed
3. **WPGraphQL WooCommerce** (for product support)
4. **Application Password** created for authentication

### Rank Math SEO GraphQL Mutations
Add this to your theme's `functions.php` to enable mutations:

```php
// Enable Rank Math SEO mutations via WPGraphQL
add_action('graphql_register_types', function() {
    register_graphql_mutation('updatePostSeo', [
        'inputFields' => [
            'postId' => ['type' => 'Int', 'description' => 'Post or Product ID'],
            'title' => ['type' => 'String', 'description' => 'SEO Title'],
            'description' => ['type' => 'String', 'description' => 'Meta Description'],
            'focusKeyword' => ['type' => 'String', 'description' => 'Focus Keyword'],
        ],
        'outputFields' => [
            'success' => ['type' => 'Boolean'],
            'post' => ['type' => 'Post'],
        ],
        'mutateAndGetPayload' => function($input) {
            $post_id = absint($input['postId']);

            if (!current_user_can('edit_post', $post_id)) {
                throw new \GraphQL\Error\UserError('You do not have permission to edit this post.');
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

            return [
                'success' => true,
                'post' => get_post($post_id),
            ];
        }
    ]);
});
```

## Configuration

Create a `config.json` in the skill directory:

```json
{
  "wordpress": {
    "graphql_url": "https://your-site.com/graphql",
    "username": "your-username",
    "app_password": "your-app-password"
  },
  "batch": {
    "size": 10,
    "delay_seconds": 1
  },
  "state_file": "./seo_update_progress.json"
}
```

Or use environment variables:
- `WP_GRAPHQL_URL`
- `WP_USERNAME`
- `WP_APP_PASSWORD`

## Workflow

### Step 1: Analyze Posts/Products for SEO Issues
```bash
# Analyze posts
python scripts/analyze_seo.py --all --output analysis.json

# Analyze WooCommerce products
python scripts/wp_graphql_client.py --action products --all --output products.json
```
This fetches items and identifies SEO issues (missing titles, too long descriptions, etc.).
Output includes instructions for Claude to generate optimized SEO content.

### Step 2: Generate Optimized SEO Content
Claude analyzes the output and generates a `changes.json` file with:
- Optimized SEO titles (50-60 chars)
- Compelling meta descriptions (150-160 chars)
- Relevant focus keyphrases

### Step 3: Preview Changes (Dry Run)
```bash
python scripts/preview_changes.py --input changes.json
```

### Step 4: Apply Updates
```bash
python scripts/rankmath_batch_updater.py --input changes.json --apply
```

### Step 5: Resume if Interrupted
```bash
python scripts/rankmath_batch_updater.py --resume --input changes.json
```

## Input Format

The skill expects a JSON file with changes:

```json
{
  "updates": [
    {
      "post_id": 123,
      "post_title": "Original Post Title",
      "current": {
        "seo_title": "Old Title | Site Name",
        "meta_desc": "Old description"
      },
      "new": {
        "seo_title": "New Optimized Title | Site Name",
        "meta_desc": "New compelling meta description under 160 chars"
      }
    }
  ]
}
```

## Output

The skill produces:
1. **Preview report** showing before/after for each post
2. **Progress state file** for resuming interrupted batches
3. **Final report** with success/failure counts

## Safety Features

- **Dry-run mode** by default - preview before applying
- **Confirmation prompt** before batch updates
- **Progress tracking** - resume interrupted sessions
- **Rate limiting** - configurable delay between API calls
- **Backup** - logs current values before changing

## Example Usage

User: "Update the meta descriptions for all WooCommerce products in the 'electronics' category to be more compelling"

Claude will:
1. Run `wp_graphql_client.py --action products` to fetch products and identify SEO issues
2. Analyze each product's content and current SEO data
3. Generate optimized titles, descriptions, and keyphrases
4. Create a `changes.json` file with the improvements
5. Run `preview_changes.py` to show before/after comparison
6. Ask for confirmation
7. Run `rankmath_batch_updater.py --apply` to apply changes
8. Report results with success/failure counts

## Rank Math Meta Keys

| Field | Meta Key |
|-------|----------|
| SEO Title | `rank_math_title` |
| Meta Description | `rank_math_description` |
| Focus Keyword | `rank_math_focus_keyword` |
| Canonical URL | `rank_math_canonical_url` |
| Robots | `rank_math_robots` |

## GraphQL Fields (WPGraphQL for Rank Math)

| Field | Type | Description |
|-------|------|-------------|
| `title` | String | SEO title |
| `description` | String | Meta description |
| `focusKeywords` | String/Array | Focus keyword(s) |
| `canonicalUrl` | String | Canonical URL |
| `robots` | Array | Robot directives |
| `seoScore` | Object | SEO score with `score` and `rating` |
