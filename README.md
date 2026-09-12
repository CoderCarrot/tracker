# USPS Package Dashboard (GitHub Pages, synced)

A package tracker that syncs across every device via a small JSON file in
this repo, using GitHub itself as the "backend." No server, no database,
no third party involved besides GitHub.

## How it works

- `index.html` is the whole app -- a static page with no build step.
- `data/packages.json` holds your package list.
- When you add/edit/remove a package in the browser, the page commits the
  updated `data/packages.json` straight to this repo using the GitHub REST
  API, authenticated with a personal access token you provide.
- Every device that has the token entered reads/writes the same file, so
  your list stays in sync everywhere.

**Important privacy note:** GitHub Pages on the free tier requires a
*public* repository. That means `data/packages.json` -- your tracking
numbers, nicknames, and notes -- is visible to anyone who finds this repo,
and every edit shows up in the commit history. Don't put anything sensitive
in the nickname/notes fields. If you have a paid GitHub plan that supports
Pages on private repos, you can use a private repo instead and skip this
concern entirely.

## One-time setup

1. **Create a public GitHub repository** (e.g. `package-dashboard`).
2. **Add the two files from this folder** to the repo root: `index.html`
   and `data/packages.json`.
3. **Enable GitHub Pages**: repo Settings → Pages → Source: "Deploy from a
   branch" → Branch: `main`, folder `/ (root)` → Save. GitHub will give you
   a URL like `https://yourusername.github.io/package-dashboard/`.
4. **Create a personal access token** scoped to just this repo:
   - GitHub → Settings (your account, not the repo) → Developer settings →
     Personal access tokens → Fine-grained tokens → Generate new token.
   - Set **Repository access** to "Only select repositories" → choose this
     repo.
   - Under **Permissions → Repository permissions**, set **Contents** to
     "Read and write." Leave everything else as "No access."
   - Set an expiration (a year is fine -- you'll just need to generate a
     new one and re-paste it into the app when it expires).
   - Generate the token and copy it (you won't be able to see it again).
5. **Open your GitHub Pages URL.** On first load it'll ask for:
   - GitHub username (the repo owner)
   - Repository name
   - Branch (`main` by default)
   - Data file path (`data/packages.json` by default)
   - The personal access token you just created
6. Do this same step 5 on any other device/browser you want the list to
   show up on, using the *same* token (or a separate fine-grained token
   scoped to the same repo -- either works).

After that, adding, updating, or removing a package on any device commits
straight to the repo, and every other device picks up the change next time
it loads the page (there's a "Settings" button in the header if you ever
need to change the repo/token, and a small sync indicator that shows
whether your last edit saved successfully).

## Limitations worth knowing

- **This is a personal tool, not built for concurrent heavy use.** If you
  edit from two devices at nearly the same instant, one save can conflict
  with the other; the app retries once automatically, but if it still
  fails you'll see a "sync conflict" message asking you to refresh and
  redo the edit.
- Typing in the notes field saves shortly after you stop typing (not on
  every keystroke), to avoid spamming your commit history.
- GitHub API rate limits for an authenticated token are generous (5,000
  requests/hour), so normal personal use won't come close to hitting them.
- Since it's committing real files, your repo's commit history will fill
  up with "Update packages via dashboard" commits over time. Harmless, but
  worth knowing if you care about a tidy git history.
