# 🛍️ SHEIN Web Scraper

[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Architecture](https://img.shields.io/badge/Architecture-Multi--threaded-8A2BE2)](#-architecture)
[![Browser](https://img.shields.io/badge/Browser-undetected--chromedriver-4285F4?logo=googlechrome&logoColor=white)](#-features)
[![AI](https://img.shields.io/badge/AI-Google%20Gemini%20Vision-4285F4?logo=googlegemini&logoColor=white)](#-features)
[![Output](https://img.shields.io/badge/Output-JSON-6FBF73)](#-outputs)
[![Target](https://img.shields.io/badge/Target-SHEIN%20US-blueviolet)](#)
[![License](https://img.shields.io/badge/License-Personal%20%2F%20Educational-green)](#-license)

A production-style, Python-based web scraper that automates the collection of rich product data (names, prices, descriptions, discounts, sizes, and more) from **SHEIN US** product pages. It combines `undetected-chromedriver`, Playwright stealth techniques, persistent Chrome profiles, and **Google Gemini Vision AI** to reliably detect and solve CAPTCHA / verification challenges while scraping at scale with a thread-safe browser pool.

---

## ✨ Project Highlights

| Highlight | Description |
|-----------|-------------|
| ✅ **AI-powered CAPTCHA solving** | Gemini Vision analyzes screenshots and returns structured click / drag / refresh actions executed via CDP |
| ✅ **Concurrent BrowserPool** | Up to **3** reusable Chrome instances scrape in parallel (`num_browsers` configurable) |
| ✅ **Thread-safe architecture** | Shared work queue, browser tracking, and JSON writes are guarded with locks |
| ✅ **Automatic browser recovery** | Dead browsers are detected and recreated automatically |
| ✅ **Persistent Chrome profiles** | Authenticated sessions (cookies) are reused across runs via `ChromeProfile/` |
| ✅ **Duplicate URL detection** | Already-scraped URLs are skipped before and during the run |
| ✅ **Incremental JSON saving** | Products are appended to `Outputs/products.json` as they are scraped — no data loss on crash |
| ✅ **10,000+ product scalable design** | Queue-driven workers scale to thousands of URLs without launching a browser per URL |
| ✅ **Gemini API key rotation** | Multiple keys can be configured and rotated automatically on quota exhaustion |

---

## 📦 Repository Statistics

| Attribute | Value |
|-----------|-------|
| Language | Python (≥ 3.8) |
| Architecture | Multi-threaded (BrowserPool workers) |
| Browser Engine | `undetected-chromedriver` |
| AI / Vision | Google Gemini (Vision + Text) |
| Output Format | JSON (`Outputs/products.json`) |
| Target Website | SHEIN US |
| Concurrency | 3 Browsers (configurable) |
| Dependencies | `requirements.txt` |

---

## 🏗️ Project Structure

```
shein_web_scraper_testing/
├── main.py                  # Core execution engine (single-URL scraping pipeline)
├── run_pipeline.py          # End-to-end orchestrator: Discovery → Cleaning → Scraping
├── Shein.py                 # Main Shein scraper class (page loading, parsing, CAPTCHA solving)
├── browser_pool.py          # Thread-safe pool of reusable Chrome instances for concurrency
├── browser_manager.py       # Simple single-browser lifecycle manager
├── Gemini.py                # Google Gemini API wrapper (Vision CAPTCHA solving, marketing text)
├── config.py                # Centralized verification/challenge handling configuration
├── category_config.py       # SHEIN main category names and URLs
├── user_selection.py        # Interactive category selection for discovery phase
├── setup_session.py         # Manual session warmup (solve CAPTCHAs by hand, save session)
├── product_utils.py         # Filesystem-safe product name normalization
├── urls_utils.py            # URL preprocessing helpers (clean, dedupe, sort)
├── urls_input_file_adder.py # Standalone URL cleaning/deduplication utility
├── Logger.py                # Dual-channel logger (terminal + log file)
├── requirements.txt         # Python dependencies
├── .env                     # Environment variables (GEMINI_API_KEY) — not committed
├── .gitignore
├── Inputs/
│   ├── urls.txt             # Product URLs to scrape (one per line)
│   └── urls-backup.txt      # Backup copy of urls.txt
└── Outputs/
    ├── products.json        # Accumulated scraped product data
    └── .staging/            # Temporary staging area during runs
```

> **Note:** `_replace_method.py` and `_verify_method.py` appear in some editor tabs / older README revisions but are **not** part of the current implementation and are **not** required.

---

## 🔧 Requirements

- **Python** ≥ 3.8
- **Google Chrome** installed (for `undetected-chromedriver`)
- A **Google Gemini API key** for CAPTCHA solving (from [Google AI Studio](https://aistudio.google.com/))

### Main Python dependencies

| Category | Packages |
|----------|----------|
| Browser automation | `undetected-chromedriver`, `selenium`, `playwright` + `playwright-stealth` |
| Parsing | `beautifulsoup4`, `lxml` |
| AI / Vision | `google-genai` |
| Image / CAPTCHA support | `opencv-python`, `pillow`, `PyAutoGUI` |
| Utilities | `colorama`, `python-dotenv`, `tqdm`, `requests` |

Install everything with:

```bash
pip install -r requirements.txt
```

---

## ⚙️ Setup & Configuration

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure the `.env` file

Create a `.env` file in the project root with your Gemini API key(s).

**Single key:**

```
GEMINI_API_KEY=your_gemini_api_key_here
```

**Multiple keys (auto key rotation on quota exhaustion):**

```
GEMINI_API_KEY=OwnerA:KEY_A,OwnerB:KEY_B,OwnerC:KEY_C
```

> 💡 **Tip:** Legacy plain comma-separated keys are also supported and will be auto-named (`key_1`, `key_2`, ...).

**Optional environment variables:**

| Variable | Description |
|----------|-------------|
| `CHROME_EXECUTABLE_PATH` | Path to a custom Chrome executable |
| `CHROME_PROFILE_PATH` | Path to an existing Chrome profile (for authenticated sessions) |
| `HEADLESS` | Set `true` to run headless (default `false`) |
| `BROWSER_PROXY` | Optional `--proxy-server` value for all browser instances |

### 3. Prepare your Chrome profile (recommended)

The scraper works best with an authenticated Chrome profile. Run the manual session setup once:

```bash
python setup_session.py
```

Solve any CAPTCHAs manually in the opened browser window, then press **ENTER** once the product page loads. The session (cookies) is saved to the `ChromeProfile/` directory and reused by the automated scraper.

---

## 🚀 Usage

### Option A – Full automated pipeline (recommended)

Run the end-to-end orchestrator, which handles all 3 phases:

```bash
python run_pipeline.py
```

You will be prompted to select SHEIN categories to scrape. The pipeline then:

1. **Phase 1 – Discovery**: Navigates category pages with `undetected-chromedriver`, dismisses cookie popups, solves verification challenges with Gemini, and harvests product URLs into `Inputs/urls.txt`.
2. **Phase 2 – Cleaning**: Deduplicates and sanitizes the discovered URLs.
3. **Phase 3 – Scraping**: Launches `main.py` to scrape every URL with 3 concurrent browsers and save results.

**Pipeline options:**

```bash
python run_pipeline.py --target 1000          # Stop discovery after 1000 URLs
python run_pipeline.py --out Inputs/urls.txt  # Custom output file for URLs
```

### Option B – Scrape a fixed list of URLs

1. Put product URLs into `Inputs/urls.txt` (one per line, `#` lines are ignored).
2. Run the core engine:

```bash
python main.py
```

**Main options:**

```bash
python main.py --verbose      # Enable verbose debug output
python main.py --target 10    # Scrape at most 10 URLs (0 = unlimited)
```

### Option C – Clean your URL list

```bash
python urls_input_file_adder.py
```

---

## 📊 Results

The scraper has been **successfully tested** and continuously saves products into `Outputs/products.json`.

**Successfully tested with:**

- ✅ 10 URLs
- ✅ 100 URLs
- ✅ 500 URLs
- ✅ 1000+ URLs

**Verified behaviors:**

- ✅ BrowserPool concurrency (3 parallel Chrome instances)
- ✅ CAPTCHA solving (auto via Gemini + manual fallback)
- ✅ Duplicate prevention (URL de-dup before and during run)
- ✅ Incremental JSON saving (products appended immediately)
- ✅ Retry mechanism (failed URLs requeued / retried)
- ✅ Browser recovery (dead browsers recreated automatically)
- ✅ URL filtering (comments, blank lines, dash prefixes, dedupe, sort)

> 💡 **Important:** Products are appended **incrementally** as they are scraped — not at the end of execution. This means you can monitor `Outputs/products.json` growing in real time and never lose already-scraped data if a run is interrupted.

---

## ✅ Output Verification

After scraping, verify the output with:

```bash
python -c "import json; print(len(json.load(open('Outputs/products.json', encoding='utf-8'))))"
```

This prints the number of product records accumulated in `Outputs/products.json`.

You can also pretty-print the first record to inspect its structure:

```bash
python -c "import json; d=json.load(open('Outputs/products.json', encoding='utf-8')); print(json.dumps(d[0], indent=2, ensure_ascii=False))"
```

---

## 🧠 How It Works

### Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                         run_pipeline.py                              │
│   Discovery (category pages) → Cleaning (urls_utils) → Scraping      │
└──────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────┐
│                        BrowserPool (3 threads)                       │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐     │
│  │ Browser #1 │  │ Browser #2 │  │ Browser #3 │  │   ... (n)   │     │
│  └────────────┘  └────────────┘  └────────────┘  └────────────┘     │
│        │               │               │                            │
│        ▼               ▼               ▼                            │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │          Shared Thread-Safe URL Queue (locks)                │    │
│  └─────────────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────┐
│   Shein.py  →  load page  →  CAPTCHA?  →  Gemini Vision  →  parse    │
│                                    │                                  │
│                                    ▼                                  │
│              main.py  →  incremental save to Outputs/products.json    │
└──────────────────────────────────────────────────────────────────────┘
```

### CAPTCHA / Verification solving

1. The page is loaded with anti-detection flags (Chrome profile, disabled automation indicators, etc.).
2. Cookie consent popups are dismissed via aggressive CSS/JS injection (no API call needed).
3. A screenshot is captured and sent to **Gemini Vision**, which analyzes the page and returns a structured JSON action (click coordinates, drag target, refresh, etc.).
4. The action is executed via **CDP (Chrome DevTools Protocol)** mouse events, and the page is polled until verification is cleared.
5. If a browser keeps getting blocked, it is marked `BLOCKED` with a cooldown, and the URL is requeued for another browser in the pool.

### Browser pool concurrency

- `BrowserPool` starts **3** Chrome instances (configurable via `num_browsers`).
- Each browser runs in its own worker thread pulling URLs from a shared queue.
- Browsers are tracked as `INITIALIZING`, `AVAILABLE`, `SCRAPING`, `BLOCKED`, or `STOPPED`.
- Dead browsers are automatically recreated; blocked browsers are released after a cooldown.

### Price extraction

Prices are extracted primarily from embedded JSON (`promotionInfoPrice` / `originalPrice`), with HTML selector fallbacks and a computational fallback that derives the old price from the current price + discount percentage. Brazilian currency format (`R$2.299,08`) is normalized into integer/decimal parts.

---

## 🏛️ Design Decisions

| Decision | Why |
|----------|-----|
| **BrowserPool instead of one browser per URL** | Launching a browser per URL is extremely slow and resource-heavy. A pool of reusable instances amortizes startup cost, keeps memory bounded, and enables real parallelism. |
| **Persistent Chrome profiles** | Anti-bot systems flag fresh, cookie-less sessions. A warm profile with established cookies dramatically reduces CAPTCHA frequency and keeps sessions authenticated across runs. |
| **Gemini Vision instead of plain OCR** | CAPTCHAs are semantic, not just textual. A vision-language model can understand checkboxes, slide puzzles, image grids, and cookie popups, returning actionable structured actions rather than raw text. |
| **Incremental JSON saving** | Long scraping runs risk data loss on crashes. Appending each product immediately means already-scraped results are never lost and the output can be monitored live. |
| **Thread-safe shared queue** | Multiple workers need one consistent source of work. A lock-protected queue guarantees each URL is consumed exactly once and prevents race conditions. |
| **Browser cooldown / BLOCKED state** | When a browser gets blocked by anti-bot detection, hammering it makes things worse. A cooldown lets it recover while other browsers continue, keeping overall throughput high. |

---

## 🧩 Assignment Mapping

| Requirement | Implementation | Status |
|-------------|----------------|--------|
| Product scraping | `Shein.py` + `main.py` extract name, SKU, prices, discount, description, sizes, reviews | ✅ |
| Concurrent browsers | `BrowserPool` manages 3 parallel Chrome workers | ✅ |
| CAPTCHA handling | Gemini Vision + CDP actions with manual fallback (`setup_session.py`) | ✅ |
| Structured JSON output | `Outputs/products.json` with typed fields + structured attributes | ✅ |
| Scalable architecture | Queue-driven workers scale from 10 to 10,000+ URLs | ✅ |
| Documentation | This README + module-level docstrings | ✅ |

---

## 💻 Sample Console Output

A realistic view of a BrowserPool run:

```
========================================================================
        SHEIN PRODUCT SCRAPER - BrowserPool RUN
========================================================================
[2026-01-15 10:02:11] Initializing Browser #1 ... OK
[2026-01-15 10:02:12] Initializing Browser #2 ... OK
[2026-01-15 10:02:14] Initializing Browser #3 ... OK
[2026-01-15 10:02:15] Pool ready. 3 browsers AVAILABLE.
[2026-01-15 10:02:15] Loaded 1000 URLs from Inputs/urls.txt
[2026-01-15 10:02:16] Browser #1 -> SCRAPING https://us.shein.com/.../p-412530044.html
[2026-01-15 10:02:16] Browser #2 -> SCRAPING https://us.shein.com/.../p-441167503.html
[2026-01-15 10:02:17] Browser #3 -> SCRAPING https://us.shein.com/.../p-163439827.html
[2026-01-15 10:02:21] Browser #2: CAPTCHA detected! Sending screenshot to Gemini Vision...
[2026-01-15 10:02:23] Browser #2: Action 'click' executed at (480, 320). Polling...
[2026-01-15 10:02:25] Browser #2: Verification cleared. Resuming scraping.
[2026-01-15 10:02:27] Browser #1: Extracted 'Adjustable USB Personal Mini Fan' (price 4.76, -24%)
[2026-01-15 10:02:27] Browser #1: Saved to Outputs/products.json (1 total)
[2026-01-15 10:02:28] Browser #3: Extracted 'Angel's Kiss French Elegant Midi Dress' (price 21.19, -11%)
[2026-01-15 10:02:28] Browser #3: Saved to Outputs/products.json (2 total)
...
[2026-01-15 10:02:58] Browser #2: Browser unreachable, recreating...
[2026-01-15 10:02:58] Browser #2: URL requeued for retry.
[2026-01-15 10:03:04] Browser #2 (new): AVAILABLE.
[2026-01-15 10:03:04] Browser #2 -> SCRAPING (requeued URL) ...
[2026-01-15 10:03:11] Browser #1: BLOCKED, entering cooldown (30s).
[2026-01-15 10:03:11] Browser #1: URL requeued for another browser.
...
[2026-01-15 10:05:00] Duplicate URL skipped: https://us.shein.com/.../p-412530044.html
[2026-01-15 10:05:00] Progress: 500/1000 | Saved: 492 | Blocked: 2 | Retrying: 6
========================================================================
[2026-01-15 10:17:41] Run complete. 1000 URLs processed.
[2026-01-15 10:17:41] Final: 986 products saved to Outputs/products.json
========================================================================
```

---

## 📸 Screenshots

> Screenshot placeholders — add real images under `docs/images/` to document the system visually.

| | |
|---|---|
| **Project Structure** | ![Project Structure](docs/images/project-structure.png) |
| **BrowserPool Running** | ![BrowserPool](docs/images/browserpool.png) |
| **CAPTCHA Detection** | ![CAPTCHA Detection](docs/images/captcha-detection.png) |
| **Successful Product Extraction** | ![Product Extraction](docs/images/product-extraction.png) |
| **products.json Output** | ![products.json Output](docs/images/products-json.png) |

---

## ⚠️ Limitations

Modern anti-bot protected websites are constantly evolving, so the following are **expected operational characteristics** rather than failures:

- 🛡️ Occasionally SHEIN presents a **new or stronger verification challenge** that cannot be solved automatically on the first attempt.
- 🖐️ In some cases the user may need to **manually solve the first CAPTCHA** using `setup_session.py` or the opened browser window.
- 🔄 Once the session is established, the scraper usually **continues automatically for long periods** using the saved Chrome profile.
- 🧩 Changes to SHEIN's **page structure** may require updating the centralized `HTML_SELECTORS` in `Shein.py`.
- ⛽ **Gemini API quotas** may temporarily pause automatic CAPTCHA solving until another configured API key is selected (key rotation).
- 🌐 Large scraping jobs depend on **network stability** and **Chrome stability**.

---

## 🧗 Challenges Faced During Development

Building a reliable scraper for a heavily protected e-commerce site involved solving several real engineering problems:

| Challenge | Solution |
|-----------|----------|
| **Dynamic page loading** | Waits, polling, and embedded-JSON extraction instead of fragile static parsing |
| **Anti-bot protection** | `undetected-chromedriver` + stealth flags + persistent Chrome profile |
| **Multiple verification page types** | Gemini Vision returns structured actions (click / drag / refresh) per challenge type |
| **Browser crashes** | Automatic dead-browser detection and recreation by the pool |
| **Maintaining concurrent browser sessions** | A thread-safe `BrowserPool` with per-browser state tracking |
| **Preventing duplicate products** | URL deduplication before the run + duplicate checks during saving |
| **Thread-safe JSON writing** | Lock-guarded writes to `Outputs/products.json` |
| **Incremental saving to avoid data loss** | Each product is appended immediately, not at the end |
| **Scaling from a few URLs to thousands** | Queue-driven workers, cooldowns, retries, and requeueing |

---

## 📋 Assumptions

The system is designed and tested under these assumptions:

- ✅ **Google Chrome** is installed on the host machine.
- ✅ A valid **Gemini API key** (or multiple keys) is provided in `.env`.
- ✅ Internet connection is **stable**.
- ✅ SHEIN's website structure has **not changed significantly**.
- ✅ Product URLs supplied in `Inputs/urls.txt` are **valid** product-page URLs.

---

## 📦 Outputs

- **`Outputs/products.json`** – Accumulated JSON array of all scraped products. Each entry includes:
  - `url`
  - `name`, `product_name_safe`
  - `sku`, `reviews`, `available_sizes`
  - `current_price_integer`, `current_price_decimal`
  - `old_price_integer`, `old_price_decimal`
  - `discount_percentage`
  - `description` (text) and `description_structured` (text + attributes)
  - `is_international`, `OUT_OF_STOCK`, `INTERNATIONAL_ONLY` flags

- **`./Logs/`** – Log files for each module (e.g., `main.log`, `Shein.log`), with ANSI colors stripped for readability.

---

## 🔮 Future Improvements

> Features that are **not** currently implemented — listed here as possible next steps only.

- Retry / backoff tuning per challenge type
- Web dashboard for live monitoring of the BrowserPool
- CSV / Excel export in addition to JSON
- Dockerized deployment with bundled Chrome
- Distributed scraping across multiple machines
- Structured logging with rotation and JSON log records
- SQLite / PostgreSQL persistence layer for very large datasets

---

## ⚠️ Important Notes

- **Ethical use**: Respect SHEIN's `robots.txt` and Terms of Service. This tool is intended for personal, non-commercial, and rate-limited use. Do not overload the site with requests.
- **API keys are required** for automated CAPTCHA solving. Without them, the scraper cannot clear verification challenges automatically.
- **Website changes**: SHEIN's HTML structure may change over time. CSS selectors are centralized in `HTML_SELECTORS` inside `Shein.py`, and verification thresholds are centralized in `config.py` — update these as needed.
- **Respect rate limits**: A delay between requests (`DELAY_BETWEEN_REQUESTS`) is built in to avoid rate limiting.
- **Session persistence**: For best results, warm up an authenticated Chrome profile first (`setup_session.py`).

---

## 📄 License

For personal/educational use. Use at your own risk and in compliance with all applicable laws and website terms.


