**Step 1: Modify Hosting Main Domain & DNS**

• Update the primary main domain within the cPanel Domains section.
• Ensure the Document Root points to the exact same folder containing the current source code (typically public_html ).
• Update external DNS system A-records and provision an SSL Certificate (Let's Encrypt) for the new domain name immediately.

**Step 2: Hardcode Safe Mode in wp-config.php**

To bypass any automatic application-level redirect loop mechanisms immediately following the hosting switch, insert the following definitions directly below the opening <?php line:

```
define('WP_HOME', 'https://newdomain.com');
define('WP_SITEURL', 'https://newdomain.com');
```

**Step 3: Replace Hardcoded Values in Source Files**

Before executing the database migration, sanitize static text assets, inline theme elements, custom code configurations, or asset references hardcoded directly in code files ( wp-content/ ). Navigate to your root directory and run:
```
find . -type f \( -name "*.php" -o -name "*.css" -o -name "*.js" -o -name "*.json" \)
| xargs sed -i 's|olddomain.com|newdomain.com|g'
```

**Step 4: Database Search & Replace via WP-CLI**

Execute the official WP-CLI binary replacement protocol. This safely un-serializes, updates, and re-serializes all nested strings correctly:
```
wp search-replace 'https://olddomain.com' 'https://newdomain.com' --all-tables
```
Note: Once successful, you may safely comment out or delete the static constants added in Step 2 from your wp-config.php .

**Step 5: Flush Layout Assets & Cache Systems**

• Elementor: Navigate to `Elementor` → `Tools` → `Replace URL` . Input old and new strings. Then under `General` , click Regenerate Files & Data.
• Divi: Navigate to `Divi` → `Theme Options` → `Builder` → `Advanced` → Click Clear on Static CSS
File Generation.
• Permalinks: Navigate to `Settings` → `Permalinks` and click Save Changes twice to rewrite the server `.htaccess` rules.



**3. The Flatsome Theme Specific Adjustments**

Flatsome is uniquely sensitive to migrations because its UX Builder saves drag-and-drop structural elements within text shortcodes in wp_posts , and stores the comprehensive layout configuration options under a single database entry string ( theme_mods_flatsome ).
Mandatory Flatsome Post-Migration Actions:
```
1. Log into the newly updated WordPress English Dashboard.
2. Go to Flatsome → Theme Options → Click the blue Save All button. This explicitly forces a database refresh and recalculates structural string sizes for the theme options array.
3. Go to Flatsome → Advanced → Maintenance → Click Clear All Cache to strip legacy compiled theme layouts.
```
