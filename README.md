# Bilibili Scraper 哔哩哔哩 (No Cookie) — Videos, Danmaku, Comments & Creators API

[![Run on Apify Store](https://img.shields.io/badge/🚀_Run_on_Apify_Store-bilibili--scraper-00A1D6?style=for-the-badge)](https://apify.com/axon-studio/bilibili-scraper)
[![No Login Required](https://img.shields.io/badge/100%25_No_Cookie-WBI_%2B_Buvid3_Signed-success?style=for-the-badge)](https://apify.com/axon-studio/bilibili-scraper)
[![MCP Server Ready](https://img.shields.io/badge/Claude_MCP_%26_n8n-Ready-blueviolet?style=for-the-badge)](https://apify.com/axon-studio/bilibili-scraper)

> ⚡ **Try it live in your browser (Free $5/month quota — up to 1,111 Bilibili items free):**
> 👉 **[Click here to run Bilibili Scraper on Apify Store (`axon-studio/bilibili-scraper`)](https://apify.com/axon-studio/bilibili-scraper)** — No Bilibili account, no `SESSDATA` cookie, no proxies, and no local setup required. Export directly to **JSON, CSV, Excel, Parquet**, or call via **Python, JavaScript, REST API, n8n, Make, and Claude MCP**.

---

**[Bilibili Scraper 哔哩哔哩 (No Cookie) — Videos, Danmaku, Comments & Creators](https://apify.com/axon-studio/bilibili-scraper)** (`axon-studio/bilibili-scraper`) is an industrial-grade, zero-login data extraction tool for **Bilibili (bilibili.com / B站)**, China's premier Gen-Z video, ACG, tech review, and creator community. Powered by an automatic `buvid3`/`buvid4` anonymous fingerprint generator and a pure-Python **WBI dynamic signature engine**, it extracts keyword search videos, site-wide Top 100 rankings & popular feeds, real-time Top 30 hot searches, full video triple-engagement metrics (`views`, `likesCount`, `coinsCount`, `favoritesCount`, `sharesCount`, `coinPerViewRatio`, `engagementRate`), XML **danmaku (弹幕)** bullet comments with peak-minute density sampling, user comments with Chinese/English IP province geolocation, and UP creator profiles without requiring cookies or accounts.

---

## What Can This Bilibili Scraper Do? (Features)

### 8 Dedicated Bilibili Extraction Modes in One API
- **1. Search Videos (`search`)**: Scrape Bilibili videos by keyword with full support for all 6 native sort orders (`totalrank`, `click`, `pubdate`, `dm`, `stow`, `scores`), 5 video duration filters (`0`–`4`), publication date range filtering (`dateStart` / `dateEnd`), automatic multi-sort & bilingual alias rotation to break pagination ceilings, `bvid` deduplication, and automatic `/x/web-interface/view` enrichment so every video includes real `likesCount`, `coinsCount`, `favoritesCount`, `sharesCount`, `cid`, and `category`.
- **2. Popular & Top 100 Ranking Videos (`popular`)**: First captures Bilibili's all-station Top 100 leaderboard (`/x/web-interface/ranking/v2` with `rank` 1–100), and when `maxResults > 100`, seamlessly paginates the Popular stream (`/x/web-interface/popular`) to fill your quota.
- **3. Bilibili Hot Search Top 30 (`hot_search`)**: Extract the live Bilibili Hot Search leaderboard (`rank`, `title`, `keyword`, `hotValue`, `label`) for instant trend spotting.
- **4. Video Details & Triple-Engagement Metrics (`video_details`)**: Accept BV IDs (`BV1r94y1H77U`), av numbers, short links (`b23.tv`), or full URLs and return deep engagement telemetry including **One-Click Triple Combo (一键三连)** metrics (`likesCount`, `coinsCount`, `favoritesCount`, `sharesCount`) plus exclusive derived KPIs: **`coinPerViewRatio`** and **`engagementRate`**.
- **5. Video Comments & IP Province Geolocation (`video_comments`)**: Extract top and latest comments (`commentId`, `text`, `likesCount`, `repliesCount`, `authorLevel`, `isPinned`, `upLiked`) along with standardized Chinese & English **IP geolocation** (`regionName: "广东"` → `regionEn: "Guangdong"`).
- **6. Danmaku Bullet Comments XML & Peak Minute Sampling (`danmaku`)**: Parse Bilibili's native XML bullet streams (`list.so`) with optional keyword filtering (`danmakuKeyword`) or density-ranked sampling across high-energy peak minutes and the full video timeline (sorted by `videoSecond` ascending, avoiding opening `00:00` spam).
- **7. Creator Uploaded Videos (`user_videos`)**: Triple-engine anti-`-352` risk-control architecture combined with concurrent `/x/web-interface/view` enrichment that retrieves all uploaded videos and complete 7-metric engagement stats from any UP creator (`mid` or `space.bilibili.com/{mid}`) without logging in.
- **8. Creator Profile & Followers (`user_profile`)**: Extract UP creator follower count (`followersCount`), following count, total archive count (`videosCount`), cumulative likes (`likesCount`), official verification title (`officialVerify`), VIP status, and account level.

### Deep-Enrichment Switches (Inline Danmaku + Cascade Comments & Creator Profiles)
- **Inline Danmaku Enrichment (`scrapeDanmaku`)**: When enabled in video modes (`search`, `popular`, `user_videos`, `video_details`), calculates the highest-density bullet minute **`peakMinute`** (e.g. `"02:00-02:59"`) and attaches up to `maxDanmakuPerVideo` representative bullet strings **`danmakuSamples`** directly onto the video record at **zero extra row cost**.
- **Cascade Comments (`scrapeComments`)**: Automatically scrape the top `N` comments (with IP geolocation) for every video discovered.
- **Cascade Creator Profiles (`includeCreatorProfiles`)**: Automatically enrich every unique UP creator discovered with their full follower & total-likes card profile.

---

## Quick Start: Run on Apify Console or via API

1. **[Open Bilibili Scraper on Apify Store](https://apify.com/axon-studio/bilibili-scraper)** and click **Try for free**.
2. **Select a Mode & Enter Targets**:
   - For `search`: enter keywords like `["黑神话悟空"]` or `["Black Myth Wukong"]` (with `autoLocalize: true` enabled by default).
   - For `video_details`, `video_comments`, or `danmaku`: enter BV IDs or URLs such as `["BV1r94y1H77U"]`.
   - For `user_videos` or `user_profile`: enter creator MIDs or space URLs such as `["946974"]` (影视飓风 MediaStorm).
3. **Click "Start"** and export your dataset in **JSON, CSV, Excel, or Parquet**.

### Python SDK (`apify-client`)

```bash
pip install apify-client
```

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_API_TOKEN")

run_input = {
    "mode": "search",
    "searchQuery": ["Black Myth Wukong"],
    "searchOrder": "click",
    "maxResults": 30,
    "scrapeDanmaku": True,
    "maxDanmakuPerVideo": 10,
    "scrapeComments": True,
    "maxCommentsPerVideo": 5,
}

run = client.actor("axon-studio/bilibili-scraper").call(run_input=run_input)

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item["mode"], item.get("bvid"), item.get("title") or item.get("text"), item.get("coinsCount"))
```

### JavaScript / TypeScript SDK (`apify-client`)

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_API_TOKEN' });

const run = await client.actor('axon-studio/bilibili-scraper').call({
    mode: 'search',
    searchQuery: ['黑神话悟空'],
    searchOrder: 'click',
    maxResults: 30,
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

### One-Line cURL REST API

```bash
curl -X POST "https://api.apify.com/v2/acts/axon-studio~bilibili-scraper/run-sync-get-dataset-items?token=YOUR_APIFY_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"mode":"search","searchQuery":["黑神话悟空"],"maxResults":10}'
```

---

## Input Parameters Reference

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `mode` | `string` | `"search"` | One of `"search"`, `"popular"`, `"hot_search"`, `"video_details"`, `"video_comments"`, `"danmaku"`, `"user_videos"`, `"user_profile"`. |
| `maxResults` | `integer` | `30` | Maximum primary records to collect (`1`–`10000`). Cascade add-on rows do not consume this quota. |
| `searchQuery` | `array` | `["黑神话悟空"]` | Keywords for `search` mode (or discovery fallback for `video_details`/`video_comments`/`danmaku`). |
| `searchOrder` | `string` | `"totalrank"` | `"totalrank"` (Relevance), `"click"` (Most views), `"pubdate"` (Newest), `"dm"` (Most danmaku), `"stow"` (Most favorites), `"scores"` (Most comments). |
| `durationFilter` | `string` | `"0"` | `"0"` (Any), `"1"` (<10 min), `"2"` (10–30 min), `"3"` (30–60 min), `"4"` (>60 min). |
| `dateStart` / `dateEnd` | `string` | `""` | Filter videos by publication date (`YYYY-MM-DD`). |
| `autoLocalize` | `boolean` | `true` | Automatically expands English queries into Chinese equivalents. |
| `videoIds` | `array` | `["BV1r94y1H77U"]` | BV IDs, av numbers, or video URLs for `video_details`, `video_comments`, and `danmaku`. |
| `userIds` | `array` | `["946974"]` | Creator numeric MIDs or `space.bilibili.com/{mid}` URLs for `user_videos` and `user_profile`. |
| `danmakuKeyword` | `string` | `""` | Optional keyword filter for standalone `danmaku` mode. |
| `scrapeComments` | `boolean` | `false` | Cascade switch: scrape top `maxCommentsPerVideo` comments for every video. |
| `scrapeDanmaku` | `boolean` | `false` | Inline enrichment switch: attach `peakMinute` and `danmakuSamples` (`list[str]`) directly onto each video row for free. |
| `includeCreatorProfiles` | `boolean` | `false` | Cascade switch: scrape 1 `user_profile` row per unique UP creator discovered. |
| `deltaMode` | `boolean` | `false` | Deduplicate across scheduled runs using persistent Key-Value Store (`bilibili-scraper-delta`). |

---

## Sample Output (JSON)

### 1. Video Item (`search` / `popular` / `video_details` / `user_videos`)

```json
{
    "mode": "search",
    "bvid": "BV1r94y1H77U",
    "aid": 1130123456,
    "cid": 1612345678,
    "title": "《黑神话：悟空》最终预告 | 8月20日，重走西游",
    "text": "实机录制，包含全新战斗演示与关卡首领。",
    "description": "实机录制，包含全新战斗演示与关卡首领。",
    "authorName": "黑神话之悟空",
    "authorId": "642389251",
    "views": 18520000,
    "likesCount": 1920000,
    "coinsCount": 1680000,
    "favoritesCount": 1150000,
    "sharesCount": 640000,
    "danmakuCount": 312000,
    "commentsCount": 148000,
    "coinPerViewRatio": 0.0907,
    "engagementRate": 0.291,
    "duration": 296,
    "videoTimeFormatted": "04:56",
    "category": "单机游戏",
    "tags": ["黑神话悟空", "国产游戏", "动作游戏"],
    "peakMinute": "02:00-02:59",
    "danmakuSamples": [
        "[02:05] 前方高能！四大天王压迫感绝了！",
        "[02:18] 这演出水准真是国产3A天花板"
    ],
    "videoUrl": "https://www.bilibili.com/video/BV1r94y1H77U",
    "authorUrl": "https://space.bilibili.com/642389251",
    "createdAtIso": "2024-08-08T02:00:00Z"
}
```

### 2. Comment Item with IP Geolocation (`video_comments`)

```json
{
    "mode": "video_comments",
    "commentId": "238910456789",
    "bvid": "BV1r94y1H77U",
    "text": "真正国产3A神作，美术和打击感太硬核了，直接一键三连支持！",
    "authorName": "天命人_0820",
    "authorId": "38192011",
    "authorLevel": 6,
    "likesCount": 15420,
    "repliesCount": 312,
    "regionName": "广东",
    "regionEn": "Guangdong",
    "isPinned": true,
    "upLiked": true,
    "videoUrl": "https://www.bilibili.com/video/BV1r94y1H77U",
    "createdAtIso": "2024-08-20T04:15:12Z"
}
```

---

## 🔗 Full Chinese Social Media Intelligence Suite (on Apify Store)

- 📺 **[Bilibili Scraper — Videos, Danmaku, Comments & Creators](https://apify.com/axon-studio/bilibili-scraper)**
- 🌐 **[Sina Weibo Scraper — Search, Hot Trends, Comments & KOLs (No Cookie)](https://apify.com/axon-studio/weibo-scraper)**
- ⚖️ **[BlackCat Consumer Complaints Scraper (新浪黑猫投诉 API)](https://apify.com/axon-studio/blackcat-complaints-scraper)**
- 👤 **[Weibo User Posts & Creator Profile Scraper (100% No Cookie)](https://apify.com/axon-studio/weibo-user-posts-scraper)**
- 💬 **[Weibo Comments & IP Province Geolocation Scraper](https://apify.com/axon-studio/weibo-comments-scraper)**

👉 **[Launch Bilibili Scraper on Apify Now (Free Trial Included)](https://apify.com/axon-studio/bilibili-scraper)**
