# BuildWithNavneet Portfolio

Personal portfolio for **Navneet Sharma — Cloud & AI Platform Architect**. The site is built with Astro and intentionally organized so content, page composition, reusable UI, document metadata, and styling can be changed independently.

## Architecture goals

The repository follows four rules:

1. **Pages compose; they do not contain the whole implementation.** A page should mainly import and arrange components.
2. **Content is centralized.** Repeated profile data, links, projects, certifications, and expertise live in one data module.
3. **UI responsibilities are separated.** Header, hero, content sections, footer, layout, and styling are maintained independently.
4. **Small changes should require small commits.** Changing a blog URL should not require editing navigation, contact, about, and footer separately.

---

# High-level design architecture

```text
Visitor / Browser
       |
       v
Astro page route
src/pages/index.astro
       |
       v
BaseLayout.astro ----------------------> global.css
  |        |
  |        +--> HTML document metadata
  |        +--> title and description
  |        +--> favicon
  |
  +--> Header.astro
  +--> Hero.astro
  +--> HomeSections.astro
  +--> Footer.astro
             |
             v
      src/data/portfolio.ts
      Central content and URLs
             |
             v
       public/ static assets
       images, favicon, resume
```

At build time, Astro combines the page, layout, components, data, styles, and static assets into the final website served to the browser.

## Main layers

| Layer | Responsibility | Location |
| --- | --- | --- |
| Routing | Defines which page is rendered for a URL | `src/pages/` |
| Composition | Decides which components appear and in what order | `src/pages/index.astro` |
| Layout | Owns the HTML shell and document metadata | `src/layouts/BaseLayout.astro` |
| Components | Render distinct parts of the interface | `src/components/` |
| Content/Data | Stores editable portfolio content and shared URLs | `src/data/portfolio.ts` |
| Presentation | Controls layout, responsive behavior, colors, animation, typography | `src/styles/global.css` |
| Static Assets | Files served directly by the site | `public/` |

---

# Repository structure

```text
buildwithnavneet-portfolio/
├── public/
│   ├── favicon.svg
│   ├── favicon.ico
│   ├── images/
│   │   └── navneet-profile.jpg
│   └── resume/
│       └── navneet-sharma-resume.pdf
│
├── src/
│   ├── components/
│   │   ├── Header.astro
│   │   ├── Hero.astro
│   │   ├── HomeSections.astro
│   │   └── Footer.astro
│   │
│   ├── data/
│   │   └── portfolio.ts
│   │
│   ├── layouts/
│   │   └── BaseLayout.astro
│   │
│   ├── pages/
│   │   ├── index.astro
│   │   └── favicon.ico.ts
│   │
│   └── styles/
│       └── global.css
│
├── astro.config.mjs
├── package.json
└── README.md
```

---

# Component responsibilities

## `src/pages/index.astro`

**Role:** Home-page composition root.

This file should stay small. It imports the layout and page components, then arranges them in display order.

```text
BaseLayout
├── Header
├── main
│   ├── Hero
│   └── HomeSections
└── Footer
```

**Change this file when:**

- adding a new top-level section component;
- removing a major page area;
- changing the order of major components.

**Do not put here:** large CSS blocks, repeated portfolio content, or the full markup for every section.

---

## `src/layouts/BaseLayout.astro`

**Role:** Shared HTML document shell.

Responsibilities:

- imports global styles;
- creates `html`, `head`, and `body`;
- sets the page title;
- sets the meta description;
- explicitly loads `/favicon.svg`;
- renders page content through Astro's `<slot />`.

The layout accepts optional `title` and `description` props. If they are not provided, it uses profile data from `portfolio.ts`.

**Change this file when:** adding SEO metadata, analytics, social preview metadata, shared scripts, fonts, or site-wide `<head>` elements.

---

## `src/components/Header.astro`

**Role:** Sticky navigation and personal brand entry point.

Responsibilities:

- displays the `N` brand mark and name;
- links to sections on the home page;
- links to the external blog;
- reads the name and blog URL from `portfolio.ts`.

**Change this file when:** changing navigation labels, adding a new navigation destination, or changing header-specific markup.

---

## `src/components/Hero.astro`

