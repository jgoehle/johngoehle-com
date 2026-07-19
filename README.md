# johngoehle.com

Static website for **The Tug** — a memoir by John J. Goehle.

Plain HTML/CSS/images. **No build step.** Netlify publishes the repo root as-is.

## Deploy (auto)
Connected to Netlify. Every push to `main` re-publishes the site automatically.

- Publish directory: `.` (repo root)
- Build command: *(none)*

## Structure
- `index.html` — home
- `the-tug.html` — the book (description + excerpt)
- `about.html` — about the author
- `contact.html` / `thank-you.html` — contact form (Netlify Forms)
- `404.html` — not found
- `css/styles.css` — styling
- `assets/` — cover, author photo, favicon
- `netlify.toml`, `_redirects`, `robots.txt`, `sitemap.xml` — config & SEO

See `DEPLOYMENT-INSTRUCTIONS.md` for full setup notes.
