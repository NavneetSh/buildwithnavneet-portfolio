# Website Operations & Maintenance Handbook

This document explains how to operate, update, troubleshoot, and maintain the BuildWithNavneet portfolio after handover. It is written for the site owner, a future developer, or an AI coding agent.

For code architecture and component responsibilities, read `README.md` first.

---

# 1. What you are responsible for after handover

The website has four operational areas:

1. **Content** — profile text, projects, certifications, links, resume, and images.
2. **Code** — Astro components, layout, and CSS.
3. **Source control** — Git and GitHub history, branches, commits, and rollback.
4. **Hosting and domain** — deployment status, custom domain, DNS, and HTTPS.

A normal content update should usually require only a small Git commit. Infrastructure or domain changes should be treated more carefully because they can make the whole website unavailable.

---

# 2. Day-to-day operating model

Use this workflow for normal changes:

```text
Decide what must change
        |
        v
Identify the responsible file
        |
        v
Edit locally or through an approved coding agent
        |
        v
Run npm run build
        |
        v
Review the change
        |
        v
Commit and push to GitHub
        |
        v
Wait for deployment
        |
        v
Verify the live website
```

Do not consider a task complete only because code was pushed. A website change is complete when the production site has been checked.

---

# 3. Initial setup on a new computer

Requirements:

- Git
- Node.js
- npm
- access to the GitHub repository
- access to the hosting provider
- access to the domain/DNS provider

Clone the repository:

```bash
git clone https://github.com/NavneetSh/buildwithnavneet-portfolio.git
cd buildwithnavneet-portfolio
```

Install dependencies:

```bash
npm install
```

Start the local development server:

```bash
npm run dev
```

Open the local address shown by Astro in the terminal.

Before making changes, confirm the current project builds:

```bash
npm run build
```

---

# 4. How to make common updates

## Change profile text or links

Edit:

```text
src/data/portfolio.ts
```

Use this for:

- name;
- role;
- headline;
- introduction;
- biography;
- email address;
- portfolio URL;
- blog URL;
- resume URL.

Because components read from this central file, do not manually update the same URL in several components.

## Add or edit a project

Edit the `projects` collection in:

```text
src/data/portfolio.ts
```

Keep each project entry consistent with the existing structure.

After adding a project, check desktop and mobile layouts because the number of cards may affect the grid.

## Add or edit a certification

Edit the `certifications` collection in:

```text
src/data/portfolio.ts
```

Then verify the credential-card layout on desktop and mobile.

## Change the profile photo

Replace:

```text
public/images/navneet-profile.jpg
```

Prefer keeping the same filename so no code change is needed.

After replacement, verify:

- face position inside the crop;
- desktop appearance;
- mobile appearance;
- image file size.

Avoid unnecessarily large images. Optimize the image before committing it.

## Replace the resume

Replace:

```text
public/resume/navneet-sharma-resume.pdf
```

Keep the same filename unless you also update `profile.resumeUrl` in `src/data/portfolio.ts`.

After deployment, click the resume button on the live website and confirm the correct file opens or downloads.

## Change colors or spacing

Edit:

```text
src/styles/global.css
```

For site-wide colors, change the CSS variables under `:root` first rather than replacing individual color values throughout the file.

## Change navigation

Edit:

```text
src/components/Header.astro
```

If a navigation link points to a section ID, confirm that the destination section has the matching `id` attribute.

## Change SEO metadata or favicon

Edit:

```text
src/layouts/BaseLayout.astro
```

The primary favicon file is:

```text
public/favicon.svg
```

After changing a favicon, browser caching may delay visible updates. Verify the favicon file directly and test in a fresh private/incognito window.

---

# 5. Safe change procedure

Before editing:

```bash
git status
```

Make sure you understand any existing uncommitted changes.

Get the latest code:

```bash
git pull
```

Make the smallest necessary change.

Run:

```bash
npm run build
```

Review changed files:

```bash
git status
git diff
```

