# PRD: Aaron's Bees — Phase 3 Rich Results & Social Image

**Branch:** `feature/phase3-rich-results`
**Executor:** Claude Code
**Constraint:** Single-file site (`index.html`) + one new image asset (`og-image.png`). No layout changes.

---

## Context

Phase 1 added JSON-LD with `"image": ""` as a known placeholder. Phase 3 closes that gap and adds `aggregateRating` + `Review` objects — the two changes that unlock Google rich results (star ratings in SERPs) and social share previews.

---

## Task 1: Generate og-image.png

**What:** A simple branded 1200×630px PNG served from site root.  
**Why:** Satisfies both `og:image` (social cards) and JSON-LD `image` (rich results eligibility).  
**Design:** Honey background (`#F5A623`), dark bark text (`#2C1810`), business name, phone, tagline.  
**Constraint:** No photos. No external assets. Generated locally via Python/ImageMagick.

**Acceptance criteria:**
- [ ] File exists at `/aarons-bees/og-image.png`
- [ ] Dimensions: 1200×630px
- [ ] Readable at 600px preview (test in browser)
- [ ] File size < 100KB

---

## Task 2: Update JSON-LD — image, aggregateRating, Review objects

**What:** Three additions to the existing `<script type="application/ld+json">` block.

### 2a. image field
```json
"image": "https://aaronbeeremoval.com/og-image.png"
```

### 2b. aggregateRating
Source: Yelp listing shows 20+ reviews. Use conservative count of 20, rating 5.0.
```json
"aggregateRating": {
  "@type": "AggregateRating",
  "ratingValue": "5.0",
  "reviewCount": "20",
  "bestRating": "5",
  "worstRating": "1"
}
```

### 2c. Review objects (3 existing reviews from page)
```json
"review": [
  {
    "@type": "Review",
    "reviewRating": { "@type": "Rating", "ratingValue": "5", "bestRating": "5" },
    "author": { "@type": "Person", "name": "Verified Customer" },
    "reviewBody": "Same day service. Very professional. Removed a 100 year old hive within a couple of hours. Follow up excellent. Pricing competitive. This company made an uncomfortable situation very easy."
  },
  {
    "@type": "Review",
    "reviewRating": { "@type": "Rating", "ratingValue": "5", "bestRating": "5" },
    "author": { "@type": "Person", "name": "Verified Customer" },
    "reviewBody": "We had a bee issue in our backyard and needed someone who would solve the problem and not take advantage of us. Timely, professional, and rates were competitive. We will be using Aaron again!"
  },
  {
    "@type": "Review",
    "reviewRating": { "@type": "Rating", "ratingValue": "5", "bestRating": "5" },
    "author": { "@type": "Person", "name": "Verified Customer" },
    "reviewBody": "He took care of the wasps and also found some nests that I didn't even realize existed. Thorough and honest — exactly what you want when dealing with stinging insects."
  }
]
```

**Acceptance criteria:**
- [ ] JSON-LD validates at validator.schema.org with 0 errors
- [ ] `aggregateRating` present with ratingValue 5.0 and reviewCount 20
- [ ] 3 `Review` objects match review text already on page (no fabrication)
- [ ] `image` points to `https://aaronbeeremoval.com/og-image.png`

---

## Task 3: og:image + Twitter Card meta tags

Add to `<head>` after existing OG tags:
```html
<meta property="og:image" content="https://aaronbeeremoval.com/og-image.png">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Aaron's Bee Removal — Eco-Friendly Live Bee Removal in Los Angeles">
<meta name="twitter:description" content="Safe, same-day bee removal and live relocation. Serving all of Southern California since 2008.">
<meta name="twitter:image" content="https://aaronbeeremoval.com/og-image.png">
```

**Acceptance criteria:**
- [ ] `og:image` present and points to real asset
- [ ] `og:image:width` and `og:image:height` set
- [ ] Twitter Card tags present (enables large image preview on X/Twitter)

---

## Non-Goals

- ❌ FAQPage schema (no FAQ section exists on page)
- ❌ Hero illustration (Phase 2b)
- ❌ Any layout changes
- ❌ New pages

---

## Validation

```bash
# 1. JSON-LD: copy block, paste into https://validator.schema.org/
# Expected: aggregateRating present, review array present, 0 errors

# 2. Social preview: paste URL into https://www.opengraph.xyz/
# Expected: og-image.png shown as preview card

# 3. Image check
# open og-image.png in browser — readable at 600px width?

# 4. No regressions
# All Phase 1 + 2a elements still present (run heading check, verify CTAs)
```

---

## Commit (after human approval only)

```
feat: Phase 3 rich results — aggregateRating, Review schema, og:image

- Add aggregateRating (5.0, 20 reviews) to JSON-LD
- Add 3 Review objects matching existing on-page reviews
- Generate og-image.png (1200x630 branded card)
- Set JSON-LD image field to hosted PNG URL
- Add og:image + Twitter Card meta tags
```
