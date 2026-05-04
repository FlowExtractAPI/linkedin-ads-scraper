# 📢 LinkedIn Ad Library Scraper

**Extract comprehensive advertising data from LinkedIn's Ad Library with automatic pagination, advanced filtering, URL-based search, and optional ad detail enrichment.**

This Apify actor extracts detailed ad information from LinkedIn's public Ad Library, delivering structured data about ad campaigns including creatives, headlines, advertiser details, impressions, targeting parameters, and more.


## 🎬 Video Tutorial

[![Watch the Tutorial](https://img.shields.io/badge/YouTube-Watch%20Tutorial-red?style=for-the-badge&logo=youtube)](https://youtube.com/@FlowExtractAPI)
Watch a full demo: [YouTube – LinkedIn Ad Library Scraper Tutorial](https://www.youtube.com/watch?v=q5PGi7bmdXw)


---

## Key Features

###  Comprehensive Ad Data Extraction
- **Ad Creatives**  Images, videos (all quality variants), and carousel content
- **Ad Copy**  Headlines, body text, descriptions, and full untruncated ad text
- **Advertiser Information**  Company names, logos, bio/subtitle text, profile URLs
- **Call-to-Action**  CTA button text and destination links
- **External Links**  URLs mentioned in ad text
- **Ad Detail Enrichment**  Optional deep-fetch of impressions, targeting, dates, payer info, and video URLs per ad
- **Metadata**  Ad IDs, creative types, run dates, status (active/inactive), and timestamps

###  Performance & Reliability
- **Smart Pagination**  Automatically handles multi-page results with token tracking and stall detection
- **Retry Logic**  Configurable exponential backoff for both search pages and ad detail pages
- **Concurrent Detail Fetching**  Fetch multiple ad detail pages in parallel with configurable concurrency
- **Rate Limit Handling**  Automatic detection and wait on HTTP 429 responses
- **Proxy Support**  Optional Apify Proxy integration with residential proxy groups

### Two Search Modes
- **URL Mode**  Paste LinkedIn Ad Library URLs directly (with all filters pre-applied)
- **Filter Mode**  Search by keyword, account owner, payer, country, date range, impressions, targeting facets, and sort order

### 🤝 ZIP Job Logic
When providing multiple keywords, account owners, and payers, they are paired **by index position** (not cross-product). This gives you precise control over which combinations run.

---

##  Quick Start

### Basic Keyword Search

```json
{
  "keyword": ["apify"],
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

### Advanced Configuration

```json
{
  "keyword": ["saas", "marketing automation"],
  "accountOwner": ["Apify", "HubSpot"],
  "payer": ["Apify Technologies s.r.o.", "HubSpot Inc."],
  "countries": ["US", "GB", "CA"],
  "dateOption": "last-30-days",
  "sortOrder": "ASCENDING",
  "fetchAdDetail": true,
  "maxResults": 100,
  "proxyConfiguration": {
    "useApifyProxy": true,
    "apifyProxyGroups": ["RESIDENTIAL"]
  }
}
```

---

## 🤝 How ZIP Job Logic Works

The scraper pairs `keyword`, `accountOwner`, and `payer` arrays **by index position**, not as a cross-product. Each index creates one search job, and all shared filters (countries, date, impressions, targeting, sort order) are applied to every job.

### Example 1: Simple Pairing

```json
{
  "keyword": ["Keyword1", "", "Keyword3", ""],
  "accountOwner": ["Company0", "Advertiser1", "Company3", "Advertiser4"],
  "payer": ["Payer1", "Payer2", "", ""],
  "maxResults": 11
}
```

This creates **4 jobs** (paired by index):

| Job | keyword | accountOwner | payer | maxResults |
|-----|---------|-------------|-------|------------|
| 1 | Keyword1 | Company0 | Payer1 | 11 |
| 2 | *(empty)* | Advertiser1 | Payer2 | 11 |
| 3 | Keyword3 | Company3 | *(empty)* | 11 |
| 4 | *(empty)* | Advertiser4 | *(empty)* | 11 |

**Total expected results: 4 jobs × 11 = 44**

Empty strings (`""`) are treated as "no filter" for that field in that job.

### Example 2: Unequal Array Lengths

```json
{
  "keyword": ["sony"],
  "accountOwner": ["Company1", "Company2"],
  "payer": ["Payer1"],
  "maxResults": 10
}
```

The number of jobs = `max(keyword.length, accountOwner.length, payer.length)` = **2 jobs**. Missing entries are treated as empty:

| Job | keyword | accountOwner | payer |
|-----|---------|-------------|-------|
| 1 | sony | Company1 | Payer1 |
| 2 | *(empty)* | Company2 | *(empty)* |

### Example 3: URLs + Filters Combined

```json
{
  "urls": [
    { "url": "https://www.linkedin.com/ad-library/search?keyword=sony&countries=DZ" }
  ],
  "keyword": ["apify"],
  "payer": ["Apify Technologies s.r.o."],
  "maxResults": 10
}
```

**Phase 1** runs 1 URL job (with full pagination, respecting maxResults).
**Phase 2** runs 1 filter job (`keyword=apify + payer=Apify Technologies s.r.o.`).

**Total: 2 jobs × 10 = 20 max results.**

### Example 4: Only One Field Provided

```json
{
  "accountOwner": ["Company5"],
  "maxResults": 9
}
```

This creates **1 job**: `accountOwner=Company5`, keyword and payer are empty.

**Total: 1 job × 9 = 9 max results.**

---

## 📋 Input Configuration

### Input Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| **Search by URL** | | | | |
| `urls` | array | ❌ | - | LinkedIn Ad Library URLs (requestListSources) |
| **Search by Filters** | | | | |
| `keyword` | array | ❌ | - | Keywords to search (zipped with accountOwner/payer) |
| `accountOwner` | array | ❌ | - | Advertiser account names (zipped) |
| `payer` | array | ❌ | - | Who paid for the ad (zipped) |
| `countries` | array | ❌ | All | Country codes (e.g., US, GB, DZ) |
| `sortOrder` | string | ❌ | DESCENDING | Sort order: ASCENDING or DESCENDING |
| **Date Range** | | | | |
| `dateOption` | string | ❌ | - | Predefined date filter |
| `startDate` | string | ❌ | - | Custom start date (YYYY-MM-DD) |
| `endDate` | string | ❌ | - | Custom end date (YYYY-MM-DD) |
| **Impressions & Targeting** | | | | |
| `impressionsMinValue` | integer | ❌ | - | Minimum impressions value |
| `impressionsMinUnit` | string | ❌ | - | Unit: none, thousand, or million |
| `impressionsMaxValue` | integer | ❌ | - | Maximum impressions value |
| `impressionsMaxUnit` | string | ❌ | - | Unit: none, thousand, or million |
| `includedTargetingFacetCategories` | array | ❌ | - | Targeting categories to include |
| `excludedTargetingFacetCategories` | array | ❌ | - | Targeting categories to exclude |
| **Run Settings** | | | | |
| `maxResults` | integer | ❌ | 10 | Max ads per job (0 = unlimited) |
| `fetchAdDetail` | boolean | ❌ | false | Fetch full ad detail page per ad |
| `proxyConfiguration` | object | ❌ | - | Apify proxy settings |

### Impressions Units

The `impressionsMinUnit` and `impressionsMaxUnit` fields support three values:

| Value | Meaning | Example |
|-------|---------|---------|
| `none` | Exact number (no multiplier) | `impressionsMinValue: 5, impressionsMinUnit: none` → min 5 impressions |
| `thousand` | Multiply by 1,000 | `impressionsMinValue: 5, impressionsMinUnit: thousand` → min 5,000 impressions |
| `million` | Multiply by 1,000,000 | `impressionsMaxValue: 2, impressionsMaxUnit: million` → max 2,000,000 impressions |

### Targeting Facet Categories

Available values for `includedTargetingFacetCategories` and `excludedTargetingFacetCategories`:

`LANGUAGE`, `LOCATION`, `AUDIENCE`, `DEMOGRAPHIC`, `COMPANY`, `EDUCATION`, `JOB`, `INTERESTS_AND_TRAITS`

A category can appear in both included and excluded arrays simultaneously  this corresponds to LinkedIn's "Both" filter option.

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

## ⚙️ Advanced Configuration Examples

### Full Filters with Impressions and Targeting

```json
{
  "keyword": ["sony"],
  "countries": ["DZ"],
  "impressionsMinValue": 5,
  "impressionsMinUnit": "none",
  "impressionsMaxValue": 123456789,
  "impressionsMaxUnit": "none",
  "includedTargetingFacetCategories": ["LANGUAGE", "AUDIENCE", "JOB"],
  "excludedTargetingFacetCategories": ["LOCATION", "COMPANY"],
  "sortOrder": "ASCENDING",
  "maxResults": 50
}
```

### URL Mode with Detail Enrichment

```json
{
  "urls": [
    { "url": "https://www.linkedin.com/ad-library/search?keyword=apify&countries=US%2CGB" },
    { "url": "https://www.linkedin.com/ad-library/search?accountOwner=nivide&payer=nivide" }
  ],
  "fetchAdDetail": true,
  "maxResults": 20,
  "proxyConfiguration": {
    "useApifyProxy": true,
    "apifyProxyGroups": ["RESIDENTIAL"]
  }
}
```

### Zipped Multi-Keyword Competitor Research

```json
{
  "keyword": ["web scraping", "data extraction", "automation"],
  "accountOwner": ["ParseHub", "Octoparse", "Apify"],
  "payer": ["", "", "Apify Technologies s.r.o."],
  "countries": ["US", "GB", "CA"],
  "dateOption": "last-30-days",
  "fetchAdDetail": true,
  "maxResults": 50
}
```

This creates 3 zipped jobs:
1. keyword="web scraping" + account="ParseHub" + payer=*(none)*
2. keyword="data extraction" + account="Octoparse" + payer=*(none)*
3. keyword="automation" + account="Apify" + payer="Apify Technologies s.r.o."

Each with the same country, date, and detail settings. Total: 3 × 50 = 150 max results.

### Seasonal Campaign Analysis

```json
{
  "keyword": ["black friday", "holiday sale"],
  "dateOption": "custom-date-range",
  "startDate": "2025-11-01",
  "endDate": "2025-11-30",
  "maxResults": 0
}
```

---

## 📈 Performance Metrics

### Scraping Speed (without fetchAdDetail)
- **Single Ad**  ~0.2 seconds
- **Page (10 ads)**  ~2–3 seconds (including delay)
- **100 Ads**  ~30–40 seconds
- **1000 Ads**  ~5–7 minutes

### With fetchAdDetail Enabled
- **Per ad detail page**  ~0.5–1 second
- **With concurrency 3**  ~3 ads per second per batch
- **100 ads with detail**  ~2–4 minutes (depending on retry rate)

### Resource Usage
- **Memory**  256MB minimum, 512MB recommended
- **CPU**  Low to moderate
- **Network**  Moderate bandwidth (higher with fetchAdDetail)

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
- The `detail` object depends on the detail page structure  if LinkedIn changes it, fields may return null
- Video URLs include all quality variants with bitrate info

### Rate Limiting

- Built-in configurable delays between pages (default 2s)
- Retry logic with exponential backoff handles temporary failures
- HTTP 429 responses trigger automatic wait (default 5s)
- Proxy usage strongly recommended for large-scale operations or when fetchAdDetail is enabled

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
