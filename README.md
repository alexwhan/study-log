# Study Log

A single-page daily study log. Notes are written as markdown to a **separate private GitHub repo** via the GitHub API. This repo contains nothing personal — all configuration (profile name, repo, access token) lives in `localStorage` on each device.

## Architecture

- **This repo (public)** — static app, served by GitHub Pages. `index.html` is the whole app.
- **Notes repo (private)** — e.g. `alexwhan/study-log-notes`. Holds one folder per profile containing `timetable.json` and one markdown file per semester (`2026-S2.md`).
- **Credential** — a fine-grained personal access token scoped to *only* the notes repo, entered once on the device's setup screen.

## Deploying the app

1. Push this repo to GitHub (public is fine — it contains no secrets).
2. Repo → Settings → Pages → Source: *Deploy from a branch* → `main`, `/ (root)`.
3. The app is live at `https://<user>.github.io/study-log-app/` a minute later.

## Setting up the notes repo

1. Create a **private** repo, e.g. `study-log-notes`.
2. Add `<profile>/timetable.json` (see the notes repo README for the format). The profile name is lowercase, e.g. `hugo/timetable.json`.
3. No semester file needed — the app creates `<profile>/2026-S2.md` on first save.

## Creating the access token

GitHub → Settings → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → *Generate new token*:

- **Resource owner:** your account
- **Repository access:** *Only select repositories* → the notes repo
- **Permissions:** Repository permissions → **Contents: Read and write**. Nothing else.
- **Expiration:** as long as you're comfortable with. Put the expiry date in your calendar — when it lapses the app shows a "token expired" banner rather than failing silently.

Copy the token (`github_pat_…`); it's shown once.

## Setting up a device

Open the app URL → the setup screen asks for **name** (profile), **notes repo** (`owner/repo`), and the **token**. Saving validates all three by fetching the timetable, then stores the config in `localStorage`. On Android, use Chrome's *Add to Home screen* to install it as an app.

## Development sandbox

To hack on the app without touching real notes: create a second private repo (e.g. `study-log-notes-dev`) with its own scoped token, and configure your own browser with that repo + a different profile. Your credential structurally cannot reach the real notes repo.

## Data format

```markdown
## 2026-07-21

### Physics

- Snell's law — **high**
- Total internal reflection — **low**
```

`## ` headings are ISO dates, `### ` headings are subjects, each bullet is a concept with a `**low|med|high**` self-rating. The tracking grid is derived entirely by parsing this file against the timetable — there is no separate state.
