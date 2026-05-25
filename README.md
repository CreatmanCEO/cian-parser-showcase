# Cian Parser — Showcase

[![License](https://img.shields.io/badge/license-Commercial-orange.svg)](LICENSE)
[![Stars](https://img.shields.io/github/stars/CreatmanCEO/cian-parser-showcase?style=flat)](https://github.com/CreatmanCEO/cian-parser-showcase/stargazers)
[![Validate](https://github.com/CreatmanCEO/cian-parser-showcase/actions/workflows/validate.yml/badge.svg)](https://github.com/CreatmanCEO/cian-parser-showcase/actions/workflows/validate.yml)
![Status](https://img.shields.io/badge/status-showcase-blue)
![Platform](https://img.shields.io/badge/platform-Python-3776ab?logo=python&logoColor=white)

[Русская версия](README.ru.md)

> **This is a documentation-only showcase. The source code of Cian Parser is proprietary and is not included in this repository.**

Cian Parser is a commercial automation tool for the Russian real-estate platform [Cian.ru](https://cian.ru). It monitors listings on a schedule, applies user filters, and pushes new matches to Telegram. This repo demonstrates the product surface — feature scope, example configuration, screenshots — for portfolio and client-evaluation purposes.

## Why this repo exists

Prospective clients ask "what does it actually do, and how is it configured?". This showcase answers that without exposing the working scraper, anti-detection logic, or proxy infrastructure — those remain proprietary.

## How it works (high level)

1. **Scheduler** triggers periodic scrapes (APScheduler).
2. **Fetcher** pulls Cian search-result pages through a rotating proxy pool with user-agent randomization and request pacing.
3. **Parser** extracts listings into a normalized schema (price, area, rooms, location, posted-at, contact).
4. **Filter engine** applies user-defined rules (see [`examples/filters.example.json`](examples/filters.example.json)).
5. **Deduper** compares against a persistent listing store to skip already-seen ads.
6. **Notifier** pushes matches to Telegram with photos and a deep link.
7. **Analytics** keeps price history per listing for trend reporting.

## Tech stack

| Layer | Tools |
|---|---|
| Language | Python 3.9+ |
| Scheduling | APScheduler |
| HTTP | requests / httpx with proxy rotation |
| Parsing | lxml / BeautifulSoup |
| Storage | SQLite / PostgreSQL |
| Notifications | Telegram Bot API |
| Deployment | systemd / Docker (client choice) |

## Features

- Scheduled scraping with configurable intervals (every N minutes, specific hours, or cron expressions)
- Rotating proxy pool with automatic health checks and failover
- User-agent randomization and request pacing to avoid detection
- Telegram notifications with listing photos, price, area, floor, and a direct link
- Price history tracking per listing for trend analysis
- Duplicate detection across runs — only new or updated listings trigger alerts
- Filter engine supporting nested boolean logic (AND/OR/NOT)
- Multi-user support — each user maintains independent filters and notification channels

## Filter options

Filters are defined in JSON and support the following fields:

| Field | Type | Example |
|---|---|---|
| `price_min` / `price_max` | integer | `30000` / `80000` |
| `rooms` | list | `[1, 2]` |
| `area_min` / `area_max` | float | `35.0` / `90.0` |
| `floor_min` / `floor_max` | integer | `2` / `25` |
| `not_first_floor` | boolean | `true` |
| `not_last_floor` | boolean | `true` |
| `districts` | list | `["Presnensky", "Tverskoy"]` |
| `metro_stations` | list | `["Barrikadnaya"]` |
| `max_km_from_metro` | float | `1.5` |
| `keywords_exclude` | list | `["auction", "commercial"]` |

See [`examples/filters.example.json`](examples/filters.example.json) for a complete sample.

## Alert configuration

Telegram alerts are configured per user:

```json
{
  "telegram_chat_id": "123456789",
  "notify_on": "new_listing",
  "quiet_hours": { "from": "23:00", "to": "07:00" },
  "batch_interval_minutes": 15,
  "include_photos": true,
  "max_photos_per_listing": 3
}
```

Alerts can be batched to avoid notification spam during high-volume periods.

## Architecture overview

```
APScheduler (cron)
      |
  Fetcher (httpx + proxy pool)
      |
  HTML Parser (lxml / BS4)
      |
  Filter Engine --> Deduper (SQLite/PG)
      |
  Notifier (Telegram Bot API)
      |
  Analytics (price history store)
```

## Examples

- [`examples/filters.example.json`](examples/filters.example.json) — filter configuration shape

## Limitations (showcase, not the product)

- This repository contains **no runnable code**. Cloning it will not give you a working scraper.
- Screenshots and demo media live in `media/` (when present) and may lag behind the live product.
- Filter examples are illustrative — the production schema is broader and changes with client requirements.
- Cian's HTML/anti-bot surface changes frequently; the production tool is maintained against those changes, but no public guarantees are made here.

## Inquiries

For commercial inquiries, similar projects, or licensing of the underlying tool: **creatmanick@gmail.com** · [creatman.site](https://creatman.site).

EOF
