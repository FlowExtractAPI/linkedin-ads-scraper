# 📢 LinkedIn Ad Library Scraper

**Extract comprehensive advertising data from LinkedIn's Ad Library with automatic pagination and filtering.**

This powerful Apify actor extracts detailed ad information from LinkedIn's public Ad Library, delivering structured data about ad campaigns including creatives, headlines, advertiser details, and targeting information.

---

## 🌟 Key Features

### 🎯 Comprehensive Ad Data Extraction
- **Ad Creatives** - Images, videos, and carousel content
- **Ad Copy** - Headlines, body text, and descriptions
- **Advertiser Information** - Company names, logos, and bio/subtitle text
- **Call-to-Action** - CTA button text and links
- **External Links** - URLs mentioned in ad text
- **Metadata** - Ad IDs, creative types, and timestamps

### ⚡ Performance & Reliability
- **Smart Pagination** - Automatically handles multi-page results
- **Keyword Search** - Search by keywords or account owners
- **Date Filtering** - Filter ads by time periods
- **Country Filtering** - Target specific geographic regions
- **Rate Limiting** - Built-in delays to prevent blocking
- **Retry Logic** - Exponential backoff with 3 retry attempts
- **Proxy Support** - Optional Apify Proxy integration

### 🎯 Flexible Configuration
- **Multiple Keywords** - Process multiple search terms in one run
- **Account Owners** - Filter by specific advertisers
- **Result Limits** - Control maximum results (or scrape all)
- **Custom Date Ranges** - Search specific time periods
- **Multi-Country** - Search ads from multiple countries

---

## 🚀 Quick Start

### Basic Usage

```json
{
  "keyword": ["apify"],
  "maxResults": 50
}
```

### Advanced Configuration

```json
{
  "keyword": ["saas", "marketing automation"],
  "accountOwner": ["Apify", "HubSpot"],
  "countries": ["US", "GB", "CA"],
  "dateOption": "last-30-days",
  "maxResults": 100,
  "proxyConfiguration": {
    "useApifyProxy": true,
    "apifyProxyGroups": ["RESIDENTIAL"]
  }
}
```

---

## 📋 Input Configuration

### Input Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `keyword` | array | ❌ No | - | Keywords to search in Ad Library |
| `accountOwner` | array | ❌ No | - | Filter by advertiser account names |
| `countries` | array | ❌ No | All | Country codes (e.g., US, GB, CA) |
| `maxResults` | integer | ❌ No | 50 | Maximum ads to scrape (0 = unlimited) |
| `dateOption` | string | ❌ No | - | Date filter (last-30-days, current-year, etc.) |
| `startDate` | string | ❌ No | - | Custom start date (YYYY-MM-DD) |
| `endDate` | string | ❌ No | - | Custom end date (YYYY-MM-DD) |
| `proxyConfiguration` | object | ❌ No | - | Apify proxy settings |

### Input Schema Details

#### keyword
Array of keywords to search for in LinkedIn Ad Library.

**Example:**
```json
{
  "keyword": ["apify", "web scraping", "automation"]
}
```

#### accountOwner
Filter ads by specific advertiser names (optional).

**Example:**
```json
{
  "accountOwner": ["Apify", "Microsoft", "Google"]
}
```

#### countries
Array of country codes to filter ads by geographic targeting.

**Example:**
```json
{
  "countries": ["US", "GB", "CA", "AU"]
}
```

#### Date Filtering Options

**Predefined Options:**
- `last-30-days` - Last 30 days
- `current-month` - Current month
- `current-year` - Current year
- `last-year` - Previous year
- `custom-date-range` - Custom date range (requires startDate and endDate)

**Custom Date Range:**
```json
{
  "dateOption": "custom-date-range",
  "startDate": "2024-01-01",
  "endDate": "2024-12-31"
}
```

#### proxyConfiguration
Optional proxy settings for improved reliability:

```json
{
  "proxyConfiguration": {
    "useApifyProxy": true,
    "apifyProxyGroups": ["RESIDENTIAL"]
  }
}
```

---

## 📤 Output Structure

### Ad Data Format

```json
{
  "adId": "894398854",
  "advertiserName": "Apify",
  "advertiserBio": "CEO at Apify | Helping people get more value from the web",
  "isPromoted": true,
  "creativeType": "SPONSORED_STATUS_UPDATE",
  "headline": "$1M in prizes for tools AI needs. The frontier is open for builders.",
  "adText": "Companies deploying AI hit walls. Tools don't exist yet. That's your opportunity. Build on Apify, win your share.",
  "detailPageUrl": "https://www.linkedin.com/ad-library/detail/894398854",
  "imageUrl": "https://media.licdn.com/dms/image/v2/D4E10AQH0zRaEVZ_APg/...",
  "logoUrl": "https://media.licdn.com/dms/image/v2/D4E0BAQHAN7DZIQRKSw/...",
  "ctaText": "View details",
  "externalLinksInText": [],
  "scrapedAt": "2025-12-03T00:55:56.066Z"
}
```

### Output Fields Explained

#### Ad Identification
- `adId` - Unique LinkedIn ad identifier
- `advertiserName` - Company/advertiser name
- `advertiserBio` - Advertiser bio/subtitle text (e.g., job title, company description, headline)
- `isPromoted` - Whether ad is promoted
- `creativeType` - Type of ad creative (SPONSORED_STATUS_UPDATE, etc.)

#### Ad Content
- `headline` - Ad headline text
- `adText` - Main ad body text/description
- `ctaText` - Call-to-action button text

