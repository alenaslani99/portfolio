# AGENTS.md

## Package Manager

- **Use `pnpm`**. The repo requires `shamefully-hoist=true` (set in `.npmrc`) for Astro dependencies to resolve correctly under pnpm.
- `pnpm-workspace.yaml` exists but only allows builds for native deps (`esbuild`, `sharp`). This is **not a monorepo**.

## Dev Commands

- `pnpm dev` — start Astro dev server
- `pnpm build` — static production build (output to `dist/`)
- `pnpm preview` — preview the static build locally
- **No test, lint, or format scripts are configured.** Do not assume they exist.

## Architecture

- **Framework:** Astro 4 with static site generation.
- **Styling:** TailwindCSS 3 + DaisyUI. Theme is set to `"lofi"` via `data-theme` on the `<html>` tag in `src/layouts/BaseLayout.astro`.
- **Path aliases:** `@components/*` → `src/components/*`, `@layouts/*` → `src/layouts/*`.
- **Global config:** `src/config.ts` exports `SITE_TITLE`, `SITE_DESCRIPTION`, `GENERATE_SLUG_FROM_TITLE`, and `TRANSITION_API`. Import this for any site-wide values.
- **Content collections:** Defined in `src/content/config.ts`. Collection is `blog` only. Query with Astro's `getCollection()`.
- **RSS:** Generated at `/rss.xml.js`. Uses `import.meta.env.SITE` which comes from `astro.config.mjs`.
- **Sitemap plugin removed:** `@astrojs/sitemap` was causing build failures (`Cannot read properties of undefined (reading 'reduce')`) and was removed from `astro.config.mjs`.

## Slug Behavior

- Blog post slugs are generated from the post `title` by default (see `src/lib/createSlug.ts`).
- To use the raw file-based slug instead, set `GENERATE_SLUG_FROM_TITLE = false` in `src/config.ts`.
- If you change this flag, remember that existing internal links and RSS items may break unless you handle redirects.

## Deployment Constraint

- **Must deploy as static.** The blog pagination uses dynamic route parameters in filenames (`[...page].astro`), which is **incompatible with SSR** configs. Use Astro's default static output.

## Content Gotchas

- Blog posts go in `src/content/blog/` as `.md` files with frontmatter: `title`, `description`, `pubDate`, `heroImage` (optional), `badge` (optional), `tags` (optional, must be unique array).
- When adding a new page, update `src/components/SideBarMenu.astro` and set the matching `sideBarActiveItemID` prop on `BaseLayout`.

## Build Artifacts

- `dist/` — static build output
- `.astro/` — Astro cache and generated types (do not commit)
