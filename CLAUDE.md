# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This is "The Curious Cat" - a student blog project about cats built with Eleventy v3 (eleventy-base-blog v9 starter). The site features blog posts about feline behavior and care, authored by Sahar Naz.

## Common commands

```bash
# Install dependencies
npm install

# Start dev server with live reload (drafts included)
npm start

# Build for production (drafts excluded)
npm run build

# Debug mode
npm run debug
```

## Architecture

### Directory structure

- `content/` - All pages and blog posts (markdown with front matter)
  - `blog/` - Blog posts (each in its own folder with index.md)
  - `index.njk` - Home page
  - `about.md`, `blog.njk`, `tags.njk` - Other pages
- `_includes/layouts/` - Nunjucks layout templates
  - `base.njk` - Top-level HTML structure
  - `home.njk` - Home page layout (extends base)
  - `post.njk` - Blog post layout (extends base)
- `_data/` - Global data files
  - `metadata.js` - Site metadata (title, URL, author)
  - `eleventyDataSchema.js` - Zod schema for draft validation
- `_config/` - Eleventy configuration modules
  - `filters.js` - Custom Nunjucks filters (dates, array helpers)
- `css/` - Stylesheets (bundled via eleventy-plugin-bundle)
- `public/` - Static assets (copied to _site root)
  - `fonts/` - Montserrat and RobotoMono font files
  - `img/` - Blog post images
- `_site/` - Build output (gitignored)

### Key configuration

The `eleventy.config.js` uses ESM format with these plugins:
- **feedPlugin** - Atom feed generation
- **eleventyImageTransformPlugin** - Auto image optimization (AVIF, WebP)
- **pluginSyntaxHighlight** - Code syntax highlighting
- **pluginNavigation** - Navigation menu from front matter

### Content patterns

- Blog posts require `posts` tag (set automatically via `blog.11tydata.js`)
- Use `draft: true` in front matter to exclude from production builds
- Navigation controlled via `eleventyNavigation` key in front matter
- Images in posts use standard HTML `<img>` tags pointing to `/img/`

### Deployment

Configured for Netlify (`netlify.toml`) and Vercel (`vercel.json`). Build output is `_site/`.
