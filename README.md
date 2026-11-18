# apartment.ag-renovations.co.uk

Google Ads landing page for complete flat (apartment) refurbishments. Same family as the kitchen page (forest greens,
mint, brass, DM Sans) with its own centrepiece: a floor plan of a flat that changes through the stages of the work.

No build step: plain HTML and one CSS file. Edit, then push.

## What's in this repo

| Path | What it is |
|---|---|
| `index.html` | The page, including all scripts. The master copy: edit here only. |
| `css/apartment.css` | All styles. The top part is the kitchen page's theme; the apartment-only styles are at the end, under "apartment page (v1)". If you change this file, bump `css/apartment.css?v=` in `index.html` in the same commit. |
| `images/optimized/` | Andrius's photo, the award photo, commercial client photos, accreditation marks, partner logos. |
| `images/on-site/` | The on-site photo of one of our fitters (shown in "The works" step). |
| `images/placeholders/` | Marked placeholder images. Delete the folder once every placeholder is replaced. |
| `images/flats/` | Created by the photo script for real flat photos. |
| `images/og-image.jpg` | The preview image shown when the link is shared (1200 x 630). |
| `optimize_flat_photos.py` | Converts raw photos to AVIF and WebP at four sizes, removes all metadata including GPS, and writes a ready gallery tile. |
| `make_staging.py` | Writes the staging copy next to this folder (tracking off, noindex, form sends nothing). |
| `CNAME`, `robots.txt` | Production only. |

## How the page works

- **Hero:** three tap options ("Where are you with the flat?"). A tap records the answer and jumps straight to step 2
  of the enquiry form. The arched frame shows the finished floor plan (original line art, labelled as an
  illustration), so the hero needs no photo.
- **Enquiry form:** three steps (where they are with the flat, preferred start, contact details). It posts to the same
  Zapier hook as the bathroom and kitchen pages with `project_type=Apartment Refurbishment` and
  `lead_source=Google Ads Apartment Landing Page`. The two answers are sent as `project_stage` and `start_timeline`,
  and also written at the top of `message`, so they reach the CRM even if the Zap only maps the original fields.
- **Conversion:** the Ads form conversion (same action as the bathroom and kitchen pages) fires only on a confirmed
  2xx from Zapier. On failure the visitor sees a "call us" message and nothing is counted. Phone taps count as the
  phone conversion only on `apartment.ag-renovations.co.uk`.
- **GA4 events** for funnel analysis: `form_step`, `contract_toggle`, `plan_select` and `plan_tour` (the floor plan),
  `gallery_open`, `cta_click`.
- **The floor plan ("How a flat is rebuilt, stage by stage"):** seven stages (strip-out, new layout, electrics,
  plumbing and heating, plastering, kitchen and bathrooms, floors and carpentry, decorating and handover). The first
  time it is seen, the flat as it is draws itself; then the stages play like stories (pausable, with arrows, and
  tap-to-advance on phones). Walls coming down are dashed brass, new walls draw in, and the services fade once they
  are plastered over. Room labels are hidden on small phones; the caption carries the story there.
- **Before you start:** two cards, moving out and building approvals, with small line drawings that draw once.
- **The drawing line:** one fine line runs down the page margins as the visitor scrolls, as on the kitchen page.
  Give any new section an `id` and add it to the `sides` lists in the thread script.
- Everything stays still for visitors who have reduced motion switched on.

## Confirmed facts this page uses (Nov 2026)

- Direct quotes typically 10-15% lower than through a matching service: applies to full refurbishments (Andrius).
- Plastering, carpentry, flooring, kitchens, bathrooms and decorating by our own team. Electrical work by NICEIC
  certified electricians and gas by Gas Safe registered engineers, coordinated by us. The page does not say who does
  plumbing.
- Clients cannot live in the flat during the work. Clients usually change the layout, so the flat is stripped out at
  once.
