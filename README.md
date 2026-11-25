# Social Media Experts: new homepage, handover notes

New homepage for **www.smeonline.co.in**. It is a single, self-contained page: plain HTML, CSS and vanilla JavaScript, with no build step and no dependencies apart from Google Fonts and the existing Google Tag Manager container.

## What's in this folder

```
index.html            The homepage (all CSS and JS are inline)
_redirects            Cloudflare Pages redirects: old /about-us, /contact and /blogs/* pages go to the homepage
robots.txt            Allows indexing (AI search crawlers named explicitly), points to the sitemap
llms.txt              Plain-language fact sheet for AI assistants (ChatGPT, Claude, Perplexity, Gemini)
sitemap.xml           The homepage
site.webmanifest      App name, icons, theme colour
assets/
  logo-blue.png       Header logo
  og-image.png        1200x630 social share preview (WhatsApp, Facebook, LinkedIn, X)
  favicon.ico, favicon-32.png, apple-touch-icon.png, icon-192.png, icon-512.png
  clients/*.webp      12 client logos for the scrolling strip
```

## Branches

- **`main`**: the live site. Cloudflare Pages deploys every push to `main` to www.smeonline.co.in.
- **`preview`**: the old internal preview copy (noindex, no tracking). Not deployed anywhere important.

## Hosting

Plain static files on **Cloudflare Pages** (free plan, commercial use allowed). No build step:
- Framework preset: **None**
- Build command: *(leave empty)*
- Build output directory: **`/`**

Custom domains `www.smeonline.co.in` and `smeonline.co.in` are attached in the Pages project. `smeonline.co.in` should redirect to `www.smeonline.co.in`, which is the canonical URL in the meta tags.

**Email lives on Zoho Mail.** When changing DNS, never touch the MX, SPF (TXT) or DKIM records, or company email stops working.

### What happened to the old site's pages

The previous Next.js site had four blog posts and an `/about-us` page. They were dated (2024 SEO advice) and nobody writes new posts, so they were not moved. `_redirects` sends all of those old addresses to the homepage with a 301, so old links and search results still land somewhere useful.

Google Tag Manager `GTM-5S6TNWQQ` is included in `index.html` (head script plus body `noscript`). Don't add it twice.

## How the Brand Scan form works

There is no backend. On submit, the form:
1. checks name, phone and business name
2. builds a WhatsApp message with all the answers
3. shows an **"Open WhatsApp with my details"** button linking to `wa.me/919998065655` (Aparna)

The hero's "Chat on WhatsApp" button also goes to that number. To change the number, edit `var WA = "919998065655"` in the script and the hero link's `href`.

**Tracking events (for GTM).** The page pushes these to `dataLayer`:

| Event | When | Extra fields |
|---|---|---|
| `whatsapp_click` | Any WhatsApp link is clicked | `link_location`: `hero` or `brand_scan_form` |
| `brand_scan_form_submit` | Form passes validation | `services`, `budget` |
| `phone_click` | A phone number is tapped | `contact` (the number) |
| `email_click` | An email address is clicked | `contact` (the address) |

To use them, create **Custom Event** triggers in GTM. Then fire a GA4 event and a Meta Pixel `Lead` from them, so campaigns can optimise for real enquiries.

**Recommended next step: send form entries to Zoho CRM as well.** SME already uses Zoho CRM. Create a webform under **Setup > Channels > Webforms** on the **Leads** module, with fields Name, Phone, Company, Website, Services and Budget, and a hidden field `Lead Source = Website`. Then post the form to it on submit and keep the WhatsApp step as the instant follow-up. Zoho's guide: https://help.zoho.com/portal/en/kb/crm/generate-leads/web-forms/articles/set-up-web-forms

## Fonts

- **Headings:** Fraunces (a serif), weight 600. The hero's rotating word is in Fraunces italic.
- **Body text:** Instrument Sans.
- Both load from Google Fonts in the `<link>` in the head. To change the heading font, edit `--display` in `:root`.

## Editing content

Everything is plain HTML in `index.html`, in page order:

`header` → `.hero` → `.clients` (logo strip) → `.band` → `#services` (bento boxes) → `#how` → `#industries` → `#faq` → `#contact` (form) → `footer`

- **Industries:** the `<li class="ind">` tiles in `#industries`. Only name a client under an industry if they really are in it.

