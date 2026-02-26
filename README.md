# Cian Parser — Real Estate Automation Tool

> **Intelligent web scraping and monitoring for the Russian real estate market**

![Status](https://img.shields.io/badge/Status-Production-success?style=flat-square)
![Python](https://img.shields.io/badge/Python-3.9+-blue?style=flat-square&logo=python)
![License](https://img.shields.io/badge/License-Commercial-orange?style=flat-square)

---

## Overview

**Cian Parser** is a production-grade web scraping and automation tool designed for the Russian real estate platform **Cian.ru**. It enables users to monitor property listings in real-time, apply advanced filters, and receive instant notifications when new listings match their criteria.

### Key Features

- **Multi-threaded scraping** — Fast, concurrent data collection
- **Anti-detection mechanisms** — Proxies, user-agent rotation, request delays
- **Advanced filtering** — Price, location, property type, rooms, area
- **Real-time notifications** — Telegram bot integration for instant alerts
- **Data analytics** — Price history, market trends, duplicate detection
- **Scheduling** — Automatic periodic scraping with APScheduler

---

## Demo

**Watch the tool in action:**

[Demo Video](media/demo.mp4) — See real-time scraping, filtering, and Telegram notifications

**Screenshots:**

- Dashboard with active listings
- Telegram notification example
- Filter configuration interface
- Analytics and price trends

---

## Architecture

```
┌────────────────────────────────────────────┐
│         Cian Parser Application            │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │   Scraper Engine                     │ │
│  │   • Multi-threaded requests          │ │
│  │   • Anti-detection (proxies, UA)     │ │
│  │   • HTML parsing (BeautifulSoup)     │ │
│  │   • Rate limiting                    │ │
│  └────────────┬─────────────────────────┘ │
│               │                            │
│               ▼                            │
│  ┌──────────────────────────────────────┐ │
│  │   Data Processor                     │ │
│  │   • Deduplication                    │ │
│  │   • Price normalization              │ │
│  │   • Location geocoding               │ │
│  │   • History tracking                 │ │
│  └────────────┬─────────────────────────┘ │
│               │                            │
│               ▼                            │
│  ┌──────────────────────────────────────┐ │
│  │   Filter Engine                      │ │
│  │   • Price range                      │ │
│  │   • Location (districts, metro)      │ │
│  │   • Property specs (rooms, area)     │ │
│  │   • Custom rules                     │ │
│  └────────────┬─────────────────────────┘ │
│               │                            │
│               ▼                            │
│  ┌──────────────────────────────────────┐ │
│  │   Notification System                │ │
│  │   • Telegram Bot API                 │ │
│  │   • Email notifications (SMTP)       │ │
│  │   • Formatted messages               │ │
│  │   • Media attachments                │ │
│  └──────────────────────────────────────┘ │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │   Scheduler (APScheduler)            │ │
│  │   • Cron-like scheduling             │ │
│  │   • Background job management        │ │
│  └──────────────────────────────────────┘ │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │   Storage (SQLite)                   │ │
│  │   • Listings database                │ │
│  │   • Price history                    │ │
│  │   • User preferences                 │ │
│  └──────────────────────────────────────┘ │
└────────────────────────────────────────────┘
```

---

## Tech Stack

### Core Technologies

- **Python 3.9+** — Main programming language
- **aiohttp** — Async HTTP client for concurrent requests
- **BeautifulSoup4** — HTML parsing and data extraction
- **lxml** — Fast XML/HTML parser
- **APScheduler** — Background task scheduling

### Data & Storage

- **SQLite** — Local database for listings and history
- **pandas** — Data analysis and manipulation
- **SQLAlchemy** — ORM for database operations

### Notifications

- **python-telegram-bot** — Telegram Bot API wrapper
- **smtplib** — Email notifications

### Anti-Detection

- **fake-useragent** — Random user-agent generation
- **requests** — HTTP library with proxy support
- **time.sleep()** — Request throttling

---

## Features Deep Dive

### 1. Multi-Threaded Scraping

**Problem:** Sequential scraping is too slow for real-time monitoring

**Solution:** Concurrent requests with `ThreadPoolExecutor`

```python
from concurrent.futures import ThreadPoolExecutor

def scrape_pages(urls, max_workers=5):
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        results = executor.map(fetch_page, urls)
    return list(results)
```

**Benefits:**
- 5-10x faster than sequential scraping
- Efficient resource utilization
- Configurable concurrency level

---

### 2. Anti-Detection Mechanisms

**Challenge:** Cian.ru has bot detection and rate limiting

**Techniques implemented:**

#### User-Agent Rotation
```python
from fake_useragent import UserAgent

ua = UserAgent()
headers = {'User-Agent': ua.random}
```

#### Proxy Support
```python
proxies = {
    'http': 'http://proxy.example.com:8080',
    'https': 'https://proxy.example.com:8080'
}
response = requests.get(url, proxies=proxies)
```

#### Request Delays
```python
import time
import random

time.sleep(random.uniform(2, 5))  # Random delay 2-5 seconds
```

#### Session Management
- Cookie persistence across requests
- Headers that mimic real browsers
- Referer header management

---

### 3. Advanced Filtering

**Supported filters:**

- **Price:** Min/max range, price per square meter
- **Location:** City districts, metro stations, distance from landmarks
- **Property Type:** Apartment, studio, room, house
- **Rooms:** Number of rooms (1, 2, 3, 4+)
- **Area:** Living area, kitchen area, total area
- **Floor:** Floor number, total floors, not first/last
- **Amenities:** Parking, balcony, elevator, renovation
- **Listing Age:** New today, this week, this month

**Example configuration:**
```json
{
  "price": {
    "min": 3000000,
    "max": 5000000
  },
  "location": {
    "districts": ["Центральный", "Кировский"],
    "metro_stations": ["Площадь Ленина", "Гагаринская"],
    "max_metro_distance": 15
  },
  "property": {
    "type": "apartment",
    "rooms": [1, 2],
    "area": {
      "min": 35,
      "max": 60
    },
    "floor": {
      "not_first": true,
      "not_last": true
    }
  },
  "amenities": {
    "parking": true,
    "balcony": true
  }
}
```

---

### 4. Real-Time Notifications

#### Telegram Bot Integration

**Features:**
- Instant notifications for new listings
- Rich message formatting (property details, photos)
- Interactive buttons (view listing, save, ignore)
- Command interface for configuration

**Example notification:**

```
🏠 New Listing Alert!

📍 Location: Центральный район, м. Площадь Ленина (5 мин)
💰 Price: 4,200,000 ₽ (120,000 ₽/м²)
📐 Area: 35 м² (1 room)
🏢 Floor: 5/10

🔗 View: https://cian.ru/sale/...

✅ Matches your filters: Price, Location, Area
```

#### Email Notifications

- Daily/weekly digest of new listings
- HTML formatted emails with images
- Customizable templates

---

### 5. Data Analytics

**Features:**

- **Price History Tracking** — Monitor price changes for listings
- **Market Trends** — Average prices by district, property type
- **Duplicate Detection** — Identify re-posted listings
- **Time-on-Market Analysis** — How long listings stay active

**Visualizations:**
- Price distribution histograms
- Price trends over time (line charts)
- Heatmaps of listings by district

---

### 6. Scheduling

**Use case:** Automatic periodic scraping without manual intervention

**Configuration:**
```python
from apscheduler.schedulers.background import BackgroundScheduler

scheduler = BackgroundScheduler()

# Scrape every hour
scheduler.add_job(scrape_and_notify, 'interval', hours=1)

# Daily analytics report at 9 AM
scheduler.add_job(send_daily_report, 'cron', hour=9)

scheduler.start()
```

---

## Use Cases

### 1. First-Time Home Buyer

**Scenario:** Looking for a 1-2 room apartment in central districts, budget 3-5M RUB

**Setup:**
- Configure filters (price, location, rooms)
- Enable Telegram notifications
- Schedule hourly scraping

**Outcome:**
- Instant alerts for new listings
- No more manual browsing
- First to contact landlords

---

### 2. Real Estate Investor

**Scenario:** Monitoring undervalued properties for investment

**Setup:**
- Track price history for all listings
- Set up alerts for price drops >10%
- Analyze market trends by district

**Outcome:**
- Identify investment opportunities
- Data-driven decisions
- Portfolio optimization

---

### 3. Real Estate Agent

**Scenario:** Staying updated on market inventory for clients

**Setup:**
- Multiple filter profiles for different clients
- Daily digest emails
- Export listings to CRM

**Outcome:**
- Better client service
- Faster response times
- Competitive advantage

---

## Project Status

**Stage:** Production  
**Client Use:** Active (commissioned project)  
**Maintenance:** Ongoing

### What's Working
- ✅ Full scraping pipeline (multi-threaded)
- ✅ Advanced filtering system
- ✅ Telegram notifications
- ✅ Price history tracking
- ✅ Anti-detection mechanisms
- ✅ Scheduling (APScheduler)

### Planned Improvements
- 📋 Web dashboard (Flask/FastAPI)
- 📋 Machine learning price predictions
- 📋 Multi-platform support (Avito, Domofond)

---

## Configuration Example

**filters.json:**
```json
{
  "name": "My Search",
  "active": true,
  "filters": {
    "price": {"min": 3000000, "max": 5000000},
    "location": {
      "districts": ["Центральный", "Кировский"],
      "metro_stations": ["Площадь Ленина"],
      "max_metro_distance": 10
    },
    "property": {
      "type": "apartment",
      "rooms": [1, 2],
      "area": {"min": 30, "max": 60}
    }
  },
  "notifications": {
    "telegram": true,
    "email": false
  },
  "schedule": {
    "enabled": true,
    "interval": "1h"
  }
}
```

See [examples/](examples/) for more configuration examples.

---

## Technical Highlights

### Performance Optimizations

1. **Concurrent requests** — 5-10x faster than sequential
2. **Efficient parsing** — lxml (faster than html.parser)
3. **Database indexing** — Fast duplicate detection
4. **Caching** — Reduce redundant API calls

### Scalability

- **Horizontal scaling** — Run multiple instances with different filters
- **Database optimization** — Indexed queries, connection pooling
- **Resource limits** — Configurable max workers, memory limits

### Error Handling

- **Retry logic** — Exponential backoff for failed requests
- **Logging** — Detailed logs for debugging (rotating file handler)
- **Graceful degradation** — Continue on partial failures

---

## Why Commercial?

This project represents significant expertise in:
- **Web scraping** — Anti-detection, performance optimization
- **Data processing** — Normalization, deduplication, analytics
- **Automation** — Scheduling, notifications, reliability
- **Production deployment** — Error handling, monitoring, maintenance

**Note:** This showcase demonstrates architectural decisions and technical capabilities. The actual code is proprietary and was developed for a commercial client.

---

## About the Creator

Built by [CREATMAN](https://github.com/CreatmanCEO) — Full-Stack Developer specializing in automation, web scraping, and data processing.

**Other Projects:**
- [GHOST](https://github.com/CreatmanCEO/ghost-showcase) — AI Assistant with Invisible Overlay
- [ACCU](https://github.com/CreatmanCEO/accu) — AI-Curated Code Universe
- [smart-link-collector](https://github.com/CreatmanCEO/smart-link-collector) — AI Link Organizer Bot

---

## License

**Commercial Project** — All rights reserved.

This showcase repository is for demonstration and portfolio purposes. The actual application code is proprietary.

For business inquiries: creatmanick@gmail.com

---

## Connect

- **Website:** [creatman.site](https://creatman.site)
- **Telegram:** [@Creatman_it](https://t.me/Creatman_it)
- **Email:** creatmanick@gmail.com
- **GitHub:** [@CreatmanCEO](https://github.com/CreatmanCEO)

---

<sub>Built with Python · BeautifulSoup · aiohttp · Telegram Bot API</sub>
