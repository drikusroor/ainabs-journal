---
title: "Use Wordpress’ paginate_link function with custom html and tailwindcss"
date: 2023-03-09T21:28:00Z
categories:
  - "notebook"
tags:
  - "tailwindcss"
  - "wordpress"
source: Notion
---

Wordpress’ `paginate_link` [function](https://developer.wordpress.org/reference/functions/paginate_links/) is pretty neat for generating pagination links without any hassle. It does not, however, give you easy control over the exact html output. This makes it hard for styling the list, list items and links when using tailwindcss as we cannot add the utility classes to the elements.

To make this easier, I wrote a little procedure that will do some string replacements that allows us to have more control over the classes that we want to use. Instead of using a function, I decided to make use of Wordpress’ `get_template_part` [functionality](https://developer.wordpress.org/reference/functions/get_template_part) :

## template-parts/pagination.php

```php
<?php
global $wp_query;

if ($wp_query->max_num_pages <= 1) return;

$big = 999999999;

$pages = paginate_links(array(
    'base' => str_replace($big, '%#%', esc_url(get_pagenum_link($big))),
    'format' => '?paged=%#%',
    'current' => max(1, get_query_var('paged')),
    'total' => $wp_query->max_num_pages,
    'type'  => 'array',
));
if (is_array($pages)) {
    $paged = (get_query_var('paged') == 0) ? 1 : get_query_var('paged');

		// style the container / list element
    echo '<ul class="flex flex-row justify-center gap-2 text-sm list-none">';

    foreach ($pages as $page) {
        $isCurrent = strpos($page, 'current');
        $isAElement = strpos($page, '<a');

				// default item classes
        $class = 'text-black no-underline';

				// add classes to the current item
        if ($isCurrent !== false) {
            $class .= ' font-bold';
        }

				// add classes to the links
        if ($isAElement !== false) {
            $class .= ' hover:underline';
        }

        $page = str_replace("page-numbers", $class, $page);
        echo "<li>$page</li>";
    }
    echo '</ul>';
}
```

## How to use

In your page or template, simply use `get_template_part('template-parts/pagination');`:

```php
<?php

get_template_part('template-parts/pagination');

?>
```