**Role:** First-screen introduction.

Responsibilities:

- renders positioning and headline;
- displays introductory copy;
- provides email and work calls to action;
- renders proof points;
- displays the profile image and decorative visual elements.

It reads `profile` and `proofPoints` from the central data file.

**Change this file when:** changing hero layout, CTA structure, portrait treatment, or hero-only visual elements.

**For text-only changes:** prefer editing `src/data/portfolio.ts`.

---

## `src/components/HomeSections.astro`

**Role:** Renders the content sections below the hero.

Current section order:

1. Experience / company trust strip
2. Ways I can help
3. Technical expertise
4. Selected work / case studies
5. Microsoft credentials
6. Professional profile / resume
7. About
8. Contact

The file contains section-level comments so a future contributor or AI coding agent can locate each area quickly.

It reads these collections from `portfolio.ts`:

- `companies`
- `collaboration`
- `expertise`
- `projects`
- `certifications`
- `profile`

**Change this file when:** changing markup or layout inside an existing content section.

**For content-only changes:** prefer editing `portfolio.ts`.

### Future improvement

If `HomeSections.astro` grows significantly, split each section into its own component:

```text
components/sections/
├── ExperienceSection.astro
├── ServicesSection.astro
├── ExpertiseSection.astro
├── WorkSection.astro
├── CredentialsSection.astro
├── ResumeSection.astro
├── AboutSection.astro
└── ContactSection.astro
```

This is not necessary yet, but it is the preferred next refactor when individual sections become more complex.

---

## `src/components/Footer.astro`

**Role:** Closing navigation and utility controls.

Responsibilities:

- displays the current year automatically;
- shows email, blog, and portfolio links;
- renders the fixed back-to-top control.

Shared links come from `portfolio.ts`.

**Change this file when:** changing footer markup or footer-specific navigation.

---

# Central data architecture

## `src/data/portfolio.ts`

This is the **single source of truth for portfolio content**.

It contains:

- `profile` — name, role, headline, biography, email, blog URL, portfolio URL, resume URL;
- `proofPoints` — short credibility metrics shown in the hero;
- `companies` — experience/trust-strip entries;
- `certifications` — Microsoft certification cards;
- `collaboration` — ways visitors can work with Navneet;
- `expertise` — technical capability cards;
- `projects` — selected work and case-study cards.

## Why centralize content?

Without a data layer, the same URL or text can be duplicated across multiple components. That creates inconsistent updates. For example, the blog URL is used in the header, about section, contact section, and footer. Because all components read `profile.blogUrl`, changing one value updates every location.

### Example data flow

```text
portfolio.ts
  profile.blogUrl
       |
       +--> Header.astro
       +--> HomeSections.astro / About
       +--> HomeSections.astro / Contact
       +--> Footer.astro
```

**Rule:** if the same content or URL may be used in more than one place, put it in `portfolio.ts` rather than hard-coding it repeatedly.

---

# Styling architecture

## `src/styles/global.css`

This file owns the visual system for the current single-page portfolio.

It includes:

- CSS custom properties / design tokens;
- global reset and base typography;
- shared `.shell` content width;
- header and navigation styling;
- hero layout and animation;
- cards and section layouts;
- credentials and resume styling;
- contact and footer styling;
- responsive breakpoints;
- reduced-motion accessibility behavior.

### Design tokens

The `:root` variables define the reusable palette:

```text
--bg          page background
--surface     elevated surface
--soft        alternate section background
--card        card background
--text        primary text
--muted       secondary text
--border      light border
--accent      brand purple
--dark        dark section background
--dark-card   card on dark background
--dark-border border on dark background
```

**Change a token when:** updating a color across the whole site.

**Change a selector when:** updating the visual behavior of one component or section.

### Future styling rule

If the site grows to multiple pages, consider splitting styles into:

```text
styles/
├── tokens.css
├── base.css
├── layout.css
└── components/
```

For the current portfolio size, one documented global stylesheet keeps the system simpler.

---

# Static asset architecture

## `public/`

Astro serves files in `public/` directly from the website root.

Examples:

