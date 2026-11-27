**[LinkedIn Ads Scraper](https://apify.com/dz_omar/linkedin-ads-scraper?fpr=smcx63)** extracts **ads from LinkedIn's public Ad Library** — **creatives, headlines, ad copy, advertisers, CTAs and, optionally, impressions by country, targeting, run dates, payer and video URLs**. Paste Ad Library URLs or build many independent searches in one run.

Perfect for **marketers**, **competitive-intelligence teams** and **agencies** researching B2B ad campaigns.

https://www.youtube.com/watch?v=q5PGi7bmdXw

## What does LinkedIn Ads Scraper do?

- **Ad creatives** — images, videos (all quality variants) and carousel content.
- **Ad copy** — headlines, body text and the full untruncated ad text.
- **Advertisers** — company name, logo, bio/subtitle and profile URL.
- **Call-to-action** — CTA button text and destination links, plus URLs mentioned in the ad text.
- **Optional detail enrichment** — total impressions, impressions by country, targeting parameters, run dates, status, payer and video URLs.
- **Many independent searches per run** — each with its own company, payer, keyword, country, dates, impressions, targeting and sort order.

### What data can you extract from the LinkedIn Ad Library?

| Field | Description |
|-------|-------------|
| `adId` | Unique LinkedIn ad identifier |
| `advertiserName`, `advertiserBio`, `logoUrl` | Advertiser name, bio/subtitle and logo |
| `isPromoted`, `creativeType` | Promoted flag; SPONSORED_STATUS_UPDATE, SPONSORED_VIDEO, … |
| `headline`, `adText` | Headline and main body text (may be truncated) |
| `imageUrl`, `ctaText`, `externalLinksInText` | Creative image, CTA text, URLs in the ad text |
| `detailPageUrl`, `scrapedAt` | Ad detail page URL and scrape time |
| `detail.adType`, `detail.adStatus` | "Video Ad", "Single Image Ad", …; ACTIVE / INACTIVE / UNKNOWN |
| `detail.paidForBy`, `detail.advertiserProfileUrl` | Legal entity that paid; LinkedIn company page |
| `detail.adStartDate`, `detail.adEndDate`, `detail.adRunDatesRaw` | When the ad ran |
| `detail.totalImpressions`, `detail.impressionsByCountry` | Impression range and per-country share |
| `detail.targetingLanguage`, `detail.targetingLocation`, `detail.targetingParameters` | Targeting text and inclusion/exclusion per category |
| `detail.fullAdText`, `detail.headlineText`, `detail.destinationUrl`, `detail.ctaButtonText` | Full text, headline, destination with UTM params, CTA label |
| `detail.videoUrls`, `detail.videoPosterUrl`, `detail.imageUrl`, `detail.advertiserLogoUrl` | Media |

### Why use LinkedIn Ads Scraper on Apify?

Running it on Apify gives you **API access**, **scheduling**, **monitoring**, **integrations** (Make, Zapier, n8n, webhooks) and **proxy rotation** without managing infrastructure.

## How to scrape the LinkedIn Ad Library

1. Open [LinkedIn Ads Scraper](https://apify.com/dz_omar/linkedin-ads-scraper?fpr=smcx63) in Apify Console.
2. Paste LinkedIn Ad Library URLs into **URLs** (every filter in the URL is respected), and/or add **Searches**.
3. Give each search at least one of **Company**, **Payer** or **Keyword**, then any country, date, impressions, targeting or sort filters.
4. Set **Max results** per URL/search (start small: `10`) and turn on **Fetch ad detail** for impressions and targeting.
5. Click **Start**, then download the results as JSON, CSV or Excel, or fetch them over the API.

## How much does it cost to scrape LinkedIn ads?

You pay per ad saved plus a small start fee:

| Event | FREE | BRONZE | SILVER | GOLD |
|---|---|---|---|---|
| Ad extracted | $0.0015 | $0.001 | $0.0008 | $0.00045 |
| Actor start (per run) | $0.01 | $0.01 | $0.01 | $0.01 |

- **1,000 ads** cost about **$1.50** on the FREE plan price and **$0.45** on GOLD (+ the start fee).
- `maxResults` caps **each URL and each search**: worst-case total = (URLs + searches) × `maxResults`. For example 2 URLs + 3 searches at `maxResults: 11` is at most 55 ads. `0` = unlimited.

## Input and output examples

### Input

Independent searches in one run — each with its own filters:

```json
{
  "searches": [
    {
      "keyword": "web scraping",
      "countries": ["US"],
      "sortOrder": "DESCENDING"
    },
    {
      "accountOwner": "HubSpot",
      "countries": ["DE"],
      "dateOption": "last-30-days",
      "impressionsMinValue": 10,
      "impressionsMinUnit": "thousand"
    },
    {
      "keyword": "black friday",
      "dateOption": "custom-date-range",
      "startDate": "2025-11-01",
      "endDate": "2025-11-30"
    }
  ],
  "fetchAdDetail": true,
  "maxResults": 50
}
```

URLs and searches combined:

```json
{
  "urls": [
    { "url": "https://www.linkedin.com/ad-library/search?keyword=sony&countries=DZ" }
  ],
  "searches": [
    { "keyword": "apify", "payer": "Apify Technologies s.r.o." }
  ],
  "maxResults": 10
}
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `urls` | array | — | LinkedIn Ad Library URLs. Only `linkedin.com/ad-library` URLs are accepted |
| `searches` | array | — | One or more independent searches (fields below) |
| `maxResults` | integer | 10 | Max ads **per URL / per search** (0 = unlimited) |
| `fetchAdDetail` | boolean | false | Fetch the full ad detail page for each ad |

| Per-search field | Type | Description |
|-------|------|-------------|
| `accountOwner` | string | Company or advertiser name ⚠️ |
| `payer` | string | Entity that paid for the ad ⚠️ |
| `keyword` | string | Word or phrase to search ⚠️ |
| `countries` | array | Country codes (e.g. US, GB, DZ); default all |
| `sortOrder` | string | `ASCENDING` or `DESCENDING` (default) |
| `dateOption` | string | `last-30-days`, `current-month`, `current-year`, `last-year`, `custom-date-range` |
| `startDate` / `endDate` | string | Custom range (YYYY-MM-DD); takes priority over `dateOption` |
| `impressionsMinValue` / `impressionsMaxValue` | integer | Impression bounds |
| `impressionsMinUnit` / `impressionsMaxUnit` | string | `none`, `thousand` (×1,000) or `million` (×1,000,000) |
| `includedTargetingFacetCategories` / `excludedTargetingFacetCategories` | array | `LANGUAGE`, `LOCATION`, `AUDIENCE`, `DEMOGRAPHIC`, `COMPANY`, `EDUCATION`, `JOB`, `INTERESTS_AND_TRAITS` — a category in both lists means LinkedIn's "Both" |

⚠️ Every search needs **at least one** of `accountOwner`, `payer` or `keyword`; a search with none is skipped and the others still run.

### Output (standard)

```json
{
  "adId": "894398854",
  "advertiserName": "Apify",
  "advertiserBio": "Promoted",
  "isPromoted": true,
  "creativeType": "SPONSORED_STATUS_UPDATE",
  "headline": "$1M in prizes for tools AI needs.",
  "adText": "Companies deploying AI hit walls. Tools don't exist yet...",
  "detailPageUrl": "https://www.linkedin.com/ad-library/detail/894398854",
  "imageUrl": "https://media.licdn.com/dms/image/...",
  "logoUrl": "https://media.licdn.com/dms/image/...",
  "ctaText": "View details",
  "externalLinksInText": [],
  "scrapedAt": "2026-03-21T00:55:56.066Z"
}
```

### Output (with `fetchAdDetail: true`)

```json
{
  "adId": "1230248844",
  "advertiserName": "Nivide",
  "creativeType": "SPONSORED_STATUS_UPDATE",
  "headline": "Professionellt stöd kring digital annonsering och SEO",
  "detailPageUrl": "https://www.linkedin.com/ad-library/detail/1230248844",
  "detail": {
    "adType": "Single Image Ad",
    "advertiserProfileUrl": "https://www.linkedin.com/company/5275721",
    "paidForBy": "Nivide AB",
    "adRunDatesRaw": "Ran from Mar 15, 2026 to Mar 21, 2026",
    "adStartDate": "Mar 15, 2026",
    "adEndDate": "Mar 21, 2026",
    "adStatus": "INACTIVE",
    "totalImpressions": "< 1k",
    "impressionsByCountry": [
      { "country": "Sweden", "impressionPercent": "100%", "progressValue": 100 }
    ],
    "targetingLanguage": "Targeting includes svenska",
    "targetingLocation": "Targeting includes Sverige",
    "targetingParameters": [
      { "category": "Company", "hasInclusion": true, "hasExclusion": true, "targetingType": "BOTH" },
      { "category": "Job", "hasInclusion": true, "hasExclusion": true, "targetingType": "BOTH" }
    ],
    "fullAdText": "Är det dags för ett omtag kring företagets digitala annonsering eller SEO?...",
    "destinationUrl": "https://nivide.se?trk=ad_library_ad_preview_headline_content",
    "ctaButtonText": "Läs mer",
    "videoUrls": [],
    "videoPosterUrl": null
  }
}
```

### Dataset views

| View | Fields |
|------|--------|
| 📢 **Overview** | Ad image, ID, advertiser, bio, creative type, headline, ad text, CTA, LinkedIn URL |
| 📋 **Detailed** | All standard fields + detail fields (impressions, targeting, dates, video URLs) |
| 🏢 **Advertisers** | Logo, company name, bio, ad ID, headline, creative type, URL |
| 🎨 **Creative analysis** | Creative image, ID, advertiser, bio, type, headline, body text, CTA |

## Other Actors you might need

<!-- RECOMMENDED:START (generated by apify-actor-forge/tools/recommend.py; edits inside are overwritten) -->

| Actor | What it adds |
|---|---|
| **[LinkedIn Company Scraper](https://apify.com/dz_omar/linkedin-company-scraper?fpr=smcx63)** | LinkedIn company pages in bulk: industry, employee count, HQ, offices & recent posts |
| **[Facebook Ads Scraper Pro](https://apify.com/dz_omar/facebook-ads-scraper-pro?fpr=smcx63)** | Extract Facebook Ad Library data: creatives, targeting & metrics. |
| **[TikTok Ad Library Scraper](https://apify.com/dz_omar/tiktok-ad-library-scraper?fpr=smcx63)** | Creator Posts, Targeting & Reach |
| **[LinkedIn Jobs Scraper](https://apify.com/dz_omar/linkedin-jobs-scraper?fpr=smcx63)** | LinkedIn job postings with salary ranges, applicant counts & the recruiter who posted |
| **[Facebook Comment Scraper](https://apify.com/dz_omar/facebook-comment-scraper?fpr=smcx63)** | Comments & threaded replies from Facebook reels, videos, photos, posts & groups |

<!-- RECOMMENDED:END -->

## Frequently asked questions

### How does searching work?

Exactly like linkedin.com/ad-library: fill in **Company**, **Payer** and/or **Keyword**, pick **Country**, **Date** and **More** filters, and search. This Actor queues **many of those searches at once** — you can run "apify, newest first, US" and "marketing, last 30 days, Germany, min 10k impressions" in the same run.

### Which proxy does it use?

Paying users get a dedicated proxy optimized for the LinkedIn Ad Library, with fewer rate-limit interruptions on large scrapes; other users run on Apify Proxy, which works well for small jobs. If the dedicated proxy is ever unreachable, the run falls back to Apify Proxy automatically.

### How are errors and rate limits handled?

Pagination is automatic across every search, transient errors are retried, and HTTP 429 responses trigger back-off. Detail pages are fetched several at a time.

### Do I need a LinkedIn account?

No. The Ad Library is public — no login and no cookies.

### Is it legal to scrape the LinkedIn Ad Library?

The Ad Library is a public transparency tool and the Actor reads only what any visitor sees. Comply with LinkedIn's terms and applicable data-protection laws. This is not legal advice.

### Can I use LinkedIn Ads Scraper in Make, Zapier, n8n or my own code?

Yes — through Apify's integrations, or with the Apify API and client libraries.

## Support

- 🌐 **Website:** [flowextractapi.com](https://flowextractapi.com)
- 📧 **Email:** [support@flowextractapi.com](mailto:support@flowextractapi.com)
- 💬 **GitHub:** [FlowExtractAPI](https://github.com/FlowExtractAPI)
- 💼 **LinkedIn:** [flowextract-api](https://www.linkedin.com/in/flowextract-api/)
- 🐦 **X (Twitter):** [@FlowExtractAPI](https://x.com/FlowExtractAPI)
- 📱 **Facebook:** [flowextractapi](https://www.facebook.com/flowextractapi)
- 🎵 **TikTok:** [@flowextractapi](https://www.tiktok.com/@flowextractapi)

## Legal & compliance

- **Public data only** — the public LinkedIn Ad Library, no login, no cookies.
- **Rate limiting** — modest request rates with back-off.
- **Data protection** — no storage of personal information or unauthorized data collection.
- **Commercial use** — suitable for business intelligence and research.
- No affiliation with or endorsement by LinkedIn is implied.

---

*LinkedIn Ads Scraper — by FlowExtract API. Turn any website into structured data.*
