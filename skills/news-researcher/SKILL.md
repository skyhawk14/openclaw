---
name: news-researcher
description: "Research and aggregate news, trends, and insights across time ranges, regions, and source categories. Provides summaries or detailed analysis."
metadata: {"openclaw":{"emoji":"📰","requires":{"bins":[]}}}
---

# News Researcher

A comprehensive news and trend research skill that aggregates information across configurable time ranges, regions, and source categories. Provides both quick summaries and in-depth analysis.

## Configuration Parameters

```json
{
  "time_range": "24h | 7d | 30d",
  "region": "global | us | india | eu",
  "depth": "summary | detailed",
  "sources_preference": ["tech", "finance", "research", "politics", "science", "crypto", "ai", "startups"]
}
```

## Parameter Details

### Time Range
| Value | Description | Use Case |
|-------|-------------|----------|
| `24h` | Last 24 hours | Breaking news, daily updates |
| `7d` | Last 7 days | Weekly trends, emerging stories |
| `30d` | Last 30 days | Monthly analysis, trend patterns |

### Region
| Value | Coverage | Key Sources |
|-------|----------|-------------|
| `global` | Worldwide | Reuters, BBC, AP, Bloomberg |
| `us` | United States | NYT, WSJ, TechCrunch, CNBC |
| `india` | India | Economic Times, Mint, TOI, MoneyControl |
| `eu` | European Union | Financial Times, DW, Euronews |

### Depth
| Value | Output | Details |
|-------|--------|---------|
| `summary` | Quick overview | 2-3 sentences per topic, key headlines |
| `detailed` | In-depth analysis | Full context, multiple perspectives, data points |

### Source Categories
| Category | Focus Areas |
|----------|-------------|
| `tech` | Technology, software, hardware, internet |
| `finance` | Markets, stocks, economy, banking |
| `research` | Academic papers, studies, scientific discoveries |
| `politics` | Government, policy, elections, regulations |
| `science` | Scientific breakthroughs, space, environment |
| `crypto` | Cryptocurrency, blockchain, DeFi, Web3 |
| `ai` | Artificial intelligence, ML, LLMs, robotics |
| `startups` | Funding, acquisitions, new companies |

## Usage Examples

### Quick Tech News (24h)
```
User: "What's happening in tech today?"

Config:
{
  "time_range": "24h",
  "region": "global",
  "depth": "summary",
  "sources_preference": ["tech", "ai"]
}

Response:
📰 Tech News Summary (Last 24h)

🤖 AI & Machine Learning
• OpenAI announces GPT-5 preview with reasoning improvements
• Google DeepMind publishes new protein folding research

💻 Technology
• Apple releases iOS 19.3 with new AI features
• Microsoft Azure reports 40% cloud growth

🚀 Startups
• Stripe raises $2B at $95B valuation
• New AI coding startup raises $50M Series A
```

### Detailed Finance Analysis (7d, India)
```
User: "Give me a detailed finance report for India this week"

Config:
{
  "time_range": "7d",
  "region": "india",
  "depth": "detailed",
  "sources_preference": ["finance", "startups"]
}

Response:
📊 India Finance Report (Feb 26 - Feb 2, 2026)

## Market Overview
The BSE Sensex gained 2.3% this week, closing at 78,450...
[Detailed analysis with data points, expert quotes, sector breakdown]

## Key Developments
1. RBI Policy Decision...
2. FII/DII Activity...
3. Sector Performance...

## Startup Ecosystem
• Zerodha launches new mutual fund platform
• PhonePe reaches 500M users milestone
...
```

### Research Deep Dive (30d)
```
User: "What research papers have come out about LLMs this month?"

Config:
{
  "time_range": "30d",
  "region": "global",
  "depth": "detailed",
  "sources_preference": ["research", "ai"]
}
```

## Memory Storage

**Research Session (news_research_session):**
```json
{
  "last_query": "2026-02-02T15:00:00Z",
  "config": {
    "time_range": "24h",
    "region": "global",
    "depth": "summary",
    "sources_preference": ["tech", "finance"]
  },
  "cached_results": [...],
  "user_interests": ["AI", "startups", "crypto"]
}
```

**Saved Articles (news_saved_articles):**
```json
{
  "articles": [
    {
      "id": "art_001",
      "title": "OpenAI Announces GPT-5",
      "source": "TechCrunch",
      "url": "https://...",
      "saved_at": "2026-02-02T14:00:00Z",
      "tags": ["ai", "openai", "llm"]
    }
  ]
}
```

**User Preferences (news_user_preferences):**
```json
{
  "default_config": {
    "time_range": "24h",
    "region": "us",
    "depth": "summary",
    "sources_preference": ["tech", "ai", "startups"]
  },
  "blocked_sources": [],
  "priority_topics": ["AI", "funding rounds"],
  "alert_keywords": ["OpenAI", "funding", "acquisition"]
}
```

