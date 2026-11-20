---
name: add-clip
description: Add one of Yasmeen's published stories to the Clips section of her site from a link. Opens the link, copies the headline exactly as published plus the outlet and date, adds it newest first, and pushes to main so it goes live. Use when she runs /add-clip with a story URL or asks to add a clip.
argument-hint: <link to the story>
---

# Add a clip

The link to add is: $ARGUMENTS

If no link was given, ask for one and stop.

Follow the project rules in `CLAUDE.md` throughout, especially: use her words and published text exactly, never add facts, keep the site style.

## 1. Open the link

Fetch the page (use WebFetch, or the web-fetch agent if WebFetch isn't available; `curl -sL` is fine for reading the raw HTML). Then find these three things **from the page itself**:

- **Headline, exactly as published.** Use the story's own headline: the main `<h1>` on the article, or the `headline` field in the page's JSON-LD (`NewsArticle`/`Article`). Do **not** use the browser `<title>` tag if it has the site name tacked on (for example "… | The Washington Sun"). Copy the headline character for character: same words, capitals, punctuation and curly quotes. Never rewrite, shorten, fix or summarize it.
- **Outlet**, as the publication names itself: the JSON-LD `publisher.name`, or `og:site_name`, or the name on the masthead.
- **Publication date**: JSON-LD `datePublished`, or `article:published_time`, or the date shown on the article. Use the publication date, not an "updated" date.

**Stop and ask her instead of guessing** if any of these happen:
- The link won't open (error, paywall, login wall, blocked, or only a cookie/consent page comes back).
- Any of the three items can't be found on the page.
- Different places on the page give different headlines (for example the `<h1>` and the JSON-LD headline differ) or different dates. Show her both and ask which one to use.
- Her name (Yasmeen Khan) doesn't appear in the byline. Ask her to confirm it's her story before adding it.

When asking, request exactly: the headline as published, the outlet, and the date. When she answers, use her words exactly.

## 2. Check for duplicates

If a clip with the same link is already in `index.html`, don't add it again. Tell her it's already there.

## 3. Add it to the Clips section

Clips live in `index.html` inside `<section id="clips">`. Use this markup:

```html
<ol class="clips">
  <li class="clip" data-date="YYYY-MM-DD">
    <h3 class="clip-title"><a href="LINK">HEADLINE</a></h3>
    <p class="clip-meta">OUTLET · Month D, YYYY</p>
  </li>
</ol>
```

- If the section has no `<ol class="clips">` yet (it holds only the `<!-- Clips to be added later. -->` comment), replace that comment with the list, and remove `card-empty` from the section's `class`.
- **Newest first.** Insert the new `<li>` so the list stays in date order by `data-date`. A new story usually goes at the very top; if it's older than clips already listed, put it in its date position.
- `data-date` is the ISO date (for example `2026-11-18`). The visible date is written out in full, like `November 18, 2026`, the same format for every clip.
- `LINK` is the link she gave, unchanged.
- Escape `&`, `<`, `>` and `"` in the headline and outlet as HTML entities so they show exactly as published. Change nothing else.
- Don't touch any other part of the page or its styles.

## 4. Check it

Open the page in the pre-installed Chromium/Playwright at desktop width (1400px) and phone width (390px). Confirm that the clip shows correctly, that the headline matches what was published, and that there's no sideways scrolling.

## 5. Publish

Commit straight to `main` with a message like `Add clip: <headline>`, and push to `origin main`. If the push fails, say so plainly and don't claim it's live.

## 6. Tell her what you added

In plain words, give her:
- the headline exactly as added, the outlet and the date
- where it sits in the list (for example "at the top")
- that the live site can take a minute or two to update
