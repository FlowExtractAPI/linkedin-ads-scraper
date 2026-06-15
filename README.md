# 📢 LinkedIn Ad Library Scraper

**Extract comprehensive advertising data from LinkedIn's Ad Library with automatic pagination, advanced filtering, URL-based search, and optional ad detail enrichment.**

This Apify actor extracts detailed ad information from LinkedIn's public Ad Library, delivering structured data about ad campaigns including creatives, headlines, advertiser details, impressions, targeting parameters, and more.

## 🎬 Video Tutorial

<a href="https://www.youtube.com/watch?v=q5PGi7bmdXw">
  <img src="https://img.youtube.com/vi/q5PGi7bmdXw/maxresdefault.jpg" alt="LinkedIn Ad Library Scraper Tutorial" width="400">
</a>

---

## Key Features

### Comprehensive Ad Data Extraction
- **Ad Creatives** — Images, videos (all quality variants), and carousel content
- **Ad Copy** — Headlines, body text, descriptions, and full untruncated ad text
- **Advertiser Information** — Company names, logos, bio/subtitle text, profile URLs
- **Call-to-Action** — CTA button text and destination links
- **External Links** — URLs mentioned in ad text
- **Ad Detail Enrichment** — Optional deep-fetch of impressions, targeting, dates, payer info, and video URLs per ad
- **Metadata** — Ad IDs, creative types, run dates, status (active/inactive), and timestamps

### Performance & Reliability
- **Smart Pagination** — Automatically handles multi-page results across every search
- **Automatic Retries** — Transient errors and rate limits are retried for you
- **Concurrent Detail Fetching** — Enriches multiple ads in parallel
- **Rate Limit Handling** — Automatic detection and back-off on HTTP 429 responses

### Two Search Modes
- **URL Mode** — Paste LinkedIn Ad Library URLs directly (with all filters pre-applied)
- **Search Mode** — Build one or more searches, each with its own company, payer, keyword, country, date range, impressions, targeting, and sort order

---

## 🔍 How Searching Works

The actor mirrors exactly how search works on **linkedin.com/ad-library**: you fill in the **Company or advertiser**, **Payer**, and/or **Keyword** fields, pick your **Country**, **Date**, and **More** filters, and run the search.

This actor lets you queue **many of those searches at once**. There are two ways to provide them, and you can use either or both in a single run:

1. **URLs** — paste any number of LinkedIn Ad Library URLs. Every filter already baked into the URL is respected.
2. **Searches** — build searches from scratch in the input form. **Each search is fully independent and carries its own filters.**

This is the key difference from a single global search: you can run *"apify, newest first, US"* and *"marketing, last 30 days, Germany, min 10k impressions"* **in the same run**, each with completely different settings.

### Each search needs at least one identifier

LinkedIn can't run a search with no search terms, so **every search must have at least one of: Company, Payer, or Keyword.** You can fill in just one, any two, or all three. A search with none of these is skipped automatically (the rest of your searches still run). All the other fields — country, date, impressions, targeting, sort — are optional refinements.

---

## 📊 Results & Cost

`maxResults` sets the cap **per URL and per search** — not for the whole run.

> **Worst-case total = (number of URLs + number of searches) × maxResults**

**Example:** 2 URLs + 3 searches, with `maxResults = 11`:

```
(2 + 3) × 11 = 55 ads maximum
```

Set `maxResults` to `0` for unlimited results per URL/search.

---

## 🌐 Proxy & Reliability

This Actor uses a tiered proxy strategy to deliver the best possible success rate.

**Paying users** get our **premium dedicated proxy infrastructure** optimized specifically for LinkedIn Ad Library, with higher success rates, faster response times, and significantly fewer rate-limit interruptions on large scrapes.

**Free users** run on the standard Apify Proxy network. It works well for small jobs and quick tests, but on larger or sustained scrapes you may see slower runs and occasional retries.

If you're hitting limits on the free tier or running production workloads, **upgrading to a paid plan automatically activates the premium proxy** — no configuration required.

| | Free | Paid |
|---|---|---|
| Proxy network | Apify Proxy | Premium dedicated infrastructure |
| Rate-limit resilience | Standard | Enhanced |
| Best for | Small scrapes, testing | Production workloads, large scrapes |

> **Automatic failover:** If the premium proxy is ever unreachable, the Actor seamlessly falls back to Apify Proxy mid-run — your scrape never stops.

