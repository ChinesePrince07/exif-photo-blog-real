# 📷 `EXIF` Photo Blog






✨&nbsp;&nbsp;Features
-
- Built-in auth
- Photo upload with EXIF extraction
- Organize photos by tag
- Infinite scroll
- Light/dark mode
- Automatic OG image generation
- CMD-K menu with photo search
- AI-generated text descriptions
- RSS/JSON feeds
- Support for Fujifilm recipes and film simulations

<img src="/readme/og-image-share.png" alt="OG Image Preview" width=600 />


💻&nbsp;&nbsp;Local development
-
1. Clone code
2. Run `pnpm i` to install dependencies
3. If necessary, install [Vercel CLI](https://vercel.com/docs/cli#installing-vercel-cli) and authenticate by running `vercel login`
4. Run `vercel link` to connect CLI to your project
5. Run `vercel dev` to start dev server with Vercel-managed environment variables

See FAQ for [limitations of local development](#can-i-work-locally-without-access-to-an-image-storage-provider)

🎨&nbsp;&nbsp;Further customization
-
### AI text generation

_⚠️ READ BEFORE PROCEEDING_

> _Usage of this feature will result in fees from OpenAI. When enabling AI text generation, follow all recommended mitigations in order to avoid unexpected charges and attacks. Make sure your OpenAI secret key environment variable is not prefixed with NEXT_PUBLIC._

1. Setup OpenAI
   - If you don't already have one, create an [OpenAI](https://openai.com) account and fund it (see [this thread](https://github.com/sambecker/exif-photo-blog/issues/110) if you're having issues)
   - Generate an API key and store in environment variable `OPENAI_SECRET_KEY`
   - Setup usage limits to avoid unexpected charges (_recommended_)
2. Add rate limiting (_recommended_)
   - As an additional precaution, create an Upstash Redis store from the storage tab of the Vercel dashboard and link it to your project in order to enable rate limiting—no further configuration necessary
3. Configure auto-generated fields (optional) 
   - Set which text fields auto-generate when uploading a photo by storing a comma-separated list, e.g., `AI_TEXT_AUTO_GENERATED_FIELDS = title,semantic`
   - Accepted values:
     - `all`
     - `title` (default)
     - `caption`
     - `tags` (default)
     - `semantic` (default)
     - `none`

#### Alternate AI providers (experimental)

Set `OPENAI_BASE_URL` in order to use an alternate OpenAI-compatible provider


### Optional configuration

Application behavior can be changed by configuring the following environment variables:

#### Content
- `NEXT_PUBLIC_META_TITLE` (seen in search results and browser tab)
- `NEXT_PUBLIC_META_DESCRIPTION` (seen in search results)
- `NEXT_PUBLIC_NAV_TITLE` (seen in top-right navigation, defaults to domain when not configured)
- `NEXT_PUBLIC_NAV_CAPTION` (seen in top-right navigation, beneath title)
- `NEXT_PUBLIC_PAGE_ABOUT` (seen in grid sidebar—accepts rich formatting tags: `<b>`, `<strong>`, `<i>`, `<em>`, `<u>`, `<br>`)
- `NEXT_PUBLIC_DOMAIN_SHARE` (seen in share modals where a shorter url may be desirable)

#### Performance
> ⚠️ Enabling may result in increased project usage. Static optimization [troubleshooting hints](#why-do-production-deployments-fail-when-static-optimization-is-enabled) in FAQ.

- `NEXT_PUBLIC_STATICALLY_OPTIMIZE_PHOTOS = 1` enables static optimization for photo pages (`p/[photoId]`), i.e., renders pages at build time
- `NEXT_PUBLIC_STATICALLY_OPTIMIZE_PHOTO_OG_IMAGES = 1` enables static optimization for OG images, i.e., renders images at build time
- `NEXT_PUBLIC_STATICALLY_OPTIMIZE_PHOTO_CATEGORIES = 1` enables static optimization for photo categories (`tag/[tag]`, `shot-on/[make]/[model]`, etc.), i.e., renders pages at build time
- `NEXT_PUBLIC_STATICALLY_OPTIMIZE_PHOTO_CATEGORY_OG_IMAGES = 1` enables static optimization for photo category (`tag/[tag]`, `shot-on/[make]/[model]`, etc.) OG images, i.e., renders images at build time
- `NEXT_PUBLIC_PRESERVE_ORIGINAL_UPLOADS = 1` prevents photo uploads being compressed before storing
- `NEXT_PUBLIC_IMAGE_QUALITY = 1-100` controls the quality of large photos
- `NEXT_PUBLIC_BLUR_DISABLED = 1` prevents image blur data being stored and displayed (potentially useful for limiting Postgres usage)

#### Categories
- `NEXT_PUBLIC_CATEGORY_VISIBILITY`
  - Comma-separated value controlling which photo sets appear in grid sidebar and CMD-K menu, and in what order. For example, you could move cameras above tags, and hide film simulations, by updating to `cameras,tags,lenses,recipes`.
  - Accepted values:
     - `recents`
     - `years`
     - `tags` (default)
     - `cameras` (default)
     - `lenses` (default)
     - `recipes` (default)
     - `films` (default)
     - `focal-lengths`
- `NEXT_PUBLIC_EXHAUSTIVE_SIDEBAR_CATEGORIES = 1` always shows expanded sidebar content
- `NEXT_PUBLIC_HIDE_TAGS_WITH_ONE_PHOTO = 1` to only show tags with 2 or more photos

#### Display
- `NEXT_PUBLIC_HIDE_KEYBOARD_SHORTCUT_TOOLTIPS = 1` hides keyboard shortcut hints in areas like the main nav, and previous/next photo links
- `NEXT_PUBLIC_HIDE_EXIF_DATA = 1` hides EXIF data in photo details and OG images (potentially useful for portfolios, which don't focus on photography)
- `NEXT_PUBLIC_CATEGORY_IMAGE_HOVERS = 1` shows images when hovering over category links like cameras and lenses (⚠️ setting `NEXT_PUBLIC_STATICALLY_OPTIMIZE_PHOTO_CATEGORY_OG_IMAGES = 1` strongly recommended for responsive hover interactions)
- `NEXT_PUBLIC_HIDE_ZOOM_CONTROLS = 1` hides fullscreen photo zoom controls
- `NEXT_PUBLIC_HIDE_TAKEN_AT_TIME = 1` hides taken at time from photo meta
- `NEXT_PUBLIC_HIDE_SOCIAL = 1` removes X (formerly Twitter) button from share modal
- `NEXT_PUBLIC_HIDE_REPO_LINK = 1` removes footer link to repo

#### Grid
- `NEXT_PUBLIC_GRID_HOMEPAGE = 1` shows grid layout on homepage
- `NEXT_PUBLIC_GRID_ASPECT_RATIO = 1.5` sets aspect ratio for grid tiles (defaults to `1`—setting to `0` removes the constraint)
- `NEXT_PUBLIC_SHOW_LARGE_THUMBNAILS = 1` ensures large thumbnails on photo grid views (if not configured, density is based on aspect ratio)

#### Design
- `NEXT_PUBLIC_DEFAULT_THEME = light | dark` sets preferred initial theme (defaults to `system` when not configured)
- `NEXT_PUBLIC_MATTE_PHOTOS = 1` constrains the size of each photo, and displays a surrounding border, potentially useful for photos with tall aspect ratios (colors can be customized via `NEXT_PUBLIC_MATTE_COLOR` + `NEXT_PUBLIC_MATTE_COLOR_DARK`)

#### Settings
- `NEXT_PUBLIC_GEO_PRIVACY = 1` disables collection/display of location-based data (⚠️ re-compresses uploaded images in order to remove GPS information)
- `NEXT_PUBLIC_ALLOW_PUBLIC_DOWNLOADS = 1` enables public photo downloads for all visitors (⚠️ may result in increased bandwidth usage)
- `NEXT_PUBLIC_SITE_FEEDS = 1` enables feeds at `/feed.json` and `/rss.xml`
- `NEXT_PUBLIC_IGNORE_PRIORITY_ORDER = 1` prevents `priority_order` field affecting photo order
- `NEXT_PUBLIC_OG_TEXT_ALIGNMENT = BOTTOM` keeps OG image text bottom aligned (default is top)
      
💬 &nbsp;&nbsp;I18N
-

Partial internationalization (for non-admin, user-facing text) provided for a handful of languages. Configure locale by setting environment variable `NEXT_PUBLIC_LOCALE`.

### Supported Languages
- `en-us`
- `pt-br`
- `pt-pt`
- `id-id`
- `zh-cn`
- `bd-bn`




