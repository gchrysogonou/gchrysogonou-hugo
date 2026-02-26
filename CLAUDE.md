# gchrysogonou.gr — Claude Instructions

**Live:** https://gchrysogonou.gr
**GitHub:** github.com/gchrysogonou/gchrysogonou-hugo
**CMS:** https://gchrysogonou.gr/admin/

## Stack

| Tool | Version | Σκοπός |
|------|---------|--------|
| Hugo | 0.155.3 extended | Static site generator |
| Stack theme | v4 (git submodule) | Card-style layout, sidebar |
| Vercel | Free plan | Hosting + auto-deploy on push |
| Sveltia CMS | Latest | Online editor (GitHub OAuth) |
| Obsidian | Latest | Primary content writing tool |

## Development

```bash
# Local preview
hugo server -D

# Deploy (auto-deploy via Vercel on push)
git add . && git commit -m "message" && git push
```

## Key Files

| Τι | Αρχείο |
|----|--------|
| Main config | `hugo.yaml` |
| Vercel config | `vercel.json` |
| Homepage layout | `layouts/home.html` |
| Homepage content | `content/_index.md` |
| Custom styles | `assets/scss/custom.scss` |
| Custom 404 | `layouts/404.html` ("Dead Air.") |
| Image helper | `layouts/_partials/helper/image.html` |
| JSON-LD schemas | `layouts/_partials/head/custom.html` |
| OG/Twitter override | `layouts/_partials/head/opengraph/provider/` |
| robots.txt template | `layouts/robots.txt` |
| CMS config | `static/admin/config.yml` |
| Obsidian template | `templates/new-blog-post.md` |
| OAuth | `api/auth.js`, `api/callback.js` |
| Greek i18n | `i18n/el.toml` |

## Content Structure

```
content/
├── _index.md              # Homepage (intro + favorite_posts frontmatter)
├── posts/                 # Blog posts — PAGE BUNDLES
│   └── my-post/
│       ├── index.md       # Post content
│       ├── cover.jpg      # Cover image (1200×630px, <300KB)
│       └── photo.jpg      # In-content images (paste in Obsidian)
└── page/                  # Stack pages
    ├── about/index.md
    ├── work/index.md
    ├── podcast/index.md
    ├── archives/index.md
    └── search/index.md
```

**Image guidelines:**
- Cover: 1200×630px, `image: "cover.jpg"` στο front matter
- In-content: 800-1200px width, <300KB
- Paste/drag in Obsidian → αποθηκεύεται αυτόματα στον ίδιο φάκελο

## New Post (Obsidian Workflow)

`Cmd+P` → "Templater: Create new note from template" → `new-blog-post`

Ρωτάει: Τίτλος → Category (dropdown) → Tags (multi-select) → Περιγραφή
Δημιουργεί: `content/posts/{slug}/index.md`

**Σημαντικό για slugs:** Το `hugo.yaml` χρησιμοποιεί `permalinks: posts: /posts/:slug/`. Το `:slug` κάνει fallback στο **title** (όχι folder name) αν δεν υπάρχει `slug:` στο frontmatter — δίνει Greek URLs. Κάθε post **πρέπει** να έχει `slug:` στο frontmatter (ήδη περιλαμβάνεται στο Obsidian template και στο Sveltia CMS).

## Categories

| Category | Posts |
|----------|-------|
| Μουσική | ~18 |
| Σκέψεις | ~17 |
| Podcast | ~12 |

Category badge color: `#EF820C` (πορτοκαλί) — ορίζεται στο `custom.scss`

## Custom Shortcodes

```markdown
{{< spotify type="playlist" id="PLAYLIST_ID" >}}
{{< spotify type="playlist" id="PLAYLIST_ID" height="352" >}}
```
Heights: `80` (mini) · `152` (compact, default) · `352` (full)

```markdown
{{% center %}}
Κεντραρισμένο κείμενο ή εικόνα
{{% /center %}}
```

```markdown
{{< columns >}}
Κείμενο αριστερά...

|||

![alt](photo.jpg)
{{< /columns >}}
```
Columns: 50/50 σε desktop, single column σε mobile.

## DNS — ΠΡΟΣΟΧΗ

DNS Zone βρίσκεται στο **Hostinger panel** (ΟΧΙ Papaki — nameservers δείχνουν Hostinger).

```
A     @    → 216.198.79.1        (Vercel)
CNAME www  → cname.vercel-dns.com
MX records → mx1/mx2.hostinger.com  ← ΜΗΝ ΑΓΓΙΞΕΙΣ
```

## SEO (τι τρέχει)

- Sitemap: auto-generated + submitted σε GSC ✅
- JSON-LD: `Person` (homepage) + `Article` + `BreadcrumbList` (posts)
- OG fallback image: `author.jpg` για posts χωρίς cover ✅
- Twitter Cards: `@gchrysogonou`
- robots.txt: Disallow `/admin/`, `/search/`
- RSS feeds: `/index.xml`, `/posts/index.xml`, `/podcast/index.xml`

## Sveltia CMS

OAuth: GitHub OAuth App → Vercel serverless (`api/auth.js` + `api/callback.js`)
Callback URL: `https://gchrysogonou.gr/api/callback`

Vercel env vars required: `OAUTH_GITHUB_CLIENT_ID`, `OAUTH_GITHUB_CLIENT_SECRET`, `SITE_URL`

## Pending

- [ ] `description:` frontmatter σε podcast episodes (τώρα χρησιμοποιεί `.Summary`)
- [ ] Branded OG image 1200×630px (αντί για `author.jpg`)
- [ ] Contact form