---

## 🚀 Quick Start

### Basic Keyword Search

```json
{
  "searches": [
    { "keyword": "apify" }
  ],
  "maxResults": 50
}
```

### Search by URL

```json
{
  "urls": [
    { "url": "https://www.linkedin.com/ad-library/search?keyword=sony&countries=DZ" }
  ],
  "maxResults": 20
}
```

### Search by Company and Payer

```json
{
  "searches": [
    { "accountOwner": "Apify", "payer": "Apify Technologies s.r.o." }
  ],
  "maxResults": 30
}
```

---

## 🎯 Independent Searches in One Run

Each entry in `searches` runs as its own search, with its own filters. Mix regions, dates, sort orders, and impression ranges freely:

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

This runs **3 independent searches**:
1. `web scraping` in the US, newest first
2. `HubSpot` ads in Germany from the last 30 days with at least 10k impressions
3. `black friday` ads during November 2025

**Total: 3 searches × 50 = 150 max results.**

### URLs + Searches Combined

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

The URL runs as one job and the search runs as another, each independently paginated and capped at `maxResults`.

**Total: (1 URL + 1 search) × 10 = 20 max results.**

---

## 📋 Input Configuration

### Top-Level Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `urls` | array | ❌ | - | LinkedIn Ad Library URLs. Only `linkedin.com/ad-library` URLs are accepted — other URLs are rejected before the run starts. |
| `searches` | array | ❌ | - | One or more independent searches (see per-search fields below) |
| `maxResults` | integer | ❌ | 10 | Max ads **per URL / per search** (0 = unlimited) |
| `fetchAdDetail` | boolean | ❌ | false | Fetch the full ad detail page for each ad |

### Per-Search Fields (inside each `searches` entry)

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `accountOwner` | string | ⚠️* | - | Company or advertiser name |
| `payer` | string | ⚠️* | - | Entity that paid for the ad |
| `keyword` | string | ⚠️* | - | Word or phrase to search |
| `countries` | array | ❌ | All | Country codes (e.g., US, GB, DZ) |
| `sortOrder` | string | ❌ | DESCENDING | `ASCENDING` or `DESCENDING` |
| `dateOption` | string | ❌ | - | Predefined date filter (see below) |
| `startDate` | string | ❌ | - | Custom start date (YYYY-MM-DD) |
| `endDate` | string | ❌ | - | Custom end date (YYYY-MM-DD) |
| `impressionsMinValue` | integer | ❌ | - | Minimum impressions value |
| `impressionsMinUnit` | string | ❌ | - | `none`, `thousand`, or `million` |
| `impressionsMaxValue` | integer | ❌ | - | Maximum impressions value |
| `impressionsMaxUnit` | string | ❌ | - | `none`, `thousand`, or `million` |
| `includedTargetingFacetCategories` | array | ❌ | - | Targeting categories to include |
| `excludedTargetingFacetCategories` | array | ❌ | - | Targeting categories to exclude |

> **⚠️ \*At least one** of `accountOwner`, `payer`, or `keyword` is required **per search**. You may fill one, two, or all three.

### Impressions Units

| Value | Meaning | Example |
|-------|---------|---------|
| `none` | Exact number (no multiplier) | `impressionsMinValue: 5, impressionsMinUnit: none` → min 5 impressions |
| `thousand` | Multiply by 1,000 | `impressionsMinValue: 5, impressionsMinUnit: thousand` → min 5,000 impressions |
| `million` | Multiply by 1,000,000 | `impressionsMaxValue: 2, impressionsMaxUnit: million` → max 2,000,000 impressions |

### Targeting Facet Categories

Available values for `includedTargetingFacetCategories` and `excludedTargetingFacetCategories`:

`LANGUAGE`, `LOCATION`, `AUDIENCE`, `DEMOGRAPHIC`, `COMPANY`, `EDUCATION`, `JOB`, `INTERESTS_AND_TRAITS`

A category can appear in both included and excluded arrays simultaneously — this corresponds to LinkedIn's "Both" filter option.

### Date Filter Options

| Value | Description |
|-------|-------------|
| `last-30-days` | Last 30 days |
| `current-month` | Current month |
| `current-year` | Current year |
| `last-year` | Previous year |
| `custom-date-range` | Custom range (requires startDate + endDate) |