- Approvals from the freeholder or managing agent are the client's responsibility; for an additional cost we can
  appoint an agent to handle them.
- No response-time promise and no standard duration anywhere on the page.

## Before the first launch

1. **Placeholders:** five left: three gallery tiles and two review cards. Each is marked `PLACEHOLDER` in
   `index.html` and outlined in red dashes on the page. `(Select-String -Path index.html -Pattern "PLACEHOLDER").Count`
   must return `0` before launch.
2. **Subdomain:** `apartment.ag-renovations.co.uk` is a working name until the Keyword Planner check ("flat" vs
   "apartment"). It appears in `CNAME`, the canonical and `og:` tags, the schema `image`, and the phone-conversion
   hostname check in the script.
3. **Review figures** checked live on the day: the page shows Google 5.0 from 60 and Trustpilot 4.7 from 48 (hero
   trust row and reviews section), the October catalogue figures.
4. **Photo rights:** every gallery photo is a real A&G flat that A&G has the right to use; every review photo has the
   reviewer's permission. Areas only on captions, never streets or postcodes.
5. **Wording written for this page, to confirm:** "doors, frames and skirting" (floors and carpentry stage), "Most
   London flats are leasehold" (before you start), and "Most of our full refurbishments include layout changes" (FAQ).
6. **Zap mapping:** confirm the CRM files `Apartment Refurbishment` as "Complete apartment or home refurbishment".
7. **Test the live form once before any campaign points here**, with "TEST" in the message, and confirm it reaches the
   CRM labelled as an apartment enquiry.

## Adding flat projects

The gallery shows one tile per project: the cover photo, the project name, a short line and the photo count. Tapping
a tile opens that project's photos only. Pick 5 to 9 photos per project with no near-duplicates.

1. Put the chosen photos in `photos-raw/<area>/` (git-ignored), e.g. `photos-raw/fulham/`, renamed so the cover photo
   sorts first. iPhone RAW (.DNG) and HEIC are fine.
2. `pip install pillow rawpy pillow-heif`, then `python optimize_flat_photos.py fulham "Flat in Fulham"`.
3. Paste the generated `project-fulham.html` inside `<div class="ptiles">` in the gallery in place of one placeholder
   tile (delete the placeholder's comment and `<button>` together), write the cover alt text and the short line, then
   deploy as usual.

## Reviews on the page

Review text is quoted word for word from Google. Where "…" appears, a passage was left out: staff names other than
Andrius, and any mention of the platform the client booked through. To add a review, copy a review card from the
kitchen page's `index.html` (the `<article class="rcard">` block, with its photos and Google link) in place of a
placeholder card, and change the name, text, photos and link.

## Every deploy (staging, then live)

1. Edit `index.html` (and `css/apartment.css` if needed) in this folder.
2. `python make_staging.py`, then commit and push the staging repo. Review at
   `https://a-g-renovations-ltd.github.io/apartment-landing-page-staging/` on desktop and phone.
3. In this folder: `git remote -v` must show `A-G-Renovations-Ltd/apartment-landing-page`.
4. `git status`, then `git log --oneline -3` and note the top hash as your rollback point.
5. Checks before pushing: `hooks.zapier.com` appears exactly once; `AG_STAGING = false`; no `noindex`; `CNAME` reads
   the chosen subdomain; `robots.txt` says `Allow: /`; PLACEHOLDER count is 0 (for launch).
6. Commit, push, wait about 3 minutes, check in a private window.

## Domain safety

Do not verify `ag-renovations.co.uk` at organisation level while any A&G page lives outside the A-G-Renovations-Ltd
organisation: it takes those pages offline. Leave GitHub Pages switched off on the production repo until launch day,
because the `CNAME` file points Pages at the subdomain before its DNS record exists. Launch-day DNS at one.com is one
new record only (`apartment` CNAME to `a-g-renovations-ltd.github.io`); no existing record changes.