#### Visual Assets
- `imageUrl` - URL of ad creative image
- `logoUrl` - URL of advertiser's logo

#### Additional Data
- `detailPageUrl` - LinkedIn URL to ad detail page
- `externalLinksInText` - Array of external URLs found in ad text
- `scrapedAt` - ISO 8601 timestamp when ad was scraped

---

## 📊 Pre-Configured Data Views

### 1. 📢 Overview
Quick summary of all ads with essential information including advertiser bio.

**Fields:** Ad image, ID, advertiser, bio, creative type, headline, ad text, CTA, LinkedIn URL

**Use Case:** Initial ad review and filtering

### 2. 📋 Detailed View
Complete ad information including external links, timestamps, and advertiser bio.

**Fields:** All ad data including images, logos, bio, external links, scrape timestamps

**Use Case:** In-depth ad analysis and competitive research

### 3. 🏢 Advertisers
Ads grouped by advertiser/company with bio information.

**Fields:** Logo, company name, bio, ad ID, headline, creative type, URL

**Use Case:** Track specific advertisers, competitor monitoring

### 4. 🎨 Creative Analysis
Focus on ad creatives and content including advertiser bio.

**Fields:** Creative image, ID, advertiser, bio, type, headline, body text, CTA

**Use Case:** Creative inspiration, copywriting research, A/B testing ideas

---

## 💡 Use Cases

### 📊 Competitive Intelligence
- **Competitor Ad Tracking** - Monitor competitor ad campaigns and positioning
- **Creative Research** - Analyze successful ad creatives and messaging
- **Messaging Analysis** - Study competitor positioning, bio descriptions, and messaging
- **Budget Estimation** - Track ad volume and frequency by advertiser

### 🎯 Marketing & Advertising
- **Creative Inspiration** - Find ideas for your own campaigns
- **Copy Research** - Study effective headlines and ad text
- **Bio & Description Ideas** - Analyze how advertisers describe themselves
- **CTA Analysis** - Discover compelling call-to-action approaches
- **Trend Monitoring** - Track advertising trends in your industry

### 📈 Market Research
- **Industry Analysis** - Study advertising patterns by sector
- **Brand Monitoring** - Track brand advertising activity and positioning
- **Geographic Insights** - Analyze regional advertising differences
- **Seasonal Patterns** - Identify seasonal advertising trends
- **Advertiser Positioning** - Understand how companies present themselves

### 🤖 Automation & Integration
- **Ad Monitoring Alerts** - Get notified of new competitor ads
- **Database Building** - Create comprehensive ad databases with advertiser profiles
- **API Integration** - Feed data into your own systems
- **Reporting Automation** - Generate regular competitive reports

---

## ⚙️ Advanced Configuration

### Multi-Keyword Search

Search for multiple keywords in one run:

```json
{
  "keyword": [
    "web scraping",
    "data extraction",
    "api integration",
    "automation tools"
  ],
  "maxResults": 200
}
```

This will scrape up to 200 ads total across all keywords.

### Account Owner Filtering

Track specific advertisers:

```json
{
  "keyword": ["marketing automation"],
  "accountOwner": ["HubSpot", "Salesforce", "Marketo"],
  "maxResults": 0
}
```

### Date Range Analysis

Analyze ads from specific time periods:

```json
{
  "keyword": ["black friday"],
  "dateOption": "custom-date-range",
  "startDate": "2024-11-01",
  "endDate": "2024-11-30",
  "maxResults": 0
}
```

### Geographic Targeting

Focus on specific countries:

```json
{
  "keyword": ["fintech"],
  "countries": ["US", "GB", "SG", "HK"],
  "maxResults": 500
}
```

### Using Proxies for Scale

For reliable large-scale scraping:

```json
{
  "keyword": ["saas"],
  "maxResults": 1000,
  "proxyConfiguration": {
    "useApifyProxy": true,
    "apifyProxyGroups": ["RESIDENTIAL"]
  }
}
```

---

## 📈 Performance Metrics

### Scraping Speed
- **Single Ad** - ~0.2 seconds
- **Page (10 ads)** - ~2-3 seconds (including delay)
- **100 Ads** - ~30-40 seconds
- **1000 Ads** - ~5-7 minutes

### Resource Usage
- **Memory** - 256MB minimum, 2048MB maximum
- **CPU** - Low to moderate usage
- **Network** - Moderate bandwidth usage

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
- External links are extracted from ad text content
- Advertiser bio reflects current LinkedIn profile information

### Rate Limiting

- Built-in 2-second delays between pages
- Retry logic handles temporary failures
- Proxy usage recommended for large-scale operations
- LinkedIn may implement rate limiting

### Updates & Maintenance

LinkedIn may update their Ad Library structure. If the actor stops working:
1. Check for actor updates on Apify
2. Report issues via GitHub or Apify support
3. Monitor actor changelog for fixes

---

## 📚 Additional Resources

### Sample Searches

**General Keyword:**
```json
{
  "keyword": ["apify"],
  "maxResults": 50
}
```

**Competitor Research:**
```json
{
  "keyword": ["web scraping"],
  "accountOwner": ["ParseHub", "Octoparse", "Apify"],
  "dateOption": "last-30-days"
}
```

**Industry Analysis:**
```json
{
  "keyword": ["marketing automation", "crm software"],
  "countries": ["US", "GB", "CA"],
  "maxResults": 500
}
```

**Seasonal Campaign:**
```json
{
  "keyword": ["christmas", "holiday sale"],
  "dateOption": "custom-date-range",
  "startDate": "2024-12-01",
  "endDate": "2024-12-25"
}
```

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
