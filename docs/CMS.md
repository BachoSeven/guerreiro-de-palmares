# Editing the site (CMS)

The site is a static Astro build deployed by Netlify from the `main` branch of
`BachoSeven/guerreiro-de-palmares`. Content lives in Markdown/JSON files in the
repository.

The old Decap CMS `/admin` setup used Netlify Identity + Git Gateway. Netlify
has deprecated Git Gateway, which is why the login now fails with:

> Gateway Error: Please ask your site administrator to reissue the Git Gateway token.

Instead of reissuing a deprecated token, this repository now uses
[Pages CMS](https://pagescms.org/) — a free, open-source, Git-based CMS.

## How it works

- Editors sign in at <https://app.pagescms.org>.
- The CMS edits the same files Astro already reads:
  - `src/content/blog/*.md` — blog posts
  - `src/content/page/*.md` — static pages
  - `src/content/author/*.json` — authors
  - `src/assets/**` — uploaded images
- Every save is a normal Git commit on `main`; Netlify rebuilds and deploys.
- Editors do **not** need a GitHub account. They can be invited by email from
  the Pages CMS dashboard.

The CMS configuration is [`.pages.yml`](../.pages.yml) at the repository root.

## One-time setup (site owner)

1. Push the `.pages.yml` file and the other CMS changes to `main`.
2. Go to <https://app.pagescms.org> and sign in with GitHub.
3. Install the Pages CMS GitHub App on the `BachoSeven/guerreiro-de-palmares`
   repository only (not "all repositories").
4. Open the repository in Pages CMS. It will read `.pages.yml` automatically.
5. In the repository settings, invite editors by email. They can choose a
   password or sign in with Google/Microsoft.
6. Optional: in Netlify, delete the old Identity instance and the Git Gateway
   service, since Pages CMS no longer uses them.

`/admin` now redirects to the Pages CMS editor for this repository.

## What can be edited

- **Blog** — create/edit/delete posts, cover image, tag, author and date.
- **Pagine** — edit `gruppo.md` and `capoeira.md` with a visual editor.
- **Galleria** — edit the cover image and the image list (add, remove, reorder).
- **Canzoni** — edit the songbook. This page contains `<audio>` and
  `<details>` HTML, so it uses a Markdown/code editor to preserve it.
- **Autori** — edit the author name, bio and photo.

New post filenames are generated from the title, e.g.
`V Encontro Anual` → `v-encontro-anual.md`.

## Adding a new tag or author

The blog form uses selects to avoid typos:

- Tags are listed under `content → blog → fields → tag` in `.pages.yml`.
- Authors are listed under `content → blog → fields → author` in `.pages.yml`.

Add a value there (and an author JSON file in `src/content/author/` if needed),
commit, and the option appears in the editor.

## Images

Uploads are stored in `src/assets/`. The CMS writes relative paths such as
`../../assets/photo.jpg`, which is what Astro's `image()` schema requires.
Do not change `media.input` / `media.output` in `.pages.yml` without updating
the content schema.

## Fallback: keep Decap CMS

If you prefer to keep the Decap CMS UI, the least painful replacement for
Netlify Identity + Git Gateway is [DecapBridge](https://decapbridge.com/)
(free tier: 3 sites, 10 collaborators; email invites, no GitHub accounts).

Create a site in DecapBridge, then replace the backend in a Decap
`config.yml` with the values it gives you:

```yaml
backend:
  name: git-gateway
  repo: BachoSeven/guerreiro-de-palmares
  branch: main
  identity_url: https://auth.decapbridge.com/sites/<site-id>
  gateway_url: https://gateway.decapbridge.com
```

This repository no longer ships that config because Pages CMS does not need
it. The old Decap config is still available in Git history if you want to
restore and adapt it.
