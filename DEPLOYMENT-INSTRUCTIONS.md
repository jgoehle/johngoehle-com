# Deploying johngoehle.com to Netlify + Pointing GoDaddy at It

This guide walks you through publishing this website on **Netlify** and then moving the **johngoehle.com** domain from GoDaddy's Managed WordPress to your new Netlify site.

Everything in the `netlify-site` folder **is** the website. There is no build step — it's plain HTML, CSS, and images, so it works the moment Netlify receives the files.

> **Plan your timing.** Your current site is GoDaddy Managed WordPress. Get the new Netlify site fully working on its temporary Netlify address **first**, then change DNS. DNS changes can take anywhere from a few minutes to 48 hours to fully propagate, during which some visitors may still land on the old WordPress site. **Don't cancel or delete the GoDaddy WordPress plan until the new site is confirmed live.**

---

## What's in this folder

```
netlify-site/
├── index.html              ← Home page
├── the-tug.html            ← The book page (description + excerpt)
├── about.html              ← About John Goehle
├── contact.html            ← Contact form (uses Netlify Forms)
├── thank-you.html          ← Shown after the contact form is submitted
├── 404.html                ← Friendly "page not found"
├── css/styles.css          ← All site styling
├── assets/                 ← Book cover, author photo, favicon
├── netlify.toml            ← Netlify settings (headers, publish dir)
├── _redirects              ← Short-URL redirects (/buy, /book, etc.)
├── robots.txt              ← Search-engine instructions
└── sitemap.xml             ← Sitemap for search engines
```

> **One thing to update before you go fully live:** the "Buy on Amazon" buttons currently point to an Amazon **search** for the book's ISBN (`9798184505862`), which finds the book reliably. Once the book has its own Amazon product page, send me the direct URL (looks like `https://www.amazon.com/dp/XXXXXXXXXX`) and I'll swap it everywhere in about a minute. You can also do it yourself — see **Updating the Amazon link** at the end.

---

## PART 1 — Put the site on Netlify

You only need **one** of the two methods below. **Method A (drag-and-drop) is the fastest** and is recommended to start. Method B (Git) is better if you want automatic re-deploys whenever files change.

### Method A — Drag-and-drop (simplest, recommended)

1. Go to **https://www.netlify.com** and click **Sign up** (free). You can sign up with email, GitHub, or Google.
2. Once logged in, you'll land on the **Sites** dashboard.
3. Find the **"Want to deploy a new site without connecting to Git? Drag and drop your site output folder here"** drop zone (on newer dashboards, click **Add new site → Deploy manually**).
4. In your file browser, open the **`netlify-site`** folder. **Drag the folder itself** (or select all the files *inside* it) onto the Netlify drop zone.
   - ⚠️ Drag the **contents** so that `index.html` ends up at the top level of the deploy. If after deploying the homepage 404s, you accidentally nested an extra folder — redeploy with the files at the root.
5. Netlify uploads the files and, within a few seconds, gives you a live site at a temporary address like **`https://shiny-otter-123abc.netlify.app`**.
6. Click that link and **test everything**: the homepage, the Tug page, About, Contact, the menu on mobile, and the book cover image. This temporary URL is your safe staging ground before any DNS changes.

To update the site later with this method, just drag the folder onto the site's **Deploys** tab again.

### Method B — Git-based (auto-deploys on every change)

1. Put the contents of `netlify-site` into a GitHub repository (e.g., `johngoehle-com`).
2. In Netlify: **Add new site → Import an existing project → GitHub**, and choose that repo.
3. **Build settings:** leave **Build command** blank and set **Publish directory** to `.` (a period). Click **Deploy**.
4. From now on, every push to the repo's main branch re-publishes the site automatically.

---

## PART 2 — Rename your site & turn on the contact form

### Give the site a friendlier name (optional)
1. In Netlify: **Site configuration → General → Site details → Change site name**.
2. Pick something like `johngoehle` so the temporary URL becomes `https://johngoehle.netlify.app`. (This is just cosmetic; your real domain comes in Part 3.)

### The contact form works automatically
The Contact page uses **Netlify Forms** — no setup or third-party service needed. When someone submits the form:
- Netlify captures it and shows it under **Forms** in your site dashboard.
- To get an **email** every time someone writes: **Site configuration → Forms → Form notifications → Add notification → Email notification**, and enter `jgoehle@gmail.com`.

> Netlify's free plan includes 100 form submissions per month, which is plenty for a book site. The form already includes a hidden honeypot field to cut down on spam.

---

## PART 3 — Point johngoehle.com (at GoDaddy) to the new Netlify site

There are two ways to connect your domain. **Option 1 (recommended)** lets Netlify manage DNS and gives you the smoothest setup and automatic HTTPS. **Option 2** keeps DNS at GoDaddy and just edits two records — choose this if you have other things (like email) tied to GoDaddy DNS and don't want to move them.

> 📧 **Before you start:** if your email runs through this domain (e.g., GoDaddy/Microsoft 365 email at `@johngoehle.com`), use **Option 2** so you don't disturb your mail (MX) records. If the domain is only used for the website, **Option 1** is cleaner.

### First, add your domain in Netlify (both options need this)
1. In Netlify: **Site configuration → Domain management → Add a domain** (or **Domains → Add custom domain**).
2. Type **`johngoehle.com`** and click **Verify → Add domain**.
3. Netlify will also offer to add **`www.johngoehle.com`** — add it too. Netlify automatically redirects one to the other.

Netlify will now show you exactly which DNS records you need. Use whichever option below matches your choice.

---

### Option 1 — Let Netlify run DNS (recommended)

This moves DNS hosting to Netlify. Best for a website-only domain.

