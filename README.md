
## TradingView News Flow API

This project demonstrates how to fetch and display real-time financial news for selected symbols using the TradingView News Flow Beta API. The API provides streaming news updates filtered by language and symbol, making it ideal for financial dashboards, trading platforms, or news aggregators.

---

## **Features**

- Fetches news filtered by language (`en`) and multiple financial symbols (e.g., GOLD, SPX, XAUUSD).
- Supports real-time streaming of news updates.
- Parses and displays key information: title, source, publication time, related symbols, and links to full articles.
- Easily extensible for additional symbols or languages.

---

## **API Endpoint**

```
GET https://news-mediator.tradingview.com/news-flow/v1/news
```



### **Query Parameters**

| Parameter | Required | Example Value(s) | Description |
| :-- | :-- | :-- | :-- |
| filter | Yes | `lang:en`, `symbol:TVC:GOLD,OANDA:XAUUSD,SP:SPX` | Used to filter news by language, symbol, and possibly other criteria. Can be repeated for multiple filters. |
| streaming | No | `true` | If set to `true`, the API streams news updates in real time. |


---


### **filter Parameter Details**

#### 1. **lang**

- **Usage:** `filter=lang:en`
- **Purpose:** Restricts news articles to a specific language.
- **Supported Values:**
    - `en` (English)
    - Other ISO language codes may be supported (e.g., `ru` for Russian, `es` for Spanish), depending on API capabilities.


#### 2. **symbol**

- **Usage:** `filter=symbol:TVC:GOLD,OANDA:XAUUSD,SP:SPX`
- **Purpose:** Filters news to only include articles related to specific financial symbols.
- **Format:**
    - Comma-separated list of symbols.
    - Each symbol is in the format: `EXCHANGE:SYMBOL` (e.g., `TVC:GOLD`, `OANDA:XAUUSD`).


**Example:**

```
https://news-mediator.tradingview.com/news-flow/v1/news?filter=lang%3Aen&filter=symbol%3ACAPITALCOM%3AGOLD%2CMCX%3AGOLD1!%2CNCDEX%3AGOLD%2COANDA%3AXAUUSD%2CSP%3ASPX%2CTVC%3AGOLD%2CVELOCITY%3AGOLD&streaming=true
```

---


## **Sample API Response**

```json
{
  "items": [
    {
      "id": "DJN_DN20250609001516:0",
      "title": "Robinhood and AppLovin Stocks Fall. They Missed Out on Joining the S&P 500. — Barrons.com",
      "provider": "dow-jones",
      "sourceLogoId": "dow-jones-newswires",
      "published": 1749463680,
      "source": "Dow Jones Newswires",
      "urgency": 2,
      "permission": "provider",
      "relatedSymbols": [
        {
          "symbol": "CBOE:SPX",
          "logoid": "indices/s-and-p-500"
        },
        {
          "symbol": "NASDAQ:APP",
          "logoid": "applovin"
        },
        {
          "symbol": "NYSE:LNG",
          "logoid": "cheniere-energy-partners"
        },
        {
          "symbol": "NASDAQ:IBKR",
          "logoid": "interactive-brokers-group"
        },
        {
          "symbol": "NASDAQ:HOOD",
          "logoid": "robinhood"
        }
      ],
      "storyPath": "/news/DJN_DN20250609001516:0/"
    },
    {
      "id": "moneycontrol:91759bbeb094b:0",
      "title": "Gold rate today: Yellow metal gets cheaper; check the latest prices in your city on June 9",
      "provider": "moneycontrol",
      "sourceLogoId": "moneycontrol",
      "published": 1749456698,
      "source": "Moneycontrol",
      "urgency": 2,
      "link": "https://www.moneycontrol.com/news/business/commodities/gold-rate-today-yellow-metal-gets-cheaper-check-the-latest-prices-in-your-city-on-june-9-13105321.html",
      "relatedSymbols": [
        {
          "symbol": "TVC:GOLD",
          "logoid": "metal/gold"
        }
      ],
      "storyPath": "/news/moneycontrol:91759bbeb094b:0-gold-rate-today-yellow-metal-gets-cheaper-check-the-latest-prices-in-your-city-on-june-9/"
    }
  ]
}
```

## **Response Fields**

| Field | Type | Description |
| :-- | :-- | :-- |
| id | string | Unique identifier for the news item |
| title | string | Headline of the news article |
| provider | string | News provider (e.g., "dow-jones", "moneycontrol") |
| sourceLogoId | string | Identifier for the provider's logo |
| published | integer | Publication timestamp (UNIX epoch seconds) |
| source | string | Source of the news |
| urgency | integer | Urgency level (higher means more urgent) |
| permission | string | Permission level for displaying the news |
| link | string | (Optional) Direct link to the full article |
| relatedSymbols | array | List of related financial symbols (each with `symbol` and `logoid`) |
| storyPath | string | Path to the story within the TradingView ecosystem |


---

## **Usage Example**

```python
import requests
from datetime import datetime

def fetch_tradingview_news():
    url = "https://news-mediator.tradingview.com/news-flow/v1/news"
    params = {
        "filter": [
            "lang:en",
            "symbol:CAPITALCOM:GOLD,MCX:GOLD1!,NCDEX:GOLD,OANDA:XAUUSD,SP:SPX,TVC:GOLD,VELOCITY:GOLD"
        ],
        "streaming": "false"  # Set to "true" for streaming, "false" for a single snapshot
    }

    response = requests.get(url, params=params)
    response.raise_for_status()
    news_items = response.json().get("items", [])

    for item in news_items:
        published_dt = datetime.utcfromtimestamp(item["published"])
        related = ", ".join([s["symbol"] for s in item.get("relatedSymbols", [])])
        link = item.get("link") or f"https://www.tradingview.com{item.get('storyPath','')}"
        print(f"📰 {item['title']}")
        print(f"    Source: {item['source']} | Published: {published_dt.strftime('%Y-%m-%d %H:%M:%S UTC')}")
        print(f"    Related Symbols: {related}")
        print(f"    Read more: {link}\n")

if __name__ == "__main__":
    fetch_tradingview_news()

```

### **Run the python Script**

Open your terminal or command prompt, navigate to the folder where you saved `tradingview_news.py`, and run:

```bash
python tradingview_news.py
```

You should see neatly formatted news items printed in your terminal, each with:

- Title
- Source
- Published date/time (UTC)
- Related symbols
- Link to the full article

## **Troubleshooting**

If you see `ModuleNotFoundError: No module named 'requests'`, make sure you installed `requests` with `pip install requests`.
If TradingView changes its API or restricts access, the script may need updates.

---

## **Notes**

- **Streaming:** If `streaming=true`, use a streaming-capable HTTP client to process updates as they arrive.
- **Authentication:** The API may require authentication or may be subject to CORS or rate limits. Check your integration environment.
- **Extending:** Add or remove symbols in the `filter=symbol:` parameter to tailor the news feed to your needs.

---
