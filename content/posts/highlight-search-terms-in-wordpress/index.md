---
title: "Highlight search terms in Wordpress"
date: 2023-03-26T17:12:00Z
categories:
  - "notebook"
tags:
  - "wordpress"
  - "chatgpt"
source: Notion
---

To highlight search terms in the content in your Wordpress search result page, add the following function to your `functions.php` file:

```php
function search_excerpt_highlight()
{
    $content = get_the_content();
    $content = wp_strip_all_tags($content);

    // Set the excerpt length
    $excerpt_length = 100; // You can adjust this value to your desired excerpt length
    $padding = 10; // Add padding around the search term

    $keys = explode(' ', get_search_query());
    $first_key = $keys[0];

    // Find the first occurrence of the search term
    $term_pos = stripos($content, $first_key);

    if ($term_pos !== false) {
        $start = max(0, $term_pos - $padding);
        $prefix = $start > 0 ? '...' : ''; // Add "..." prefix if the excerpt doesn't start from the beginning
        $excerpt = mb_strimwidth($content, $start, $excerpt_length, '...');
        $excerpt = $prefix . $excerpt;
    } else {
        // If the search term is not found, use the original content with a trimmed excerpt
        $excerpt = mb_strimwidth($content, 0, $excerpt_length, '...');
    }

    // Highlight the search terms
    $pattern = '/(' . implode('|', $keys) . ')/iu';
    $excerpt = preg_replace($pattern, '<span class="highlighted">\0</span>', $excerpt);

    return $excerpt;
}
```

This will show the search terms in the content wrapped in a `<span>` element with the `highlighted` class.

Additionally, it will look for the search term in the whole content, not only the first 100 characters, and show that subset of the content that contains the search term. For example, if your search term is `dragon`, and it appears later in the content, the text will be shown like this, prefixed with ellipsis:

> …wolves came out to play but the \[dragon\] was still hiding in the bushes…
