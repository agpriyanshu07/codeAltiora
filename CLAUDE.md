# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

**ZenMitra** — a mental-wellness website for an Indian audience ("Saathi" = companion).
It is a **static, multi-page HTML site**. There is no build step, no package manager,
no framework, no backend, and no test suite. Every page is a standalone `.html` file
with its CSS in a single `<style>` block and its JS in a single `<script>` block at the
bottom of `<body>`.

Repo name is `codeAltiora`; the product name in the UI is ZenMitra.

## Pages

| File | Purpose |
| --- | --- |
| `index.html` | Landing page: header/nav, hero, stats bar, features, emergency helplines |
| `music.html` | Music therapy — audio player cards, sleep timer, keyword search |
| `resources.html` | Video library — YouTube iframe player with a filterable thumbnail sidebar |
| `signin.html` | Sign-in form, client-side validation only |
| `signup.html` | Sign-up form (name/email/phone/age/DOB/password), client-side validation only |

`Zen Mitra final` is a stray empty file. `README.md` is a single line.

## Running it

Open a file directly, or serve the directory (needed for the `<audio>` elements and
iframes to behave consistently):

```
python3 -m http.server 8000
```

No install, lint, or test commands exist. Do not add a `package.json`, bundler, or
framework unless explicitly asked — the site's simplicity is deliberate.

## Deployment

The repo is connected to **Vercel** as a static site. There is no build command —
Vercel serves the HTML files as-is, which is why the no-build-step constraint above
matters in practice.

Several Vercel projects are connected at once, and the exact set has varied between
pushes; at least three distinct project IDs have been observed building this repo,
including two both named `code-altiora` (under different Vercel accounts) plus
`code-altiora-jf8n`. Each pull request therefore collects multiple preview
deployments, reported in a single bot comment that the Vercel app edits in place as
each goes Building → Ready. Treat the previews as interchangeable — they serve
identical content — and don't rely on any one project name being present. The
duplication looks like leftover connections rather than deliberate environments; if
you are tidying this up, do it in the Vercel dashboard, not in the repo.

## Conventions

- **Self-contained pages.** CSS and JS live inline in each page. There is no shared
  stylesheet or script; a change to the header or theme must be repeated per page.
- **Theming via CSS custom properties** on `:root` at the top of each `<style>` block.
  The palette is a light-green theme, but the variable names are *inconsistent across
  pages* — `index/music/resources` use `--primary-green` / `--secondary-green` /
  `--light-green-bg`, while `signin/signup` use `--primary-color` / `--secondary-color`.
  Match the file you are editing rather than unifying, unless asked to unify.
- **Fonts:** Poppins on `index`, `resources`, `signin`, `signup`; Roboto on `music`.
  Loaded from Google Fonts. Font Awesome 6 (cdnjs) supplies icons on the first three.
- **JS style:** plain ES6+, no modules, no dependencies. Handlers are wired either as
  inline `onclick="..."` attributes (`index.html`, `music.html`) or via
  `addEventListener` inside `DOMContentLoaded` (`resources.html`). User feedback is
  frequently `alert()`.
- **Comments** use banner blocks (`/* ===== SECTION ===== */`) to divide the style
  sheet; keep that rhythm when adding CSS.

## Things that are stubs, not bugs to "fix" silently

- **Auth is fake.** `signin.html` and `signup.html` validate client-side, `await` a
  2-second `setTimeout` to simulate a request, then `alert()` success. The real
  redirect is commented out (`// window.location.href = '/dashboard'`). There is no
  server, no session, no password hashing. Never present these forms as secure or
  wire them to a real credential store without being asked.
- **`music.html` references audio files that are not in the repo** —
  `music1.mp3`–`music3.mp3` and the solfeggio tracks `174hz.mp3`, `285hz.mp3`,
  `396hz.mp3`, `528hz.mp3`, `639hz.mp3`, `741hz.mp3`, `852hz.mp3`. Playback will 404
  locally. This is expected; the assets are supplied at deploy time.
- **`index.html` links to `Community.html`, which does not exist.**
- Several nav items point at external services rather than local pages: the AI Support
  link goes to a Chatbase bot, Book Session goes to a Google Form.
- `resources.html` "descriptions" come from a hardcoded `switch` on the video title
  (`updateDescription`), not from any API.

## Sensitive content

`index.html` carries an **Emergency Mental Health Helplines** section with real Indian
crisis numbers (KIRAN `1800-599-0019`, Vandrevala Foundation `9999-666-555`, NIMHANS).
Treat these as safety-critical: do not edit, reformat, or remove a number without an
explicit instruction, and never invent or "correct" a helpline number from memory.
The same care applies to any wellness claim or clinical-sounding copy on the site.

## Accessibility

Existing markup uses `aria-label`, `aria-expanded` (the nav toggle in `index.html`),
and `aria-labelledby` on sections. Preserve and extend these when touching markup;
keep new interactive elements keyboard-reachable.

## Design skills (UI/UX Pro Max)

The [UI/UX Pro Max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) bundle
(MIT) — `ui-ux-pro-max`, `ui-styling`, `design`, `design-system`, `brand`,
`banner-design`, `slides` — is installed at **account level**, not in this repo.
It syncs into every session automatically, so it is available here without
`.claude/skills/` existing. Do not commit a copy back into this repo: a
project-scoped copy silently overrides the account-level one, which is how this
project previously ended up pinned to an older release than everything else.

The search backend needs Python 3, and lives wherever the skill is synced:

```
python3 ~/.claude/skills/synced/ui-ux-pro-max/scripts/search.py "<query>" --domain style [--json]
```

Domains: `style`, `palette`, `font`, `ux`, `chart`, `stack`.

Upgrades happen at the account level (claude.ai → Settings → Capabilities →
Skills), not through this repo. Replacing an existing entry means deleting it
first — uploading over a skill of the same name does not overwrite it.
