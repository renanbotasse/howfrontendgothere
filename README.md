# How Frontend Got Here

A comprehensive editorial website documenting the history and fundamentals of frontend development — written for developers who already know the tools and want to understand what's underneath them.

**Author**: [Renan Botasse](https://renanbotasse.vercel.app)

---

## What It Is

An encyclopedia of 51 independent articles organized into 10 thematic blocks. No reading order required. Each piece covers a problem, a technology, or a decision that shaped frontend as it exists today.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | [Astro](https://astro.build) `^4.6` |
| Content | Markdown + MDX (`@astrojs/mdx ^3.0`) |
| Sitemap | `@astrojs/sitemap ^3.0` |
| Syntax highlighting | Shiki (`github-dark-dimmed`) |
| Fonts | DM Serif Display · Inter · DM Mono (Google Fonts) |
| Deployment | Vercel (static output) |

Zero JavaScript shipped to the client by default. The only runtime JS handles the mobile navigation drawer and the image zoom modal.

---

## Getting Started

```bash
# Install dependencies
npm install

# Start dev server (localhost:4321)
npm run dev

# Production build → /dist
npm run build

# Preview the production build locally
npm run preview
```

---

## Project Structure

```
howfrontendgothere/
├── public/
│   ├── favicon.png
│   └── images/               # 19 custom SVG diagrams
│
├── scripts/
│   └── import-articles.js    # CLI tool to import new articles
│
└── src/
    ├── content/
    │   └── encyclopedia/     # 51 .md article files
    │   
    │
    ├── data/
    │   └── navigation.js     # All article metadata (slug, num, title, block)
    │
    ├── layouts/
    │   └── Layout.astro      # Main layout: header + sidebar + main
    │
    ├── pages/
    │   ├── index.astro                  # Home page
    │   ├── encyclopedia/
    │   │   ├── index.astro              # Encyclopedia index
    │   │   └── [slug].astro            # Dynamic article pages
    │   └── series/
    │       └── [slug].astro            # Dynamic series pages
    │
    └── styles/
        └── global.css        # Full design system (~1000 lines)
```

---

## Content

### Encyclopedia

51 articles across 10 thematic blocks:

| # | Block | Articles |
|---|---|---|
| 1 | Web Fundamentals | 01 – 04 |
| 2 | The Early Web | 05 – 10 |
| 3 | Interface | 11 – 19 |
| 4 | Build | 20 – 23 |
| 5 | Server | 24 – 29 |
| 6 | Quality | 30 – 34 |
| 7 | Scale & Mobile | 35 – 39 |
| 8 | Infrastructure | 40 – 44 |
| 9 | The AI Era | 45 – 47 |
| 10 | Industry & Cost | 48 – 51 |

---

## Adding New Articles

### 1. Create the markdown file

Drop the file into the content folder:

```
src/content/encyclopedia/{slug}.md
```

The article body starts directly with prose — no YAML frontmatter, no H1. The layout renders the title automatically from `navigation.js`. Use standard Markdown headings, blockquotes, code blocks, and image references.

**Example structure:**

```markdown
## Background

The first browsers had no concept of...

## Why It Matters

Understanding this helps you see why...

> **Key takeaway**
> The platform is older than the tools built on top of it.
```

### 2. Register it in navigation.js

Add the article object to the correct block in `src/data/navigation.js`:

```js
{
  id: 'web-fundamentals',
  label: 'Web Fundamentals',
  articles: [
    { num: '01', slug: 'how-the-web-actually-works', title: 'How the Web Actually Works' },
    // add new article here
  ],
},
```

Each article object requires:

| Field | Type | Description |
|---|---|---|
| `num` | string | Zero-padded sequential number, e.g. `'01'`, `'51'` |
| `slug` | string | URL-safe filename without extension |
| `title` | string | Display title shown in sidebar and index pages |

### 3. Import via script (optional)

If you have articles in an `outputs/` directory adjacent to the project root, the import script moves them automatically:

```bash
node scripts/import-articles.js
```

The script reads `.md` files from `../outputs/encyclopedia/`, strips the H1 heading (since the layout renders it), and saves the result to `src/content/encyclopedia/{slug}.md`.

---

## SVG Diagrams

Custom diagrams live in `public/images/`. Reference them in articles with standard Markdown image syntax:

```markdown
![TCP/IP Layer Model](/images/tcp-ip-layers.svg)
*Caption text appears here as italic*
```

Current diagrams:

| File | Topic |
|---|---|
| `tcp-ip-layers.svg` | Network protocol stack |
| `dns-resolution.svg` | DNS lookup flow |
| `http-request-response.svg` | HTTP request/response cycle |
| `http1-vs-http2.svg` | Protocol evolution |
| `http2-vs-http3.svg` | Modern transport protocols |
| `critical-rendering-path.svg` | Browser rendering pipeline |
| `event-loop.svg` | JavaScript event loop |
| `websocket-connection.svg` | Real-time connections |
| `browser-market-share.svg` | Browser history & market share |
| `client-server.svg` | Client/server architecture |
| `islands-architecture.svg` | Partial hydration pattern |
| `flux-data-flow.svg` | Unidirectional state flow |
| `webpack-bundling.svg` | Module bundling process |
| `vite-vs-bundler.svg` | Build tool comparison |
| `css-grid-layout.svg` | CSS Grid visual model |
| `design-tokens.svg` | Design systems concepts |
| `baseline-features.svg` | Browser compatibility baseline |
| `core-web-vitals.svg` | Performance metrics |
| `tc39-stages.svg` | JavaScript proposal process |

On mobile, tapping any diagram opens a fullscreen zoom modal with native pinch-to-zoom support.

---

## Design System

All design tokens are CSS custom properties defined in `src/styles/global.css`.

### Colors

```css
/* Backgrounds */
--bg:           #131420   /* page background */
--bg-surface:   #1a1b2e   /* cards, sidebar hover */
--bg-elevated:  #1f2035   /* elevated surfaces */
--bg-code:      #0d0e1a   /* code blocks */

/* Text */
--text-primary:   #f7fafc
--text-secondary: #a0aec0
--text-muted:     #4a5568
--text-ghost:     #1e1f32  /* decorative only */

/* Borders */
--border:       #1d242f
--border-mid:   #252840

/* Accents */
--accent:  #ff3c00   /* orange-red — highlights, active markers */
--teal:    #16a394   /* links, active sidebar state */
```

### Fonts

```css
--font-display: 'DM Serif Display', Georgia, serif   /* titles, hero */
--font-sans:    'Inter', system-ui, sans-serif       /* body text */
--font-mono:    'DM Mono', 'Fira Code', monospace    /* labels, code */
```

### Layout

```css
--sidebar-width: 268px
--content-max:   680px
--header-height: 64px
```

The page uses a CSS grid: `268px sidebar | 1fr main`, with the header spanning full width. The main column centers its content at a max-width of 680px.

---

## Layout

### Header

Sticky, 64px tall, full width. Contains:

- Logo link → home
- Search button (⌘K shortcut placeholder)
- Navigation links: Encyclopedia
- Social icon links: HackerNoon · GitHub · LinkedIn · Portfolio
- Hamburger button (mobile only, visible at ≤900px)

### Sidebar

Sticky on desktop, scrollable independently. Content is conditional on the current path:

- On `/` and `/encyclopedia/*`: shows all 51 articles grouped by thematic block

The active article is highlighted with a teal left border and tinted background. On mobile the sidebar becomes a slide-in drawer.

### Article Pages

Each article page renders:

1. A header block with block/category tag, article title, and article number
2. The markdown body inside `.prose` (styled H2, H3, paragraphs, blockquotes, code blocks, images)
3. Previous / Next navigation at the bottom

---

## Responsive Behavior

| Breakpoint | Changes |
|---|---|
| `≤ 900px` | Sidebar becomes a fixed slide-in drawer · hamburger button appears · header search and nav links hide · home page block-row labels collapse · article prev/next nav stacks vertically |
| `≤ 480px` | Logo reduces to 1rem · padding tightens further · hero and article titles reduce via `clamp()` |

### Mobile Navigation Drawer

Tapping the hamburger button slides the sidebar in from the left with a `cubic-bezier` transition. A semi-transparent backdrop overlay with `backdrop-filter: blur(2px)` appears behind it. Tapping the overlay closes the drawer.

### Image Zoom Modal

On mobile (≤900px), tapping any image inside an article body opens a fullscreen modal. The image uses `touch-action: pinch-zoom` for native pinch-to-zoom. Dismiss by tapping outside the image, tapping the ✕ button, or pressing `Escape`.

---

## Build & Deployment

```bash
npm run build
```

Outputs a fully static site to `/dist`. No server required — deploy anywhere that serves static files.

**Vercel setup:**

1. Push to GitHub
2. Import the repository on Vercel
3. Framework preset: **Astro** (auto-detected)
4. Build command: `npm run build`
5. Output directory: `dist`

The `site` field in `astro.config.mjs` should match your deployment URL (used by the sitemap integration):

```js
export default defineConfig({
  site: 'https://howfrontendgothere.com',
  integrations: [mdx(), sitemap()],
  markdown: {
    shikiConfig: {
      theme: 'github-dark-dimmed',
      wrap: true,
    },
  },
});
```

Total build output: **53 pre-rendered HTML pages** (home + encyclopedia index + 51 encyclopedia articles).

---

## Author

**Renan Botasse**

- Portfolio: [renanbotasse.vercel.app](https://renanbotasse.vercel.app)
- GitHub: [github.com/renanbotasse](https://github.com/renanbotasse)
- LinkedIn: [linkedin.com/in/renanbotasse](https://www.linkedin.com/in/renanbotasse/)
- HackerNoon: [hackernoon.com/u/renanb](https://hackernoon.com/u/renanb)