Commit:

```bash
git add <changed-files>
git commit -m "Describe the actual change"
```

Push:

```bash
git push
```

Good commit messages:

```text
Update portfolio blog URL
Add platform engineering case study
Replace professional profile PDF
Improve mobile hero spacing
```

Avoid vague messages such as:

```text
changes
fix
update
stuff
```

---

# 6. Production verification checklist

After every deployment, verify the parts affected by the change.

For a normal release, check:

- home page loads;
- favicon appears;
- navigation works;
- blog link opens the correct domain;
- email link works;
- project cards render correctly;
- resume link works;
- profile image loads;
- mobile layout is usable;
- browser console has no obvious errors;
- HTTPS works;
- custom domain resolves correctly.

For visual changes, test at least:

```text
Desktop
Tablet-sized browser width
Mobile-sized browser width
```

---

# 7. Deployment operations

The repository is the source of truth. The hosting platform should deploy from the configured production branch, normally `main`.

Typical deployment flow:

```text
Commit pushed to main
        |
        v
Hosting provider detects change
        |
        v
Dependencies installed
        |
        v
Production build runs
        |
        v
New deployment created
        |
        v
Custom domain serves new version
```

If a deployment fails:

1. Open the hosting provider's deployment dashboard.
2. Find the failed deployment.
3. Read the build log from the first meaningful error, not only the final failure line.
4. Reproduce locally with `npm run build`.
5. Fix the cause.
6. Commit and push the fix.
7. Verify the new deployment.

Do not repeatedly redeploy unchanged broken code.

---

# 8. Domain and DNS maintenance

Treat domain and DNS access as critical infrastructure.

Maintain access to:

- domain registrar;
- DNS provider;
- hosting provider;
- GitHub account.

Record internally, but never commit secrets to this repository:

- which company owns the domain registration;
- where DNS is managed;
- which hosting project serves production;
- which GitHub repository and branch deploy production;
- renewal dates and billing ownership.

For a domain incident, check in this order:

```text
1. Is the GitHub repository available?
2. Did the latest deployment succeed?
3. Does the hosting-provider default URL work?
4. Does the custom domain resolve?
5. Are DNS records still correct?
6. Is HTTPS certificate issuance healthy?
7. Has the domain expired?
```

Do not change DNS records without recording the previous values first.

---

# 9. Security and access maintenance

Never commit:

- API keys;
- passwords;
- access tokens;
- private certificates;
- `.env` files containing secrets;
- hosting credentials;
- domain registrar credentials.

Before pushing:

```bash
git diff --cached
```

Review exactly what will be committed.

Operational recommendations:

- enable two-factor authentication on GitHub;
- enable two-factor authentication on the domain registrar;
- enable two-factor authentication on the hosting provider;
- use a password manager;
- keep recovery codes offline and secure;
- remove access for people who no longer maintain the site;
- review third-party integrations periodically.

If a secret is accidentally committed, deleting it from the latest file is not enough. Revoke or rotate the secret immediately, then clean Git history if necessary.

---

# 10. Dependency maintenance

Check dependencies periodically, especially before significant new development.

See outdated packages:

```bash
npm outdated
```

Install normal compatible updates:

```bash
npm update
```

After dependency changes:

```bash
npm run build
npm run dev
```

Then visually test the site.

Do not perform major framework upgrades casually. For major Astro or build-tool upgrades:

1. create a separate branch;
2. read the migration notes;
3. update dependencies;
4. run the build;
5. test the site locally;
6. review the diff;
7. merge only after verification.

Recommended maintenance frequency:

```text
Monthly      Check site, links, and deployment health
Quarterly    Review dependencies and access
Yearly       Review domain renewal, ownership, and documentation
As needed    Update content, resume, projects, and certifications
```

---

# 11. Backup and recovery model

GitHub is the primary code history and recovery mechanism.

A recoverable website requires:

- repository history;
- access to GitHub;
- access to hosting;
- access to DNS/domain management;
- copies of important source assets such as the resume and profile image.