If `startDate` and `endDate` are both provided, they take priority over `dateOption`.

---

## 📤 Output Structure

### Standard Output (fetchAdDetail: false)

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

### Enriched Output (fetchAdDetail: true)

When `fetchAdDetail` is enabled, each ad includes a `detail` object with deep data from the ad's detail page:

```json
{
  "adId": "1230248844",
  "advertiserName": "Nivide",
  "advertiserBio": "Promoted",
  "isPromoted": true,
  "creativeType": "SPONSORED_STATUS_UPDATE",
  "headline": "Professionellt stöd kring digital annonsering och SEO",
  "adText": "Är det dags för ett omtag kring företagets digitala annonsering eller SEO?...",
  "detailPageUrl": "https://www.linkedin.com/ad-library/detail/1230248844",
  "imageUrl": "https://media.licdn.com/dms/image/...",
  "logoUrl": "https://media.licdn.com/dms/image/...",
  "ctaText": "View details",
  "externalLinksInText": [],
  "scrapedAt": "2026-03-21T20:21:24.677Z",
  "detail": {
    "adType": "Single Image Ad",
    "advertiserName": "Nivide",
    "advertiserProfileUrl": "https://www.linkedin.com/company/5275721",
    "paidForBy": "Nivide AB",
    "adRunDatesRaw": "Ran from Mar 15, 2026 to Mar 21, 2026",
    "adStartDate": "Mar 15, 2026",
    "adEndDate": "Mar 21, 2026",
    "adStatus": "INACTIVE",
    "totalImpressions": "< 1k",
    "impressionsByCountry": [
      {
        "country": "Sweden",
        "impressionPercent": "100%",
        "progressValue": 100
      }
    ],
    "targetingLanguage": "Targeting includes svenska",
    "targetingLocation": "Targeting includes Sverige",
    "targetingParameters": [
      { "category": "Audience", "hasInclusion": false, "hasExclusion": false, "targetingType": "NONE" },
      { "category": "Demographic", "hasInclusion": false, "hasExclusion": false, "targetingType": "NONE" },
      { "category": "Company", "hasInclusion": true, "hasExclusion": true, "targetingType": "BOTH" },
      { "category": "Education", "hasInclusion": false, "hasExclusion": false, "targetingType": "NONE" },
      { "category": "Job", "hasInclusion": true, "hasExclusion": true, "targetingType": "BOTH" },
      { "category": "Member Interests and Traits", "hasInclusion": false, "hasExclusion": false, "targetingType": "NONE" }
    ],
    "creativeType": "SPONSORED_STATUS_UPDATE",
    "fullAdText": "Är det dags för ett omtag kring företagets digitala annonsering eller SEO?...",
    "headlineText": "Professionellt stöd kring digital annonsering och SEO",
    "destinationUrl": "https://nivide.se?trk=ad_library_ad_preview_headline_content",
    "ctaButtonText": "Läs mer",
    "advertiserLogoUrl": "https://media.licdn.com/dms/image/...",
    "imageUrl": "https://media.licdn.com/dms/image/...",
    "videoUrls": [],
    "videoPosterUrl": null
  }
}
```

### Output Fields Reference

#### Standard Fields (always present)

| Field | Description |
|-------|-------------|
| `adId` | Unique LinkedIn ad identifier |
| `advertiserName` | Company/advertiser name |
| `advertiserBio` | Advertiser bio/subtitle text |
| `isPromoted` | Whether ad is marked as promoted |
| `creativeType` | Type: SPONSORED_STATUS_UPDATE, SPONSORED_VIDEO, etc. |
| `headline` | Ad headline text |
| `adText` | Main ad body text (may be truncated) |
| `detailPageUrl` | LinkedIn URL to ad detail page |
| `imageUrl` | Ad creative image URL |
| `logoUrl` | Advertiser logo URL |
| `ctaText` | Call-to-action text |
| `externalLinksInText` | External URLs found in ad text |
| `scrapedAt` | ISO 8601 timestamp |

#### Detail Fields (when fetchAdDetail: true)

