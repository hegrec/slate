# Banners

Banners available to display on a store.

| ----- |

| Manages | Banner Object |
| OAuth Scopes |  store_v2_marketing |
| |store_v2_marketing_read_only |

## Banners - Properties

| ----- |
| name |  string |  Required field. |
| page |  enum |  'home_page', 'category_page', 'brand_page', 'search_page'. Required field. |
| dateto |  date |  If the datetype is set as 'custom', this field specifies the date when the banner should stop being visible on the storefront. |
| content |  text |  Required field. |
| bannerid |  int |  Read-only. |
| datefrom |  date |  If the datetype is set as 'custom', this field specifies the date when the banner should become visible on the storefront. |
| datetype |  enum |  Accepted values are 'always' or 'custom'. This specifies whether the banner should be visible during a specific date range. Required field. |
| location |  enum |  'top', 'bottom'. Required field. |
| datecreated |  date |  Date the banner was first created. This will default to today's date. |
| catorbrandid |  int |  ID for the brand or category. |
| status/visible |  int |  Integer value denoting whether or not the banner is visible on the storefront: 1 = visible; 0 = not visible. |  
