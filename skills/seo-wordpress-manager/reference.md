# SEO WordPress Manager - Reference Guide

## Rank Math SEO Fields

### Primary Fields (Most Common)
| Field | Meta Key | Max Length | Description |
|-------|----------|------------|-------------|
| SEO Title | `rank_math_title` | 60 chars | Title shown in search results |
| Meta Description | `rank_math_description` | 160 chars | Description in search results |
| Focus Keyword | `rank_math_focus_keyword` | N/A | Primary keyword to optimize for |

### Secondary Fields
| Field | Meta Key | Description |
|-------|----------|-------------|
| Canonical URL | `rank_math_canonical_url` | Preferred URL for this content |
| Robots | `rank_math_robots` | Array of robots directives (noindex, nofollow, etc.) |
| Advanced Robots | `rank_math_advanced_robots` | Additional robots settings |

### Open Graph Fields
| Field | Meta Key | Description |
|-------|----------|-------------|
| OG Title | `rank_math_facebook_title` | Facebook/social title |
| OG Description | `rank_math_facebook_description` | Facebook/social description |
| OG Image | `rank_math_facebook_image` | Social sharing image URL |
| OG Image ID | `rank_math_facebook_image_id` | Attachment ID for OG image |

### Twitter Fields
| Field | Meta Key | Description |
|-------|----------|-------------|
| Twitter Use Facebook | `rank_math_twitter_use_facebook` | Use Facebook OG data for Twitter |
| Twitter Card Type | `rank_math_twitter_card_type` | summary, summary_large_image, etc. |
| Twitter Title | `rank_math_twitter_title` | Twitter card title |
| Twitter Description | `rank_math_twitter_description` | Twitter card description |
| Twitter Image | `rank_math_twitter_image` | Twitter card image URL |

### Schema Fields
| Field | Meta Key | Description |
|-------|----------|-------------|
| Schema Type | `rank_math_rich_snippet` | Schema.org type (article, product, etc.) |
| Schema Data | `rank_math_schema_*` | Various schema-specific fields |

## GraphQL Queries

### Fetch Posts with SEO Data
```graphql
query GetPostsWithSEO($first: Int!, $after: String) {
  posts(first: $first, after: $after, where: {status: PUBLISH}) {
    pageInfo {
      hasNextPage
      endCursor
    }
    nodes {
      databaseId
      title
      slug
      uri
      seo {
        title
        description
        focusKw
        canonicalUrl
        openGraph {
          title
          description
          image {
            sourceUrl
          }
        }
      }
    }
  }
}
```

### Fetch Posts by Category
```graphql
query GetPostsByCategory($categorySlug: String!, $first: Int!) {
  posts(first: $first, where: {categoryName: $categorySlug, status: PUBLISH}) {
    nodes {
      databaseId
      title
      seo {
        title
        description
        focusKw
      }
    }
  }
}
```

### Update SEO Mutation
```graphql
mutation UpdatePostSEO($postId: Int!, $title: String, $description: String, $focusKeyword: String) {
  updatePostSeo(input: {
    postId: $postId
    title: $title
    description: $description
    focusKeyword: $focusKeyword
  }) {
    success
    post {
      databaseId
      title
    }
  }
}
```

## SEO Best Practices

### Title Optimization
- **Length**: 50-60 characters (Google truncates at ~60)
- **Structure**: `Primary Keyword - Secondary Keyword | Brand`
- **Front-load**: Put important keywords at the beginning
- **Unique**: Each page should have a unique title

### Meta Description Optimization
- **Length**: 150-160 characters (Google truncates at ~160)
- **Include CTA**: Use action words like "Learn", "Discover", "Get"
- **Include keyword**: Natural placement of focus keyphrase
- **Unique**: Each page needs a unique description
- **Compelling**: Think of it as ad copy

### Focus Keyword Guidelines
- **One primary keyword** per post
- **Long-tail** keywords often perform better
- **Search intent** alignment is crucial
- **Natural density**: Don't keyword stuff

## Common Issues & Solutions

### Issue: Titles Too Long
**Solution**: Use the `truncate_title()` function to intelligently shorten titles while preserving meaning.

### Issue: Duplicate Meta Descriptions
**Solution**: Run the analyzer to identify duplicates, then generate unique descriptions based on post content.

### Issue: Missing Focus Keywords
**Solution**: Analyze post content and titles to suggest relevant keyphrases.

### Issue: Generic Descriptions
**Solution**: Replace default/template descriptions with content-specific ones.

## WordPress Application Password Setup

1. Go to **Users > Profile** in WordPress admin
2. Scroll to **Application Passwords** section
3. Enter a name (e.g., "Claude SEO Manager")
4. Click **Add New Application Password**
5. Copy the generated password (shown once!)
6. Use format: `username:password` for basic auth

## Rate Limiting Recommendations

| Site Size | Batch Size | Delay (seconds) |
|-----------|------------|-----------------|
| Small (<100 posts) | 20 | 0.5 |
| Medium (100-1000) | 10 | 1 |
| Large (1000+) | 5 | 2 |

## Error Handling

| Error Code | Meaning | Solution |
|------------|---------|----------|
| 401 | Unauthorized | Check Application Password |
| 403 | Forbidden | Check user permissions |
| 429 | Rate Limited | Increase delay between requests |
| 500 | Server Error | Check WPGraphQL logs |

## Rank Math vs Yoast Field Mapping

If migrating from Yoast SEO, here's the field mapping:

| Yoast Field | Rank Math Field |
|-------------|-----------------|
| `_yoast_wpseo_title` | `rank_math_title` |
| `_yoast_wpseo_metadesc` | `rank_math_description` |
| `_yoast_wpseo_focuskw` | `rank_math_focus_keyword` |
| `_yoast_wpseo_canonical` | `rank_math_canonical_url` |
| `_yoast_wpseo_opengraph-title` | `rank_math_facebook_title` |
| `_yoast_wpseo_opengraph-description` | `rank_math_facebook_description` |
| `_yoast_wpseo_opengraph-image` | `rank_math_facebook_image` |
| `_yoast_wpseo_twitter-title` | `rank_math_twitter_title` |
| `_yoast_wpseo_twitter-description` | `rank_math_twitter_description` |