```text
public/favicon.svg
        -> /favicon.svg

public/images/navneet-profile.jpg
        -> /images/navneet-profile.jpg

public/resume/navneet-sharma-resume.pdf
        -> /resume/navneet-sharma-resume.pdf
```

Use `public/` for assets that should be served unchanged.

### Favicon flow

```text
BaseLayout.astro
<link rel="icon" href="/favicon.svg" />
        |
        v
public/favicon.svg
        |
        v
Browser tab icon
```

The favicon is explicitly declared in the layout so the browser does not need to guess its location.

---

# Low-level implementation flow

When a visitor opens the home page:

```text
1. Browser requests /
2. Astro maps / to src/pages/index.astro
3. index.astro creates BaseLayout
4. BaseLayout imports global.css
5. BaseLayout builds the HTML document and <head>
6. BaseLayout loads /favicon.svg
7. index.astro renders Header
8. index.astro renders Hero
9. index.astro renders HomeSections
10. index.astro renders Footer
11. Components read shared content from portfolio.ts
12. Static assets are loaded from public/
13. Final HTML and CSS are delivered to the browser
```

## Dependency direction

Dependencies should flow in one direction:

```text
Page
  -> Layout
  -> Components
  -> Data
  -> Static assets

Styles are imported by the layout and apply to the rendered UI.
```

Avoid making the data layer import UI components. Avoid making one unrelated section depend on another section's internal implementation.

---

# Common change guide

| I want to... | Edit... |
| --- | --- |
| Change name, role, headline, bio, or URLs | `src/data/portfolio.ts` |
| Add or edit a project | `src/data/portfolio.ts` |
| Add or edit a certification | `src/data/portfolio.ts` |
| Change navigation markup | `src/components/Header.astro` |
| Change the hero layout | `src/components/Hero.astro` |
| Change a homepage section layout | `src/components/HomeSections.astro` |
| Change footer markup | `src/components/Footer.astro` |
| Add SEO or favicon metadata | `src/layouts/BaseLayout.astro` |
| Change colors, spacing, responsive behavior | `src/styles/global.css` |
| Change the profile image | `public/images/navneet-profile.jpg` |
| Replace the resume | `public/resume/navneet-sharma-resume.pdf` |
| Reorder major page components | `src/pages/index.astro` |

---

# Local development

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

Build the production site:

```bash
npm run build
```

Preview the production build locally:

```bash
npm run preview
```

Before pushing a structural change, run at least:

```bash
npm run build
```

This catches missing imports, invalid Astro syntax, and build-time failures.

---

# Contribution and maintenance rules

## For humans

- Keep `index.astro` focused on composition.
- Prefer changing central data over duplicating content in components.
- Add a short comment before a non-obvious section or implementation decision.
- Do not put large CSS blocks back into page files.
- Run the production build before pushing structural changes.

## For AI coding agents

Before changing the repository:

1. Read this README.
2. Identify the smallest file responsible for the requested change.
3. Read the current file before editing it.
4. Preserve the existing architecture unless the task explicitly requires a refactor.
5. Update `portfolio.ts` for shared content and URLs.
6. Update a component for markup changes.
7. Update `global.css` for visual changes.
8. Update `BaseLayout.astro` for document-level metadata.
9. Keep commits focused and describe the actual change.
10. Do not claim a deployment is working unless it has been verified.

---

# Current architectural limitations

The current structure is intentionally lightweight, but there are areas to improve as the site grows:

- `HomeSections.astro` contains several sections and should be split when those sections gain more logic or markup.
- Styling is global; multi-page growth may justify component-scoped or layered CSS.
- There is no automated test suite yet.
- Deployment verification is external to the repository unless CI is added.
- `public/favicon.ico` and `src/pages/favicon.ico.ts` are legacy favicon paths; the primary favicon is the explicitly linked `public/favicon.svg`.

---

# Recommended next architecture improvements

```text
Phase 1 — Current
Modular page + central data + global styles

Phase 2 — As content grows
Split HomeSections into section components

Phase 3 — As the site becomes multi-page
Add reusable SEO component and layered styles

Phase 4 — As delivery matures
Add CI build checks, link validation, and deployment verification
```

The guiding principle is simple: **keep content centralized, keep components focused, and make every future change touch the smallest possible part of the repository.**
