# Nano Banana — Deploying to GitHub Pages
..
This repository is a static single-page app that uses the Puter JavaScript SDK (`puter.ai.txt2img`) to generate images. It works as a plain static site (no build step required), so GitHub Pages is a good deployment target.


## Quick summary

- The app expects an OAuth redirect target at `auth_callback.html` — this file exists in the project and will write a `putter_auth` localStorage marker and postMessage the opener when the provider redirects back.
- For OAuth redirects to work you must register the fully qualified HTTPS redirect URI with Puter (or the identity provider) — GitHub Pages provides HTTPS by default.

## Recommended GitHub Pages deployment (simple)

1. Create a GitHub repository (or use the existing one).
2. Commit and push this project to the repository (root should contain `index.html` and `auth_callback.html`).
3. In the repo Settings -> Pages, choose the branch (`main` or `master` or `popup-auth-issue`) and the root folder `/ (root)`. Save. GitHub will publish the site at `https://<your-username>.github.io/<repo-name>/`.

Note: GitHub Pages enforces HTTPS; use the Pages URL when registering redirect URIs.

## Register redirect URI with Puter / identity provider

In your Puter (or OAuth provider) dashboard, register the redirect URI exactly as:

`https://<your-username>.github.io/<repo-name>/auth_callback.html`

Replace `<your-username>` and `<repo-name>` with your GitHub username and repository name.

If you plan to use a branch other than the default or a custom domain, adjust the URL accordingly.

## Local testing (development)

- You can run a local static server for quick testing. From the repo root (on Windows with bash):

```bash
# using Python 3's simple server (works if python is in PATH)
python -m http.server 8000
# or, if you have node installed
npx serve -s . 8000
```

- NOTE: OAuth redirect URIs must be HTTPS. For local OAuth testing you have two options:
  - Use a tunneling service that provides HTTPS, e.g. ngrok:

```bash
# start a local server then expose it publicly with ngrok
python -m http.server 8000
ngrok http 8000
# ngrok will show a https://... forwarding URL — register that + /auth_callback.html as your redirect
```

- Or deploy to GitHub Pages temporarily and test against the published HTTPS URL.

## Mobile behavior and auth notes

- Mobile browsers sometimes open OAuth flows as a new tab with no `window.opener`. To accommodate that, `auth_callback.html` writes a `localStorage` key `putter_auth` and also `postMessage`s the opener when present.
- The main page (`index.html`) now listens for both `message` events and `storage` events and will reload once it detects the `putter_auth` marker — this allows the Puter SDK to pick up cookies/session state.

## Next steps you can ask me to do

- I can add a small `.github/workflows` Action to automatically deploy to GitHub Pages on push.
- I can add more troubleshooting steps for mobile (remote debugging via Chrome, Safari Web Inspector).
- I can extend `index.html` logging or create a tiny automated test harness.

---

If you'd like, I can create a GitHub Actions workflow now to auto-publish on push to `main` (or your chosen branch). Let me know which branch you want published and I will add it.
