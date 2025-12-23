# Image optimization plugin analysis and fix

**Project:** sahar-eleventy-base-blog (The Curious Cat)
**Generated:** December 23, 2025 at 10:22 AM ET

## Problem summary

The `eleventyImageTransformPlugin` is commented out in [eleventy.config.js](../eleventy.config.js#L95-L113), causing poor Lighthouse performance scores. When enabled, images fail to load because the plugin cannot locate images referenced with absolute paths like `/img/cat-purr.jpg`.

## Root cause

The issue stems from a path resolution mismatch:

1. **Images are stored in:** `public/img/` (passthrough copied to `_site/img/`)
2. **Blog posts reference images as:** `<img src="/img/cat-purr.jpg">`
3. **The image transform plugin** looks for images relative to the content file's location or the project root—it cannot resolve absolute URL paths like `/img/...` to the `public/img/` directory

The `eleventyImageTransformPlugin` transforms `<img>` tags in rendered HTML, but when it encounters `/img/cat-purr.jpg`, it tries to find the file at that path from the project root (which doesn't exist) rather than in `public/img/`.

## Current image usage in blog posts

| Post | Image path in HTML |
|------|-------------------|
| first-post | `/img/cat-purr.jpg` |
| second-post | `/img/cat-looking-outside.jpg` |
| third-post | `/img/happy-cat.jpg` |
| fourth-post | `/img/outdoor-cat.jpg` |
| fifth-post | `/img/cats.jpg` |

All five blog posts use absolute paths starting with `/img/`.

## Recommended fix

**Option A: Move images to content directory (recommended)**

Move images from `public/img/` to co-locate them with blog posts, then use relative paths. This is the approach the eleventy-base-blog starter was designed for.

1. Move each image into its corresponding post folder:
   - `public/img/cat-purr.jpg` → `content/blog/first-post/cat-purr.jpg`
   - `public/img/cat-looking-outside.jpg` → `content/blog/second-post/cat-looking-outside.jpg`
   - `public/img/happy-cat.jpg` → `content/blog/third-post/happy-cat.jpg`
   - `public/img/outdoor-cat.jpg` → `content/blog/fourth-post/outdoor-cat.jpg`
   - `public/img/cats.jpg` → `content/blog/fifth-post/cats.jpg`

2. Update each blog post to use relative paths:
   ```html
   <!-- Before -->
   <img src="/img/cat-purr.jpg" alt="A cat purring">

   <!-- After -->
   <img src="./cat-purr.jpg" alt="A cat purring">
   ```

3. Uncomment the plugin in `eleventy.config.js` (lines 95-113)

4. Remove passthrough copy of images if no longer needed, or keep `public/img/` for shared images

**Option B: Configure urlPath in plugin**

If keeping images in `public/img/`, add the `urlPath` option to tell the plugin where optimized images should be served from:

```javascript
eleventyConfig.addPlugin(eleventyImageTransformPlugin, {
  formats: ["avif", "webp", "auto"],
  urlPath: "/img/",
  outputDir: "./_site/img/",
  failOnError: false,
  htmlOptions: {
    imgAttributes: {
      loading: "lazy",
      decoding: "async",
    }
  },
  sharpOptions: {
    animated: true,
  },
});
```

However, this still requires the plugin to locate the source images. Additional configuration with `transformOnRequest` or moving images may be necessary.

## Why option A is recommended

- Co-located images are the intended pattern for eleventy-base-blog
- The image transform plugin processes images during build, generating optimized versions alongside the content
- Relative paths work naturally with the plugin's path resolution
- Images stay organized with their associated posts

## Expected results after fix

When properly configured, the plugin will:

- Generate AVIF and WebP versions of each image
- Automatically add `width` and `height` attributes (prevents layout shift)
- Add `loading="lazy"` and `decoding="async"` attributes
- Convert `<img>` to `<picture>` elements with multiple source formats
- Significantly improve Lighthouse performance scores