- **Services:** the `<article class="cell …">` blocks in `#bento`. Each box's position is set by `grid-template-areas` in the CSS.
- **FAQ:** native `<details>/<summary>` pairs, so they work without JavaScript.
- **Client logos:** each logo appears in **two** `<li class="logo-tile">` lists. The second, `aria-hidden` copy makes the scroll loop seamless, so add or remove a logo in both. Logos are about 220px tall WebP files on white tiles.
- **Hero rotating word:** the `words` array in the script.

## The chameleon (services section)

A pixel-art chameleon peeks up from behind the service box nearest the mouse cursor, follows the cursor with its eyes, then changes colour to match the box. On touch devices it pops up when a box is tapped.

- The sprite is an inline SVG built from 1×1 `<rect>`s: the `MONKEY` string in the script. The variable name is historical; it's a chameleon.
- Body colour comes from the `--cb` CSS variable. Each box sets its own blend colour with `--cham`.
- The colour transition uses `@property`. Browsers without it simply switch colour instantly.
- `REACH` (150px) is how close the cursor must be before it appears.
- It is decorative (`aria-hidden`), ignores clicks, and respects reduced-motion settings.

## Local SEO and AI search

The homepage tells Google and AI assistants (ChatGPT, Claude, Perplexity, Gemini) the same facts in four places. **Keep them identical** when anything changes:

1. **Structured data** (`<script type="application/ld+json">` in the head): one connected graph with the business, its 5 services, Aparna and Vidhu, the website, the page and the FAQ.
2. **Visible page:** hero line, `#industries`, contact details and the footer line.
3. **`llms.txt`:** the plain-text fact sheet.
4. **Google Business Profile:** name, phone, category, service areas.

**If you edit an FAQ, edit the matching `FAQPage` entry in the structured data as well.** The question and answer text must match word for word.

**To finish (needs info we don't have yet):**
- [ ] Add the Google Business Profile link to `sameAs` in the structured data, and also to the footer. To get it: Google Maps → the listing → Share → copy the link.
- [ ] If SME has a LinkedIn company page, add it to `sameAs` and to `llms.txt`.
- [ ] Add `openingHours` to the structured data once the business hours are decided. Use the same hours as the Google Business Profile.
- [ ] If the team speaks Gujarati with clients, add `"Gujarati"` to `availableLanguage`.
- [ ] The Google Business Profile should be a **service-area business** with the address hidden, because there's no walk-in office. The site never shows a street address, so the two stay consistent.
- [ ] After going live, test the page in Google's Rich Results Test: https://search.google.com/test/rich-results
- [ ] Update `dateModified` in the structured data and `<lastmod>` in `sitemap.xml` whenever the homepage changes.

## Go-live checklist

- [ ] Old addresses (`/about-us`, `/blogs/old-seo-vs-new-seo`) redirect to the homepage
- [ ] Company email (Zoho) still sends and receives after the DNS change
- [ ] GTM Preview shows the container firing, plus `whatsapp_click` and `brand_scan_form_submit`
- [ ] Share preview checked in the Facebook Sharing Debugger (https://developers.facebook.com/tools/debug/) and the LinkedIn Post Inspector (https://www.linkedin.com/post-inspector/)
- [ ] Favicon shows in the browser tab and on an iPhone home screen
- [ ] The WhatsApp button opens a chat on a phone
- [ ] `sitemap.xml` submitted in Google Search Console, and the site also added to Bing Webmaster Tools (ChatGPT and Copilot search use Bing's index)
- [ ] `https://www.smeonline.co.in/llms.txt` and `/robots.txt` load
- [ ] A Privacy Policy page is added and linked in the footer. The form collects names and phone numbers, and ad platforms expect one. The old footer had placeholder links for this.

## Already tested before handover

- The site uses one light theme (eggshell #F6F3EC with brand blue #0000FF) and stays light even on phones set to dark mode
- No horizontal scrolling at 390px, 820px or 1280px
- Accessibility check (axe-core, WCAG 2 A/AA + best practice): 0 issues
- No broken images or links, and no leftover placeholders. One `<h1>`, and headings in order.
- Form validation, WhatsApp message and the tracking event all checked
- Services, FAQ and form options still show with JavaScript turned off

## Contacts

- Aparna Shastry, Founder & Managing Director: +91 99980 65655, aparna@smeonline.co.in
- Vidhu Mishra, Performance Marketing: +91 84600 52036, vidhu@smeonline.co.in