## Research Workflow

### Step 1: Parse User Request
```
Analyze user query to determine:
- Implicit time range (today, this week, recently)
- Geographic focus (mentioned countries/regions)
- Topic categories (tech, finance, etc.)
- Depth preference (quick update vs. deep dive)
```

### Step 2: Configure Research
```
Set config based on analysis:
{
  "time_range": detected_or_default,
  "region": detected_or_default,
  "depth": detected_or_default,
  "sources_preference": detected_categories
}
```

### Step 3: Gather Information
```
For each source category:
1. Search recent news/articles
2. Filter by time range
3. Filter by region relevance
4. Rank by importance/engagement
```

### Step 4: Synthesize Response
```
Based on depth setting:

SUMMARY:
- 2-3 bullet points per category
- Key headlines only
- No detailed analysis

DETAILED:
- Full context for each story
- Multiple source perspectives
- Data points and statistics
- Expert quotes when available
- Related developments
- Future implications
```

## Quick Commands

| Command | Config Applied |
|---------|----------------|
| "news today" | 24h, global, summary, [all] |
| "tech news" | 24h, global, summary, [tech, ai] |
| "finance update" | 24h, global, summary, [finance] |
| "crypto news this week" | 7d, global, summary, [crypto] |
| "detailed AI report" | 7d, global, detailed, [ai, research] |
| "india startup news" | 24h, india, summary, [startups, tech] |
| "US market analysis" | 7d, us, detailed, [finance] |
| "research papers on LLMs" | 30d, global, detailed, [research, ai] |

## Response Formatting

### Summary Format
```
📰 {Category} News ({time_range}, {region})

🔹 {Headline 1}
   Brief 1-2 sentence summary

🔹 {Headline 2}
   Brief 1-2 sentence summary

🔹 {Headline 3}
   Brief 1-2 sentence summary

📊 Quick Stats: {relevant metrics}
```

### Detailed Format
```
📰 {Category} Report
📅 {time_range} | 🌍 {region}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Executive Summary
{2-3 paragraph overview}

## Key Developments

### 1. {Major Story 1}
{Full analysis with context, data, quotes}
Sources: [Source1], [Source2]

### 2. {Major Story 2}
{Full analysis}

## Data & Metrics
{Relevant statistics, charts description}

## Expert Perspectives
{Quotes and analysis from industry experts}

## Looking Ahead
{Future implications and upcoming events}

---
Sources: {list of sources used}
Last Updated: {timestamp}
```

## Source Categories Detail

### Tech Sources
- TechCrunch, The Verge, Ars Technica, Wired
- Hacker News, Product Hunt, GitHub Trending
- Company blogs (Google, Microsoft, Apple, Meta)

### Finance Sources
- Bloomberg, Reuters, Financial Times, WSJ
- CNBC, MarketWatch, Yahoo Finance
- Regional: Economic Times, Mint (India), FT (EU)

### Research Sources
- arXiv, Google Scholar, Semantic Scholar
- Nature, Science, IEEE, ACM
- University press releases

### AI/ML Sources
- arXiv (cs.AI, cs.LG, cs.CL)
- AI blogs (OpenAI, Anthropic, Google AI, DeepMind)
- Papers With Code, Hugging Face blog

### Crypto Sources
- CoinDesk, The Block, Decrypt
- DeFi Llama, Dune Analytics
- Protocol blogs (Ethereum, Solana, etc.)

### Startup Sources
- Crunchbase News, PitchBook
- TechCrunch Startup, VentureBeat
- Regional startup media

## Alerts & Monitoring

**Set Up Alerts:**
```
User: "Alert me when there's news about OpenAI"

Stored in news_user_preferences.alert_keywords:
["OpenAI"]

When matching news found:
🔔 News Alert: OpenAI

{headline}
{brief summary}

📰 Read more or reply for details
```

**Daily Digest:**
```
User: "Send me a daily tech digest"

Stored as scheduled task:
- Time: 8:00 AM user timezone
- Config: 24h, global, summary, [tech, ai, startups]
- Delivery: configured channel
```

## Error Handling

| Scenario | Response |
|----------|----------|
| No news found | "No significant news found for {category} in the last {time_range}. Try expanding the time range or categories." |
| Region unavailable | "Limited coverage for {region}. Showing global results instead." |
| Source unavailable | "Some sources unavailable. Results may be incomplete." |

## Integration Tips

1. **Combine with other skills** - Use news data to inform Twitter posts, research tasks
2. **Cache results** - Store in memory to avoid repeated lookups
3. **Learn preferences** - Track which topics user engages with most
4. **Cross-reference** - Verify important news across multiple sources
5. **Time-aware** - Adjust for user's timezone and work hours
