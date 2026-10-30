<p align="center">
  <a href="https://www.piloterr.com"><img src="images/banner.png" alt="Mascus Scraper API by Piloterr – scrape Mascus data as JSON" width="100%"></a>
</p>

# Mascus Scraper API

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/CFj3ph8aHU) [![Get API key](https://img.shields.io/badge/Piloterr-Get%20a%20free%20API%20key-111827)](https://app.piloterr.com) [![API docs](https://img.shields.io/badge/Docs-OpenAPI-0EA5E9)](https://app.piloterr.com/openapi) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

The **Piloterr Mascus Scraper API** lets you extract public data from Mascus as **clean, structured JSON** — no proxies, headless browsers or HTML parsing to maintain. Endpoints available in production:

- **Mascus Ad API** — Get full details for a Mascus used equipment listing: specs, price, seller and description.
- **Mascus Search API** — Search used equipment listings on Mascus from a category URL, with pagination that keeps country and category filters.

Use it for listing monitoring, price benchmarks, stock sourcing or market analytics on second-hand and vehicle marketplaces.

| Endpoint | Path | Credits | What you get |
|---|---|---|---|
| [Mascus Ad API](#mascus-ad-api) | `/v2/mascus/ad` | 1 | Get full details for a Mascus used equipment listing |
| [Mascus Search API](#mascus-search-api) | `/v2/mascus/search` | 1 | Search used equipment listings on Mascus from a category URL, with pagination that keeps country and category filters. |

## Table of contents

- [How it works](#how-it-works)
- [Mascus Ad API](#mascus-ad-api)
- [Mascus Search API](#mascus-search-api)
- [Code examples in other languages](#code-examples-in-other-languages)
- [Practical use cases](#practical-use-cases)
- [Why Piloterr?](#why-piloterr)
- [FAQ](#faq)
- [Related scrapers](#related-scrapers)
- [Support](#support)

## How it works

1. **Create a free Piloterr account** and copy your API key from the [dashboard](https://app.piloterr.com).
2. **Send a `GET` (or `POST`) request** to `https://api.piloterr.com/v2/...` with the header `x-api-key: YOUR_API_KEY`.
3. **Receive structured JSON** — Piloterr handles rendering, proxies, retries and parsing.

```bash
export PILOTERR_API_KEY="your-api-key"
pip install requests        # Python
# TypeScript: Node.js 18+ (native fetch), run with `npx tsx file.ts`
```

> Every endpoint accepts both `GET` (query string) and `POST` (JSON body with the same fields).

## Mascus Ad API

Get full details for a Mascus used equipment listing: specs, price, seller and description.

`GET | POST https://api.piloterr.com/v2/mascus/ad` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Full Mascus listing URL. Usually taken from `results[].url` in search. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/mascus/ad",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "https://www.mascus.fr/travaux-publics/broyeur-dechets/doppstadt-ak-435-profi/hocmgmfi.html",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type MascusAd = {
  brand?: string;
  model?: string;
  meta_data?: {
    meta_title?: string;
  };
  catalog_name?: string;
  company_city?: string;
  company_name?: string;
  user_currency?: string;
  company_country?: string;
  asset_details_info?: {
    label?: string;
    value?: string;
  }[];
  asset_pricing_info?: {
    label?: string;
    value?: string;
  }[];
  price_in_user_currency?: number;
};

const params = new URLSearchParams({
  "query": "https://www.mascus.fr/travaux-publics/broyeur-dechets/doppstadt-ak-435-profi/hocmgmfi.html",
});

const response = await fetch(`https://api.piloterr.com/v2/mascus/ad?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as MascusAd;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/mascus/ad
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "https://www.mascus.fr/travaux-publics/broyeur-dechets/doppstadt-ak-435-profi/hocmgmfi.html"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "brand": "Doppstadt",
  "model": "AK 435 Profi",
  "meta_data": {
    "meta_title": "Doppstadt AK 435 Profi, 2012, France - Mascus France"
  },
  "catalog_name": "construction",
  "company_city": "saint-genis-laval",
  "company_name": "RHONE ENVIRONNEMENT",
  "user_currency": "EUR",
  "company_country": "FR",
  "asset_details_info": [
    {
      "label": "Année",
      "value": "2012"
    }
  ],
  "asset_pricing_info": [
    {
      "label": "Prix",
      "value": "108 000 EUR"
    }
  ],
  "price_in_user_currency": 108000
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `brand` | string |  |
| `model` | string |  |
| `meta_data` | object |  |
| `meta_data.meta_title` | string |  |
| `catalog_name` | string |  |
| `company_city` | string |  |
| `company_name` | string |  |
| `user_currency` | string |  |
| `company_country` | string |  |
| `asset_details_info[]` | object[] |  |
| `asset_details_info[].label` | string |  |
| `asset_details_info[].value` | string |  |
| `asset_pricing_info[]` | object[] |  |
| `asset_pricing_info[].label` | string |  |
| `asset_pricing_info[].value` | string |  |
| `price_in_user_currency` | integer |  |

## Mascus Search API

Search used equipment listings on Mascus from a category URL, with pagination that keeps country and category filters.

`GET | POST https://api.piloterr.com/v2/mascus/search` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Full Mascus category or search URL. The country site comes from the host (`mascus.be`, `mascus.nl`, `mascus.de`, `mascus.com`, …). |
| `page` | number | — | Page number (default: `1`). About 40 results per page. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/mascus/search",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "https://www.mascus.be/bouw/graafmachines/be,country.html",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type MascusSearch = {
  results?: {
    url?: string;
    brand?: string;
    model?: string;
    image_url?: string;
    rental_ad?: boolean;
    auction_ad?: boolean;
    price_euro?: number;
    product_id?: string;
    catalog_name?: string;
    company_name?: string;
    category_name?: string;
    location_city?: string;
    user_currency?: string;
    year_of_manufacture?: number;
    location_country_code?: string;
    price_in_user_currency?: number;
  }[];
  pagination?: {
    next?: number;
    current?: number;
    page_size?: number;
    total_pages?: number;
    has_next_page?: boolean;
    total_results?: number;
  };
};

const params = new URLSearchParams({
  "query": "https://www.mascus.be/bouw/graafmachines/be,country.html",
});

const response = await fetch(`https://api.piloterr.com/v2/mascus/search?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as MascusSearch;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/mascus/search
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "https://www.mascus.be/bouw/graafmachines/be,country.html"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "results": [
    {
      "url": "https://www.mascus.be/bouw/minigraafmachines---7t/airman-ax10u-7-ax10u-7/ff3ch5ch.html",
      "brand": "Airman",
      "model": "AX10U-7",
      "image_url": "https://dnge9sb91helb.cloudfront.net/image/product/medium/example.jpg",
      "rental_ad": false,
      "auction_ad": false,
      "price_euro": 0,
      "product_id": "{example}",
      "catalog_name": "construction",
      "company_name": "Example Dealer",
      "category_name": "miniexcavators",
      "location_city": "Oostrozebeke",
      "user_currency": "EUR",
      "year_of_manufacture": 2020,
      "location_country_code": "BE",
      "price_in_user_currency": 0
    }
  ],
  "pagination": {
    "next": 2,
    "current": 1,
    "page_size": 40,
    "total_pages": 14,
    "has_next_page": true,
    "total_results": 547
  }
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `results[]` | object[] |  |
| `results[].url` | string |  |
| `results[].brand` | string |  |
| `results[].model` | string |  |
| `results[].image_url` | string |  |
| `results[].rental_ad` | boolean |  |
| `results[].auction_ad` | boolean |  |
| `results[].price_euro` | integer |  |
| `results[].product_id` | string |  |
| `results[].catalog_name` | string |  |
| `results[].company_name` | string |  |
| `results[].category_name` | string |  |
| `results[].location_city` | string |  |
| `results[].user_currency` | string |  |
| `results[].year_of_manufacture` | integer |  |
| `results[].location_country_code` | string |  |
| `results[].price_in_user_currency` | integer |  |
| `pagination` | object |  |
| `pagination.next` | integer |  |
| `pagination.current` | integer |  |
| `pagination.page_size` | integer |  |
| `pagination.total_pages` | integer |  |
| `pagination.has_next_page` | boolean |  |
| `pagination.total_results` | integer |  |

## Code examples in other languages

Ready-to-run examples live in the [`examples/`](examples) folder. Each one reads your key from the `PILOTERR_API_KEY` environment variable.

| Language | Ad | Search |
|---|---|---|
| cURL (Bash) | [`ad`](examples/curl/ad.sh) | [`search`](examples/curl/search.sh) |
| PHP | [`ad`](examples/php/ad.php) | [`search`](examples/php/search.php) |
| C# | [`ad`](examples/csharp/Ad.cs) | [`search`](examples/csharp/Search.cs) |
| Go | [`ad`](examples/go/ad.go) | [`search`](examples/go/search.go) |
| Java | [`ad`](examples/java/AdExample.java) | [`search`](examples/java/SearchExample.java) |
| Ruby | [`ad`](examples/ruby/ad.rb) | [`search`](examples/ruby/search.rb) |
| Node.js | [`ad`](examples/nodejs/ad.mjs) | [`search`](examples/nodejs/search.mjs) |

## Practical use cases

- **Listing monitoring** — Get alerted when new ads matching your criteria are published.
- **Price benchmarks** — Compute market prices per model, category or location.
- **Sourcing** — Find stock, vehicles, parts or equipment faster than manual browsing.
- **Market analytics** — Measure supply, turnover and pricing trends over time.
- **Data products** — Feed valuation models and dashboards with structured listings.

## Why Piloterr?

- **No infrastructure** — no proxies, headless browsers or CAPTCHA solving to run yourself.
- **Structured JSON** — stable, documented fields instead of brittle HTML selectors.
- **Pay per request** — transparent credit cost per endpoint, failed requests are retried for you.
- **One API key, hundreds of sources** — Mascus plus many other e-commerce, search and company data endpoints in the [Piloterr library](https://www.piloterr.com/library).
- **Any language** — plain HTTPS + JSON, with examples for Python, TypeScript, cURL, PHP, C#, Go, Java, Ruby and Node.js.

## FAQ

### Is it legal to scrape Mascus?

Collecting publicly available data is generally allowed in many jurisdictions, but rules depend on your country, the data you collect and how you use it. Avoid personal data unless you have a lawful basis, respect the website's terms where they apply, and get legal advice for your specific use case. This is not legal advice.

### Do I need proxies or a headless browser?

No. Piloterr handles rendering, proxy rotation and retries on its side. You only send an HTTPS request with your `x-api-key` header and receive JSON.

### How much does a request cost?

Credits per call: `/v2/mascus/ad` **1**, `/v2/mascus/search` **1**. See the [Piloterr website](https://www.piloterr.com) for plans.

### What data does the Mascus API return?

Each endpoint returns structured JSON: **Mascus Ad** — `brand`, `model`, `meta_data`, `meta_data.meta_title`, `catalog_name`, `company_city`; **Mascus Search** — `results[]`, `results[].url`, `results[].brand`, `results[].model`, `results[].image_url`, `results[].rental_ad`.

### How do I paginate Mascus results?

Use the `page` parameter and keep requesting while the response indicates more results.

### Which languages are supported?

Any language that can make an HTTP request. This README shows Python and TypeScript; the [`examples/`](examples) folder contains cURL, PHP, C#, Go, Java, Ruby and Node.js.

## Related scrapers

- [AutoScout24 Scraper API](https://github.com/piloterr/autoscout24-scraper)
- [Autotrader Scraper API](https://github.com/piloterr/autotrader-scraper)
- [Copart Scraper API](https://github.com/piloterr/copart-scraper)
- [Kijiji Scraper API](https://github.com/piloterr/kijiji-scraper)
- [Leboncoin Scraper API](https://github.com/piloterr/leboncoin-scraper)
- [mobile.de Scraper API](https://github.com/piloterr/mobile-de-scraper)
- [All Piloterr scrapers on GitHub](https://github.com/piloterr)

## Support

- 💬 Join the [Piloterr Discord](https://discord.gg/CFj3ph8aHU) to ask questions and share what you build.
- 📚 Browse the [API reference (OpenAPI)](https://app.piloterr.com/openapi) and the [Piloterr library](https://www.piloterr.com/library).
- 🔑 [Get your API key](https://app.piloterr.com).

---

<p align="center">
  <a href="https://www.piloterr.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="images/logo-dark.svg">
      <img src="images/logo.svg" alt="Piloterr" height="32">
    </picture>
  </a>
  <br>
  <sub>Built by <a href="https://www.piloterr.com">Piloterr</a> — web scraping APIs for e-commerce, search, real estate and more, one API key.</sub>
</p>

<sub>Mascus is a trademark of its respective owner. This repository is not affiliated with or endorsed by Mascus. Examples are released under the MIT license.</sub>