1. In Netlify's domain panel, click **Set up Netlify DNS** for `johngoehle.com` and follow the prompts. Netlify gives you **4 nameservers**, e.g.:
   ```
   dns1.p01.nsone.net
   dns2.p01.nsone.net
   dns3.p01.nsone.net
   dns4.p01.nsone.net
   ```
   (Yours may differ — copy the exact ones Netlify shows you.)
2. **Free up the domain from Managed WordPress first.** In GoDaddy, a Managed WordPress site can "own" the domain's DNS. Go to **GoDaddy → My Products → Managed WordPress**, and either:
   - In the WordPress product, look for a **domain / DNS** setting and detach it, **or**
   - Make sure you can edit nameservers in the next step (if the nameserver option is greyed out, that's the Managed WordPress lock — contact GoDaddy support and ask them to "release the domain so I can change nameservers to an external host").
3. In GoDaddy: **My Products → Domains → johngoehle.com → Domain Settings → Nameservers → Change → I'll use my own nameservers**.
4. **Delete** GoDaddy's default nameservers and **enter the 4 Netlify nameservers** from step 1. Save.
5. Back in Netlify, it will verify the nameservers (can take 15 minutes to a few hours). Once verified, Netlify **auto-provisions a free HTTPS certificate** — no extra steps.

✅ Done. When propagation finishes, `https://johngoehle.com` shows the new site.

---

### Option 2 — Keep DNS at GoDaddy, edit two records

Choose this if you want to leave email and other DNS records untouched at GoDaddy.

1. In Netlify's domain panel, when you add the domain **without** Netlify DNS, it shows you the target values. You'll set:
   - An **A record** for the apex (`@`) pointing to Netlify's load balancer IP: **`75.2.60.5`**
   - A **CNAME record** for `www` pointing to your Netlify subdomain: **`<your-site-name>.netlify.app`**
   > Always use the exact values Netlify displays for your site — the IP above is Netlify's standard apex IP, but confirm it in your dashboard.
2. **Detach Managed WordPress from the domain first.** GoDaddy Managed WordPress often forces the A record to the WordPress server and may lock DNS editing. Go to **My Products → Managed WordPress** and remove/detach the domain from the WordPress site (or call GoDaddy support and ask them to "point johngoehle.com away from Managed WordPress so I can edit the A and CNAME records"). Until this is done, your edits may be overridden or greyed out.
3. In GoDaddy: **My Products → Domains → johngoehle.com → DNS / Manage DNS**.
4. **Edit the A record:**
   - Find the existing **A record** with name **`@`**. Click the pencil to edit.
   - Change **Value** to **`75.2.60.5`** (or the IP Netlify shows). Set TTL to **600 seconds** / 10 minutes so changes propagate fast. Save.
   - If there are several `@` A records pointing at the old WordPress, delete the extras so only the Netlify one remains.
5. **Edit (or add) the www CNAME record:**
   - Find the **CNAME** with name **`www`**. Set **Value** to **`<your-site-name>.netlify.app`** (your real Netlify subdomain). Save.
   - If no `www` CNAME exists, **Add → CNAME → Name: `www` → Value: `<your-site-name>.netlify.app`**.
6. **Leave your MX (email) records and any other records alone.**
7. Back in Netlify, click **Verify DNS configuration**. Once it detects the records, go to **Domain management → HTTPS → Verify / Provision certificate** to turn on free HTTPS (Let's Encrypt). This can take a little while after DNS resolves.

✅ Done. When propagation finishes, `https://johngoehle.com` shows the new site and email stays put.

---

## PART 4 — Final checks (do these the same day)

1. Visit **`https://johngoehle.com`** and **`https://www.johngoehle.com`** — both should load the new site over HTTPS (padlock in the address bar).
2. Confirm the padlock/HTTPS is green and there's no "not secure" warning. If HTTPS isn't active yet, wait for DNS to finish, then in Netlify do **Domain management → HTTPS → Renew/Provision certificate**.
3. In Netlify, set the **primary domain** (apex `johngoehle.com` is a good choice) under **Domain management** so the other version redirects to it.
4. Submit a contact-form test message and confirm it appears under **Forms** (and that you got the email notification).
5. Check the site on your phone — tap the ☰ menu, open each page, confirm the cover image loads.
6. Only after all of the above looks good for a day or two should you consider **canceling the GoDaddy Managed WordPress plan** (so you're not paying for a site you no longer use). Keep the **domain registration** itself active — that's separate from the WordPress hosting.

---

## Tips & troubleshooting

- **"My homepage shows a 404 on Netlify."** The files were nested one folder too deep. Redeploy so that `index.html` sits at the top level of what you drag in.
- **"Nameserver / DNS fields are greyed out in GoDaddy."** That's the Managed WordPress lock. Detach the domain from the WordPress product, or call GoDaddy support and ask them to release it to an external host.
- **"It still shows the old WordPress site."** DNS can take up to 48 hours. Check progress at **https://dnschecker.org** (enter `johngoehle.com`, look for the new value). Clearing your browser cache or trying a phone on cellular data helps confirm.
- **"I want to update text or the cover later."** Edit the files in this folder and re-deploy (drag the folder onto the **Deploys** tab, or push to Git if you used Method B).

### Updating the Amazon link
When the book gets its own Amazon product page, replace the search URL with the direct one in these files: `index.html`, `the-tug.html`, `about.html`, `contact.html`, and `_redirects`. The current value to find-and-replace is:
```
https://www.amazon.com/s?k=9798184505862
```
Replace it with your direct product link (e.g., `https://www.amazon.com/dp/XXXXXXXXXX`). Or just send me the link and I'll do it.

---

*Questions while you're setting this up? Note where you get stuck (which Part/step) and I can walk you through it.*
