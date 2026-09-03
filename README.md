# China Hot Trending MCP

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![MCP](https://img.shields.io/badge/MCP-Server-blue)](https://modelcontextprotocol.io)
[![Remote](https://img.shields.io/badge/Streamable%20HTTP-hosted%20free-success)](https://mcp.pianam.cn/hot-mcp/mcp)

Real-time hot-search & trending boards from **8 major Chinese platforms in one MCP call** — Weibo, Zhihu, Bilibili, Baidu, Toutiao, Douyin, Tieba and Juejin.

- **Try it in 30 seconds**: a free public MCP endpoint is already running — just paste the URL into your MCP client (no install, no API key).
- **Or self-host**: a single Python file, stdlib HTTP + FastMCP, zero paid dependencies.

## ⚡ Use the hosted endpoint (no setup)

```
https://mcp.pianam.cn/hot-mcp/mcp
```

Transport: **Streamable HTTP** (MCP 2025-03-26 compatible). No authentication required.

## 🔌 Client configuration

Add this to your MCP client's `mcpServers` configuration (Claude Desktop `claude_desktop_config.json`, Cursor `mcp.json`, Cline, Cherry Studio, etc.):

```json
{
  "mcpServers": {
    "hot-trending": {
      "type": "http",
      "url": "https://mcp.pianam.cn/hot-mcp/mcp"
    }
  }
}
```

> Clients that do not accept `"type": "http"` (some Cherry Studio / older
> Cline versions) accept the same entry with just `"url"`.

## 🧰 Tools

| Tool | Parameters | Returns |
|---|---|---|
| `query_hot_trending(platform="all", limit=20)` | `platform`: one of `weibo` / `zhihu` / `bilibili` / `baidu` / `toutiao` / `douyin` / `tieba` / `juejin`, or **`all`** (default) — returns the top 10 of every board at once.<br>`limit`: items per board for single-platform queries (1–50, default 20). | Each item: `rank`, `title`, `hot` (platform-specific popularity value), `url`. With `all` you get a `platforms` map plus a `failed` map (any degraded source never breaks the others). |
| `list_platforms()` | — | Returns the 8 supported platform slugs with Chinese and English display names. |

## 📡 Data sources, caching & limits

- **Weibo** `weibo.com/ajax/side/hotSearch`, **Zhihu** `api.zhihu.com/topstory`, **Bilibili** `api.bilibili.com/x/web-interface/popular`, **Baidu** `top.baidu.com/api/board`, **Toutiao** `toutiao.com/hot-event/hot-board`, **Douyin** `iesdouyin.com/web/api/v2/hotsearch/billboard/word`, **Tieba** `tieba.baidu.com/hottopic`, **Juejin** `api.juejin.cn/content_api/v1/article_rank`.
- All data comes from each platform's own **public** web/API endpoints — **no API key, no login, no scraping of private data**.
- Implementation inspired by the open-source [DailyHotApi](https://github.com/imsyy/DailyHotApi) project (MIT) with a thin stdlib-only implementation.
- Results are **cached in memory for 300 seconds**; a failed source degrades gracefully.
- The hosted endpoint is rate-limited to **60 requests / minute / IP** (HTTP 429 beyond that).

## 🐢 Self-hosting

```bash
git clone https://github.com/boy-373/hot-trending-mcp.git
cd hot-trending-mcp
pip install -r requirements.txt
python hot_mcp_server.py
# the server listens on 127.0.0.1:8007 by default; override with:
#   MCP_HOST=0.0.0.0 MCP_PORT=9000 python hot_mcp_server.py
#   MCP_ALLOWED_HOSTS="your-domain.com,127.0.0.1:*"
#   MCP_ALLOWED_ORIGINS="https://your-domain.com"
```

Then point your MCP client at `http://127.0.0.1:8007/mcp`.
No API keys or accounts are ever required.


- `platform_names.json` — Chinese/English display names of the 8 platforms (kept outside the code file so the source stays pure-ASCII).

## 🗂️ Files

- `hot_mcp_server.py` — the MCP server (FastMCP, Streamable HTTP transport).
- `rate_limit.py` — lightweight per-IP sliding-window rate-limit middleware (60 req/min default).
- `requirements.txt` — `mcp`, `uvicorn`, `starlette`.
- `server.json` — official MCP Registry manifest (remote server entry, ready to publish with `mcp-publisher`).
- `smithery.yaml` / `glama.json` — directory listing metadata.

---

## 🇨🇳 中文使用说明

**一句话**：**8 大中文平台热榜，一次调用全拿到**：微博热搜、知乎热榜、B站热门、百度热搜、头条热榜、抖音热点、贴吧热议、掘金热榜。数据全部来自各平台官方公开接口，无需任何 API Key 或登录；服务端缓存 5 分钟，单 IP 限流 60 次/分钟。

**在线直连地址（免费、无需 Key、开箱即用）**：`https://mcp.pianam.cn/hot-mcp/mcp`

在 MCP 客户端（Claude Desktop / Cursor / Cherry Studio / Cline 等）的配置里加入：

```json
{
  "mcpServers": {
    "hot-trending": {
      "type": "http",
      "url": "https://mcp.pianam.cn/hot-mcp/mcp"
    }
  }
}
```

**工具**：

- `query_hot_trending(platform, limit)`：一次调用拿 8 大平台热榜。`platform` 传 `all`（默认）返回每个平台 Top10；也可传单个平台 slug（weibo/zhihu/bilibili/baidu/toutiao/douyin/tieba/juejin），`limit` 控制条数（1-50）。
- `list_platforms()`：列出支持的 8 个平台及中英文名。
- 返回字段：排名 `rank`、标题 `title`、热度值 `hot`、原文链接 `url`；`all` 模式还会返回 `failed` 字段标注暂时失败的平台，不影响其他平台。

**服务特性**：数据源全部为公开接口、无需注册/付费；服务端内存缓存、失败自动降级/切换备用通道；单 IP 限流 60 次/分钟。

**本地部署**：

```bash
git clone https://github.com/boy-373/hot-trending-mcp.git
cd hot-trending-mcp
pip install -r requirements.txt
python hot_mcp_server.py
# 默认监听 127.0.0.1:8007，可用环境变量 MCP_HOST / MCP_PORT / MCP_ALLOWED_HOSTS / MCP_ALLOWED_ORIGINS 覆盖
```

## 📄 License

[MIT](LICENSE) © 2026 boy-373

## Install via Smithery

One-click install for [Smithery](https://smithery.ai)-supported clients (Claude Desktop, Cursor, etc.):

[![Smithery](https://smithery.ai/badge/1561852680/hot-trending-mcp)](https://smithery.ai/servers/1561852680/hot-trending-mcp)

Or run:

```bash
npx -y @smithery/cli install 1561852680/hot-trending-mcp --client claude
```
