# Big Sky Wildlife

Welcome to Big Sky Wildlife. My name is Scott Brovsky, and I'm a wildlife photographer and naturalist based in Gardiner, MT—right on the border of Yellowstone National Park.

Feel free to comment or message me with any of your Yellowstone wildlife questions. Please consider [subscribing](https://bigskywildlife.com/subscribe) to receive every new wildlife photo and video I post directly in your inbox.

## Photography Equipment and Ethical Practices

I photograph and film with a Sony A7RV camera paired with a Sony FE 200-600mm f/5.6-6.3 G OSS Lens. Depending on the distance to the animal, I sometimes use a Sony 1.4X teleconverter.

All of my photos and videos are ethically created. I maintain a safe distance from all wildlife and do everything I can to avoid disturbing the animals. I never bait, use calls, or lure wild animals to obtain photographs or videos. All of my photographs are of wild animals; I do not photograph at zoos or wild animal parks.

## Wildlife Photo Gallery & Store

Enjoy browsing the [Wildlife Photo Gallery](https://bigskywildlife.com/wildlife-photo-gallery-store/). If you see an image you like, click the shopping cart icon at the bottom of the gallery for purchasing options. For questions about custom sizes and larger gallery-quality metal prints, please contact me at scott@bigskywildlife.com.

## Volunteer Work

I am also a volunteer officer at [Bear Creek Council](https://northernplains.org). Bear Creek Council is based in Gardiner, Montana, on the edge of Yellowstone National Park. We work closely with park rangers and local citizens to conserve and protect the integrity of our environment and community.

If you are interested in helping conserve the wildlife in Yellowstone and the larger Greater Yellowstone Ecosystem (GYE) area, here are the local and regional wildlife nonprofits that I donate to and support:

- [Bear Creek Council](https://northernplains.org)
- [Yellowstone Forever](https://www.yellowstone.org)
- [Wolves of the Rockies](https://www.wolvesoftherockies.org)
- [Save the Yellowstone Grizzly](https://savetheyellowstonegrizzly.org)
- [People and Carnivores](https://peopleandcarnivores.org)

## Recent Posts

- [A Great Blue Heron Fishing in the Salt River](https://bigskywildlife.com/2025/01/a-great-blue-heron-fishing-in-the-salt-river/)
- [A Coues Deer Buck in an Oak Forested Canyon](https://bigskywildlife.com/2025/01/a-coues-deer-buck-in-an-oak-forested-canyon/)
- [Elegant Trogon Eating Pyracantha Berries](https://bigskywildlife.com/2025/01/elegant-trogon-eating-pyracantha-berries/)
- [A Coyote in the Cibola National Wildlife Refuge](https://bigskywildlife.com/2025/01/a-coyote-in-the-cibola-national-wildlife-refuge/)
- [Flock of Yellow-headed and Red-winged Blackbirds in the Arizona Desert](https://bigskywildlife.com/2025/01/flock-of-yellow-headed-and-red-winged-blackbirds-in-the-arizona-desert/)

*Note: The above links direct to the respective posts on the Big Sky Wildlife website.*

## WordPress Troubleshooting: 403 Forbidden Error

The site has experienced recurring 403 Forbidden errors. Below are the diagnosed cause and fix steps using cPanel.

### Diagnosis

- All pages (homepage, wp-login.php, wp-admin) return HTTP 403
- This indicates the web server is blocking all requests before WordPress even loads
- Most common cause: corrupted or misconfigured `.htaccess` file in the WordPress root directory

### Fix: Reset .htaccess via cPanel File Manager

1. **Log in to cPanel** and open **File Manager**
2. Navigate to your WordPress root directory (usually `public_html/`)
3. **Enable hidden files**: Click "Settings" (top right) and check "Show Hidden Files (dotfiles)"
4. Find the `.htaccess` file in `public_html/`
5. **Rename it** to `.htaccess_backup` (right-click > Rename) — this disables the old rules
6. **Create a new `.htaccess` file** (top left "+ File" button) with the default WordPress rewrite rules:

```apache
# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
# END WordPress
```

7. **Test the site** — it should load again
8. Go to **WordPress Admin > Settings > Permalinks** and click "Save Changes" (this regenerates `.htaccess` properly)

### If .htaccess Reset Doesn't Fix It

Check these additional causes in cPanel:

- **File Permissions**: In File Manager, verify `public_html/` is set to `755` and files are `644`. Select all files > Change Permissions if needed.
- **Directory Index**: In cPanel > "Indexes", make sure your WordPress directory is not set to "No Indexing" — set it to "Default" or "Inherited".
- **IP Blocklist**: Check cPanel > "IP Blocker" to make sure your IP (or all IPs) aren't blocked.
- **ModSecurity**: In cPanel > "ModSecurity", try temporarily disabling it to see if a WAF rule is causing the block.
- **Security Plugins**: If the site loads after `.htaccess` reset, a plugin like Wordfence or Sucuri may be re-corrupting it. Rename `wp-content/plugins/` to `wp-content/plugins_disabled/` via File Manager to disable all plugins, then re-enable them one by one to find the culprit.

### Root Cause: 9GB Storage Bloat on Bluehost (10GB Limit)

Jetpack reported the site is using **9GB of a 10GB storage limit**. A full or near-full disk on Bluehost can directly cause 403 errors because WordPress and the server can't write temp files, sessions, or cache. This is the most likely root cause of the recurring 403.

For a wildlife photography site using a **Sony A7RV (61 megapixels)**, images are almost certainly the largest storage consumer. A single full-res JPEG from this camera is 20-40MB, and WordPress auto-generates multiple thumbnail sizes per upload (thumbnail, medium, medium_large, large, 1536x1536, 2048x2048, plus any theme-specific sizes). This means each uploaded photo can consume **5-10x its original file size** on disk.

### Step 1: Identify What's Using Space (cPanel)

1. Log into **cPanel > File Manager**
2. Check the sizes of these directories in `public_html/`:
   - `wp-content/uploads/` — this is where all media lives (likely the biggest)
   - `wp-content/backups/` or `wp-content/updraft/` — old backup files from plugins
   - `wp-content/cache/` — caching plugin data
   - `wp-content/ai1wm-backups/` — All-in-One WP Migration backups
3. In cPanel, go to **Disk Usage** to see a breakdown by directory

### Step 2: Clean Up Old Backups (Biggest Quick Win)

Backup plugins often store full-site backups on the server itself. These are huge.

1. In File Manager, check for and **delete old backup files** in:
   - `wp-content/updraft/` (UpdraftPlus)
   - `wp-content/ai1wm-backups/` (All-in-One WP Migration)
   - `wp-content/backups/` (various plugins)
   - Any `.zip`, `.tar.gz`, or `.sql` files in the root or `wp-content/`
2. Since Jetpack VaultPress is handling backups in the cloud, you do **not** need local backup files on the server

### Step 3: Optimize Images (Long-Term Fix)

This is critical for a photography site to stay under storage limits:

1. **Install ShortPixel or Imagify plugin** (after the 403 is resolved):
   - These compress existing and future uploads automatically
   - Use "Lossy" or "Glossy" compression — visually identical but 60-80% smaller
   - Run the bulk optimizer on all existing images
2. **Remove unused thumbnail sizes**: Add this to your theme's `functions.php` to prevent WordPress from generating sizes you don't use:
   ```php
   function bsw_remove_extra_image_sizes() {
       remove_image_size('1536x1536');
       remove_image_size('2048x2048');
   }
   add_action('init', 'bsw_remove_extra_image_sizes');
   ```
3. **Regenerate thumbnails** after removing unused sizes (use the "Regenerate Thumbnails" plugin) — this deletes the old unused sizes and reclaims space
4. **Resize before uploading**: Resize photos to a max of 2400px on the longest edge before uploading. Full 61MP images (9504x6336px) are far larger than any screen needs.

### Step 4: Offload Media Storage (Optional, Recommended)

To avoid hitting Bluehost's storage limit again as you add more photos:

1. **Jetpack Site Accelerator** (free, already have Jetpack): In WP Admin > Jetpack > Settings > Performance, enable "Site Accelerator." This serves images from WordPress.com's CDN instead of your server.
2. **Alternative**: Use the "WP Offload Media Lite" plugin with a cloud storage provider (Cloudflare R2, Amazon S3, etc.) to move all media off Bluehost entirely.

### Preventing Recurrence

- Keep a backup of the working `.htaccess` file
- If a security plugin (Wordfence, Sucuri, etc.) is adding rules to `.htaccess`, review its settings to prevent overly aggressive blocking
- Set up uptime monitoring (e.g., UptimeRobot free tier) to get alerts when the site goes down
- Resize photos before uploading (max 2400px longest edge)
- Use an image optimization plugin (ShortPixel or Imagify) for automatic compression
- Do not store backups locally on the server — rely on Jetpack VaultPress cloud backups
- Consider enabling Jetpack Site Accelerator to serve images from a CDN

---

*This Markdown file was generated based on content from [Big Sky Wildlife](https://bigskywildlife.com).*
