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

`.claude/skills/` holds the [UI/UX Pro Max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
bundle (v2.13.0, MIT) — seven skills: `ui-ux-pro-max`, `ui-styling`, `design`,
`design-system`, `brand`, `banner-design`, `slides`. They are vendored, not a
dependency; there is still no package manager here. The search backend needs
Python 3:

```
python3 .claude/skills/ui-ux-pro-max/scripts/search.py "<query>" --domain style [--json]
```

Domains: `style`, `palette`, `font`, `ux`, `chart`, `stack`.

Treat the whole directory as **vendored upstream code**. Do not hand-edit it. To
upgrade, re-run the vendor's installer and take its files wholesale:

```
npx ui-ux-pro-max-cli init --ai claude --force
```

If an upgrade conflicts with the committed copy, the newer upstream release wins —
these files carry no local changes worth preserving. Compiled bytecode under
`scripts/__pycache__/` is deliberately untracked (`scripts/.gitignore`); do not
re-add it, or every session that runs the skill leaves a dirty tree.

**This install is project-scoped** — it reaches sessions working in this repo only.
To use these skills across all repos, install them at user level instead, from a
Claude Code session on your own machine:

```
/plugin marketplace add nextlevelbuilder/ui-ux-pro-max-skill
/plugin install ui-ux-pro-max@ui-ux-pro-max-skill
```

(Fallback if the plugin install fails: `cd ~ && npx ui-ux-pro-max-cli init --ai claude`.)
A project-scoped copy takes precedence over a user-level one, so once the global
install exists, delete `.claude/skills/` here — otherwise this repo stays pinned at
v2.13.0 while every other project tracks the latest.