| Field | Description |
|-------|-------------|
| `detail.adType` | "Video Ad", "Single Image Ad", etc. |
| `detail.advertiserName` | Advertiser name from detail page |
| `detail.advertiserProfileUrl` | LinkedIn company page URL |
| `detail.paidForBy` | Legal entity that paid for the ad |
| `detail.adStartDate` | When the ad started running |
| `detail.adEndDate` | When the ad stopped (null if still active) |
| `detail.adStatus` | ACTIVE, INACTIVE, or UNKNOWN |
| `detail.adRunDatesRaw` | Raw date string from the page |
| `detail.totalImpressions` | Impression range string (e.g., "100k-150k") |
| `detail.impressionsByCountry` | Array of { country, impressionPercent, progressValue } |
| `detail.targetingLanguage` | Language targeting text |
| `detail.targetingLocation` | Location targeting text |
| `detail.targetingParameters` | Array of { category, hasInclusion, hasExclusion, targetingType } |
| `detail.fullAdText` | Full untruncated ad text |
| `detail.headlineText` | Headline from card |
| `detail.destinationUrl` | Full destination URL with UTM params |
| `detail.ctaButtonText` | CTA button label (e.g., "Learn more") |
| `detail.videoUrls` | Array of { src, type, bitrate } for all quality variants |
| `detail.videoPosterUrl` | Video thumbnail URL |
| `detail.advertiserLogoUrl` | Logo image URL |
| `detail.imageUrl` | Ad image URL |
| `detail.creativeType` | Creative type from data attribute |

---

## 📊 Pre-Configured Data Views

### 1. 📢 Overview
Quick summary of all ads with essential information.

**Fields:** Ad image, ID, advertiser, bio, creative type, headline, ad text, CTA, LinkedIn URL

### 2. 📋 Detailed View
Complete ad information including external links, timestamps, and enriched detail data.

**Fields:** All standard fields + detail fields (impressions, targeting, dates, video URLs)

### 3. 🏢 Advertisers
Ads grouped by advertiser/company.

**Fields:** Logo, company name, bio, ad ID, headline, creative type, URL

### 4. 🎨 Creative Analysis
Focus on ad creatives and content.

**Fields:** Creative image, ID, advertiser, bio, type, headline, body text, CTA

---

## ⚙️ More Examples

### Full Filters with Impressions and Targeting

```json
{
  "searches": [
    {
      "keyword": "sony",
      "countries": ["DZ"],
      "impressionsMinValue": 5,
      "impressionsMinUnit": "none",
      "impressionsMaxValue": 123456789,
      "impressionsMaxUnit": "none",
      "includedTargetingFacetCategories": ["LANGUAGE", "AUDIENCE", "JOB"],
      "excludedTargetingFacetCategories": ["LOCATION", "COMPANY"],
      "sortOrder": "ASCENDING"
    }
  ],
  "maxResults": 50
}
```

### Multiple URLs with Detail Enrichment

```json
{
  "urls": [
    { "url": "https://www.linkedin.com/ad-library/search?keyword=apify&countries=US%2CGB" },
    { "url": "https://www.linkedin.com/ad-library/search?accountOwner=nivide&payer=nivide" }
  ],
  "fetchAdDetail": true,
  "maxResults": 20
}
```

### Competitor Research Across Regions

```json
{
  "searches": [
    { "keyword": "web scraping", "accountOwner": "ParseHub", "countries": ["US"] },
    { "keyword": "data extraction", "accountOwner": "Octoparse", "countries": ["GB"] },
    { "keyword": "automation", "accountOwner": "Apify", "payer": "Apify Technologies s.r.o.", "countries": ["CA"] }
  ],
  "dateOption": "last-30-days",
  "fetchAdDetail": true,
  "maxResults": 50
}
```

Three independent searches, each scoped to its own company and region. **Total: 3 × 50 = 150 max results.**

### Seasonal Campaign Analysis (Unlimited)

```json
{
  "searches": [
    {
      "keyword": "black friday",
      "dateOption": "custom-date-range",
      "startDate": "2025-11-01",
      "endDate": "2025-11-30"
    }
  ],
  "maxResults": 0
}
```

---

## 📈 Performance

### Without fetchAdDetail
- **Page (10 ads)** — a few seconds
- **100 Ads** — well under a minute
- **1000 Ads** — a few minutes

### With fetchAdDetail Enabled
Enabling detail enrichment fires one extra request per ad, so expect runs to take longer — typically a few minutes per 100 ads depending on retry rate.

### Resource Usage
- **Memory** — 256MB minimum, 512MB recommended
- **Network** — Moderate bandwidth (higher with fetchAdDetail)

