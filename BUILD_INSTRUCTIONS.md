# Miniature Goose Status Page — Build Instructions

This document is the complete context for building and theming the MG status page. Hand it to a Claude session with: "Look at this file — it's the plan for how the status page needs to be built."

---

## What This Is

A public status page at `status.miniaturegoose.com` showing uptime, response times, and incident history for all Miniature Goose services. Built on [Upptime](https://upptime.js.org) — an open-source, GitHub-powered uptime monitor that deploys as a static site to GitHub Pages.

**Why Upptime / GitHub Pages:** The status page must be hosted externally — if our infrastructure goes down, the status page needs to still show "offline" rather than also being broken. GitHub Pages is free, reliable, and decoupled from our servers.

**Repo:** `Miniature-Goose/status` on GitHub (this repo)

---

## Architecture

Upptime works like this:
1. **GitHub Actions** run on a cron (every 5 min) and ping each monitored URL
2. Results are committed to the repo as YAML/JSON data files
3. A **SvelteKit static site** is built from those data files and deployed to GitHub Pages
4. **Incidents** are managed via GitHub Issues (labeled `incident`) — create an issue to post an incident, close it to resolve

The status page is the SvelteKit app. Upptime supports theming via:
- `status-website.customCss` in `.upptimerc.yml` — injected into the page
- `status-website.customJs` — injected script
- Full template override by forking the `upptime/status-page` repo (SvelteKit)

---

## Design System — MG Tokens

The status page must match the Miniature Goose platform aesthetic. Here are the exact design tokens:

### Dark Mode (primary — status pages are dark-first)
```css
--mg-bg: #000000;
--mg-surface: #0A0B0D;
--mg-surface-alt: #111216;
--mg-border: #1A1B1F;
--mg-text: #E4E4E7;
--mg-body: #B0B0B8;
--mg-muted: #71717A;
--mg-accent: #8BC4A3;        /* MG green */
--mg-header-shadow: none;
```

### Light Mode
```css
--mg-bg: #F5F6FA;
--mg-surface: #FFFFFF;
--mg-surface-alt: #E6E8EF;
--mg-border: #E5E7EB;
--mg-text: #373737;
--mg-body: #4B5563;
--mg-muted: #9CA3AF;
--mg-accent: #558B6E;
--mg-header-shadow: 0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04);
```

### Status Colors
```css
--operational:   #8BC4A3 (dark) / #558B6E (light)
--degraded:      #F59E0B (dark) / #D97706 (light)
--down:          #EF4444 (dark) / #DC2626 (light)
--maintenance:   #60A5FA (dark) / #2563EB (light)
```

### Typography
- **Font family:** Space Grotesk (self-hosted, NOT Google Fonts CDN)
  - Weights: 300, 400, 500, 600, 700
  - Load via `@font-face` with woff2 files committed to the repo
- **Monospace:** JetBrains Mono (for timestamps, response times, percentages)
  - Weights: 400, 700
- **Body text:** 14px, weight 500, color `--mg-body`
- **Headings:** weight 600, color `--mg-text`
- **Eyebrow labels:** 13px, weight 600, uppercase, letter-spacing 0.06em, color `--mg-muted`
- **Tabular data:** Always use `font-variant-numeric: tabular-nums`

### Glass Header (Navbar)
The status page header should use the platform's glass-header pattern:
```css
background: color-mix(in oklab, var(--mg-surface) 92%, transparent);
backdrop-filter: blur(16px) saturate(1.6);
border: 1px solid var(--mg-border);
border-radius: 14px;
```

### Card Pattern
```css
background: var(--mg-surface);
border: 1px solid var(--mg-border);
border-radius: 14px;
```

### Status Pills
```css
font-size: 12px;
font-weight: 500;
padding: 3px 10px;
border-radius: 99px;
/* color + background from status color dims (15% opacity) */
```

### Status Dots
```css
width: 8px;
height: 8px;
border-radius: 50%;
box-shadow: 0 0 6px <status-color at 40% opacity>;
```

---

## Goose Logo SVG

Use this inline SVG for the header logo (26px height). It uses `currentColor` so it adapts to light/dark:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="430 180 150 160" style="height:26px;width:auto" aria-hidden="true">
  <path fill="currentColor" d="M556.150024,278.071960 C564.027161,298.314331 556.626282,317.061188 536.268433,326.268524 C512.511719,337.013031 488.313873,336.729340 465.814453,323.341705 C447.576813,312.489960 438.447876,295.202850 438.639282,273.645416 C438.695160,267.351654 441.330261,266.115051 446.351654,269.932129 C451.262421,273.665192 456.208618,273.409363 461.376556,271.119659 C470.533630,267.062561 479.800629,263.225037 489.838348,262.213654 C495.406067,261.652649 501.076691,262.112976 506.668274,262.112976 C507.225464,258.709229 505.178650,257.389679 504.018616,255.723434 C497.316406,246.096329 491.140045,236.299805 490.335907,224.083206 C490.146210,221.201263 490.055084,218.196274 490.964844,215.697342 C493.409027,208.983704 493.647186,202.096710 494.121429,195.124405 C494.580261,188.378281 497.270630,187.135437 503.424530,190.067932 C504.175934,190.426010 504.993530,190.698914 505.655579,191.182343 C511.080109,195.142929 515.961548,194.678101 521.424133,190.612671 C527.343384,186.207352 529.257996,187.333389 530.502197,194.419647 C530.728882,195.710739 530.875488,197.363785 531.714844,198.146057 C540.931213,206.735214 546.890625,219.120987 560.369812,222.791702 C562.276733,223.311005 564.354126,224.466599 563.966797,226.920776 C563.557007,229.517227 561.213013,230.178055 559.003296,230.212708 C552.344482,230.317078 545.681946,230.160538 539.021973,230.224274 C529.569885,230.314728 526.446228,235.846237 531.278198,243.985947 C535.130188,250.474854 540.580688,255.711456 545.349243,261.479492 C549.504639,266.505890 553.308594,271.739349 556.150024,278.071960 M528.891052,226.352356 C537.776245,221.078842 539.137390,214.173584 532.391296,207.358154 C526.711121,201.619644 519.752502,199.327637 511.841187,201.487137 C504.171478,203.580719 499.313660,208.705292 497.244812,216.443268 C495.067841,224.585617 497.824219,231.920822 501.141327,239.041855 C505.687225,248.800720 512.621765,256.941406 519.576294,265.033722 C520.802185,266.460175 522.038757,267.998138 520.631226,269.903503 C519.372192,271.607758 517.656738,270.848755 516.056335,270.379456 C502.640350,266.445923 489.470093,267.601624 476.336243,271.947052 C466.309387,275.264526 457.134857,282.708374 444.957184,277.401672 C445.874084,291.097198 451.043427,301.498291 459.321167,310.320221 C477.740479,329.950409 520.033447,332.757172 541.085205,315.965515 C553.522095,306.045380 556.456726,292.259613 549.140381,278.077728 C545.284668,270.603943 539.907349,264.251434 534.316772,258.062836 C530.736633,254.099701 527.264404,250.039658 524.766846,245.293976 C520.853271,237.857498 522.074219,231.724258 528.891052,226.352356z"/>
  <path fill="currentColor" d="M463.298279,292.158630 C466.082397,288.017334 468.520386,291.004059 470.568970,292.387665 C478.727081,297.897736 487.475952,301.562805 497.378510,302.346893 C508.913025,303.260162 520.830322,298.265045 523.916016,286.814453 C524.459229,284.798615 525.531067,282.834534 528.115723,283.732086 C530.513306,284.564667 530.416809,286.674561 529.955566,288.766815 C527.948242,297.873016 517.787964,306.625671 507.322784,308.239685 C491.698944,310.649323 478.171509,305.538971 465.731750,296.594482 C464.361969,295.609589 463.242157,294.363190 463.298279,292.158630z"/>
  <path fill="currentColor" d="M526.540527,216.277344 C524.610535,218.954437 522.473938,219.690216 520.028687,217.823547 C518.296631,216.501343 517.699219,214.612244 518.743591,212.568985 C519.738159,210.623108 521.431824,209.546387 523.607544,210.265396 C526.261169,211.142410 527.697021,212.945114 526.540527,216.277344z"/>
</svg>
```

---

## Theming Approach

There are two options, in order of preference:

### Option A: Custom CSS Override (simpler, recommended first)

Upptime's `.upptimerc.yml` supports `status-website.customCss`. This injects CSS into the default SvelteKit status page. You can override nearly everything visually without forking:

1. Write the full MG theme as a CSS string in `customCss`
2. Override Upptime's default Tailwind classes with MG tokens
3. Inject the goose logo via CSS `::before` pseudo-element or via `customJs`
4. Self-host fonts by committing woff2 files to the repo and referencing them with relative paths

This approach is fragile across Upptime version upgrades (CSS selectors can change), but it's fast to ship.

### Option B: Fork `upptime/status-page` (full control)

Fork `upptime/status-page` into `Miniature-Goose/mg-status-page`. This gives full control over the SvelteKit app:

1. Fork the repo
2. Replace the layout, components, and styles entirely with MG-themed versions
3. Point `.upptimerc.yml` at the fork: `status-website.repo: Miniature-Goose/mg-status-page`

This is the better long-term approach if we want pixel-perfect control (glass header, uptime bars, incident timeline matching the mockup exactly).

### Recommendation

Start with **Option A** to get something live fast. If the CSS overrides hit limitations, move to **Option B**. The mockup artifact (see below) is the visual target for either approach.

---

## Visual Reference

A high-fidelity mockup was built as a Claude artifact. It demonstrates:
- Glass navbar with goose logo and "Miniature Goose Status" branding
- Overall status banner (green operational, swappable to amber/red)
- Service list with response times, status dots, and status pills
- 90-day uptime bars per service with hover states
- Incident timeline with investigating → identified → monitoring → resolved states
- Light/dark mode support
- Responsive mobile layout

The mockup HTML can be found by asking the owner for the artifact link, or rebuild it from the design tokens above. The CSS in the mockup is the ground truth for how every element should look.

---

## Monitored Services

Update these in `.upptimerc.yml` as services change:

| Service | URL | Notes |
|---------|-----|-------|
| Client Portal | `https://miniaturegoose.com` | Main website, expects 200/301/302 |
| Game Panel | `https://miniaturegoose.com/dashboard` | Redirects to login if unauthenticated (302 is OK) |
| API | `https://miniaturegoose.com/api/v1/health` | Health endpoint (create if doesn't exist) |
| Documentation | `https://miniaturegoose.com/docs/` | MkDocs static site |
| Billing & Payments | `https://miniaturegoose.com/store` | May redirect unauthenticated users |
| Wings (US-East) | `$WINGS_URL` | Pterodactyl Wings node — URL stored as repo secret |

### Future monitors to add:
- Additional Wings nodes as they come online
- `status.miniaturegoose.com` itself (meta-monitor, optional)
- Authentik SSO (when public-facing)
- MinnieAI API (when launched)

---

## DNS Setup

1. Add CNAME record in Cloudflare:
   - Name: `status`
   - Target: `miniature-goose.github.io`
   - Proxy: OFF (GitHub Pages needs direct DNS)
2. In GitHub repo settings → Pages → Custom domain: `status.miniaturegoose.com`
3. Enable "Enforce HTTPS"

---

## Repo Secrets Required

Set these in GitHub repo Settings → Secrets and variables → Actions:

| Secret | Purpose |
|--------|---------|
| `GH_PAT` | GitHub Personal Access Token with `repo` scope (Upptime needs write access to commit uptime data) |
| `WINGS_URL` | Wings node health URL (e.g. `https://node1.miniaturegoose.com:8080`) |
| `DISCORD_WEBHOOK` | (Optional) Discord webhook for status alerts in a `#status-alerts` channel |

---

## Incident Management

Upptime uses GitHub Issues for incidents:

1. **Create incident:** Open a new issue with the `incident` label
2. **Post updates:** Add comments to the issue (each comment = an update on the status page)
3. **Resolve:** Close the issue — it moves to "Past Incidents" automatically
4. **Scheduled maintenance:** Create an issue with both `incident` and `maintenance` labels

Issue title = incident title on the status page. First comment = initial description.

---

## Build & Deploy Steps

Once the repo is created and configured:

```bash
# 1. The repo is already initialized with .upptimerc.yml
# 2. Enable GitHub Actions in repo settings
# 3. Enable GitHub Pages (source: gh-pages branch, deployed by Actions)
# 4. Set repo secrets (GH_PAT, WINGS_URL)
# 5. Set custom domain in Pages settings
# 6. Add Cloudflare CNAME record
# 7. Upptime's template workflows handle everything else automatically
```

To use Upptime's template workflows, the repo should be created from the `upptime/upptime` template, then `.upptimerc.yml` is replaced with ours.

---

## Security Notes

- The status page is fully public — no auth gating
- No sensitive data in `.upptimerc.yml` — Wings URL and webhooks use `$SECRET_NAME` syntax referencing repo secrets
- The `GH_PAT` token needs only `repo` scope on this specific repo
- Response time data is public (this is expected for a status page)
- Do NOT monitor internal-only endpoints (admin panel, Gitea, Proxmox) — only public-facing services

---

## File Structure

```
mgh-status/
├── .upptimerc.yml          # Upptime configuration (monitors, branding, schedule)
├── BUILD_INSTRUCTIONS.md   # This file — full context for building the themed page
├── LICENSE                  # MIT (matches Upptime)
├── .github/
│   └── workflows/          # Auto-created by Upptime template
│       ├── graphs.yml
│       ├── response-time.yml
│       ├── setup.yml
│       ├── site.yml
│       ├── summary.yml
│       ├── update.yml
│       └── uptime.yml
├── assets/
│   └── fonts/              # Self-hosted woff2 files
│       ├── SpaceGrotesk-*.woff2
│       └── JetBrainsMono-*.woff2
└── themes/                 # Custom CSS/JS overrides (if using Option A)
    └── mg-status.css
```