For important non-code source files, keep an additional private backup outside the repository.

Do not rely on the live website as the only copy of an asset.

---

# 12. How to roll back a bad release

## Preferred approach: revert the bad commit

Find recent commits:

```bash
git log --oneline
```

Revert the bad commit:

```bash
git revert <commit-sha>
```

Push:

```bash
git push
```

This creates a new commit that reverses the bad change and preserves history.

## Avoid this for normal recovery

```bash
git reset --hard
git push --force
```

Force-pushing shared production history can remove commits and complicate recovery. Use it only when you fully understand the consequences.

---

# 13. Incident response playbook

## Website is completely down

Check:

1. hosting-provider status;
2. latest deployment status;
3. hosting default URL;
4. custom-domain DNS;
5. domain expiration;
6. HTTPS certificate status.

If the latest release caused the outage, revert it.

## Website loads but looks broken

Check:

1. browser console;
2. failed image or CSS requests;
3. latest deployment logs;
4. recent CSS or component changes;
5. mobile and desktop separately.

## A link is wrong

Search the central data file first:

```text
src/data/portfolio.ts
```

If the URL is shared, fix it there rather than patching individual components.

## Favicon is missing

Check:

```text
public/favicon.svg
src/layouts/BaseLayout.astro
```

Confirm the layout contains the explicit favicon link and the file can be requested directly from `/favicon.svg`.

Then test in a private/incognito browser window because favicon caches can be persistent.

## Deployment succeeds but change is not visible

Check:

1. the correct branch was pushed;
2. the hosting project is connected to the correct repository;
3. the deployment used the expected commit;
4. the custom domain points to that hosting project;
5. browser/CDN caching.

---

# 14. Handover access checklist

A complete handover is not finished until the new maintainer has verified access to all required systems.

The new maintainer should have:

- GitHub repository access;
- ability to clone and push code;
- hosting-provider access;
- permission to view deployment logs;
- permission to trigger or manage deployments;
- domain registrar access;
- DNS management access;
- access to billing ownership information where appropriate;
- access to source versions of important assets;
- this operations handbook;
- the architecture README.

The new maintainer should personally verify:

```text
[ ] Repository can be cloned
[ ] npm install works
[ ] npm run build succeeds
[ ] Local development server starts
[ ] A test change can be committed safely
[ ] Deployment dashboard is accessible
[ ] Production domain ownership is understood
[ ] DNS provider is known
[ ] Domain renewal responsibility is known
[ ] Rollback procedure is understood
```

---

# 15. Information that should be recorded privately

The following information is operationally important but should not be stored publicly in this repository:

```text
Production hosting provider:
Production project name:
Production branch:
Hosting account owner:

Domain registrar:
Domain account owner:
Domain renewal date:
Auto-renew enabled:

DNS provider:
DNS account owner:

Billing owner:
Emergency contact:
Backup maintainer:
```

Store this information in a secure private password manager or internal handover document.

---

# 16. Rules for future maintainers and AI agents

1. Read `README.md` for architecture before structural changes.
2. Read this file before deployment, maintenance, or recovery work.
3. Make the smallest responsible change.
4. Never claim production is fixed only because code was committed.
5. Run `npm run build` before pushing structural changes.
6. Review the diff before committing.
7. Keep secrets out of Git.
8. Prefer `git revert` for production rollback.
9. Verify the live site after deployment.
10. Update documentation when architecture or operational ownership changes.

---

# 17. Definition of done

A website task is complete only when:

```text
[ ] The requested change is implemented
[ ] The production build succeeds
[ ] The diff has been reviewed
[ ] The change is committed with a meaningful message
[ ] The commit is pushed
[ ] The deployment succeeds
[ ] The affected production behavior is verified
[ ] Documentation is updated if the operating model changed
```

The operating principle for this website is: **small changes, visible history, verified deployments, documented ownership, and simple recovery.**