---

## ⚠️ Important Notes

### Legal Considerations

This actor extracts publicly available data from LinkedIn's Ad Library. Users must:
- Comply with LinkedIn's Terms of Service
- Respect robots.txt directives
- Follow applicable data protection laws (GDPR, CCPA, etc.)
- Use data responsibly and ethically

**The actor creator is not responsible for how users utilize the extracted data.**

### Data Accuracy

- Data is extracted as-is from LinkedIn Ad Library
- Ad availability may change between scraping sessions
- Some fields may be null if not present in the ad
- The `detail` object depends on the detail page layout — if LinkedIn changes it, some fields may return null
- Video URLs include all quality variants with bitrate info

### Searches & Validation

- Only `linkedin.com/ad-library` URLs are accepted — other URLs are rejected before the run starts
- Each search must include at least one of Company, Payer, or Keyword; searches missing all three are skipped automatically, and the rest of the run continues

---

## 💬 Support & Contact

### Get Help

- 🌐 **Website**: [flowextractapi.com](https://flowextractapi.com)
- 📧 **Email**: [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- 🙋 **Apify Profile**: [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63)
- 💬 **GitHub Issues**: [FlowExtractAPI](https://github.com/FlowExtractAPI)

### Social Media

- 💼 **LinkedIn**: [flowextract-api](https://www.linkedin.com/in/flowextract-api/)
- 🐦 **Twitter**: [@FlowExtractAPI](https://x.com/@FlowExtractAPI)
- 📱 **Facebook**: [flowextractapi](https://www.facebook.com/flowextractapi)

## 🌟 Related Actors by FlowExtract API

### 🎬 Video & Media Tools

**[YouTube Transcript & Metadata Extractor](https://apify.com/dz_omar/youtube-transcript-metadata?fpr=smcx63)**
Extract complete video transcripts with timestamps and comprehensive metadata.

**[YouTube Full Channel, Playlists, Shorts, Live](https://apify.com/dz_omar/Youtube-Scraper-Pro?fpr=smcx63)**
Extract complete playlist information with all video details from any YouTube playlist.

**[Zoom Scraper | 🎥 Downloader & 📄 Transcript](https://apify.com/dz_omar/zoom-scraper?fpr=smcx63)**
Extract Zoom meeting recordings, transcripts, and metadata.

**[Loom Scraper | 🎥 Downloader & 📄 Transcript](https://apify.com/dz_omar/loom-video-scraper?fpr=smcx63)**
Download Loom videos and extract transcripts.

### 🏠 Real Estate Data

**[PropertyFinder Scraper](https://apify.com/dz_omar/propertyfinder-scraper?fpr=smcx63)**
Extract real estate property listings from PropertyFinder across UAE, Saudi Arabia, Bahrain, Egypt, and Qatar.

**[Idealista Scraper API](https://apify.com/dz_omar/idealista-scraper-api?fpr=smcx63)**
Advanced Idealista property data extraction with API access.

**[Idealista Scraper](https://apify.com/dz_omar/idealista-scraper?fpr=smcx63)**
Extract Spanish real estate listings from Idealista.

### 🛠️ Developer & Security Tools

**[Screenshot](https://apify.com/dz_omar/screenshot?fpr=smcx63)**
Fast, reliable webpage screenshots with customizable options.

**[Ultimate Screenshot](https://apify.com/dz_omar/ultimate-screenshot?fpr=smcx63)**
Advanced screenshot tool with full-page capture, custom viewports, and quality controls.

**[Network Security Scanner](https://apify.com/dz_omar/network-security-scanner?fpr=smcx63)**
Scan websites for security vulnerabilities and get comprehensive security reports.

### 📱 Social Media Tools

**[Facebook Ads Scraper Pro](https://apify.com/dz_omar/facebook-ads-scraper-pro?fpr=smcx63)**
Extract Facebook ads data for competitor analysis and market research.

**[AI Contact Intelligence Extractor](https://apify.com/dz_omar/ai-contact-intelligence?fpr=smcx63)**
Extract emails, phones, contacts & custom data using AI.

---

**Ready to extract LinkedIn ad data?** [Start using LinkedIn Ad Library Scraper now!](https://apify.com/dz_omar/linkedin-ads-scraper?fpr=smcx63)
