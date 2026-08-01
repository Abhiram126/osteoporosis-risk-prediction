repository implementation.

BLACKBOXAI Thinking...

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8%2B-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Selenium-4.46-43B02A?style=for-the-badge&logo=selenium&logoColor=white" alt="Selenium">
  <img src="https://img.shields.io/badge/Playwright-1.51-2EAD33?style=for-the-badge&logo=playwright&logoColor=white" alt="Playwright">
  <img src="https://img.shields.io/badge/BeautifulSoup-4.14-7A0C0E?style=for-the-badge&logo=python&logoColor=white" alt="BeautifulSoup">
  <img src="https://img.shields.io/badge/Gemini%20AI-Enabled-8E75B2?style=for-the-badge&logo=googlegemini&logoColor=white" alt="Gemini AI">
</p>

<h1 align="center">🛍️ SHEIN Web Scraper</h1>

<p align="center">
  <strong>An automated, AI-powered product scraper for SHEIN US</strong><br>
  <em>Anti-detection browsing · Gemini Vision CAPTCHA solving · Concurrent 3-browser pool · Structured JSON output</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-production_ready-2ea44f?style=flat-square">
  <img src="https://img.shields.io/badge/architecture-multi--threaded-1f6feb?style=flat-square">
  <img src="https://img.shields.io/badge/browser-undetected--chromedriver-43B02A?style=flat-square">
  <img src="https://img.shields.io/badge/output-json-f9c74c?style=flat-square">
</p>


## 📌 Executive Summary

**SHEIN Web Scraper** is a Python-based engineering project that automates the end-to-end collection of product data — names, prices, discounts, descriptions, sizes, SKUs, reviews, and shipping flags — from **SHEIN US** product pages at scale.

The system is built around three pillars:

1. **Realistic browsing** — `undetected-chromedriver` (with Playwright as a fallback) and persistent Chrome profiles avoid bot detection.
2. **AI-powered verification handling** — Google **Gemini Vision** analyzes screenshots and drives CDP mouse events to solve CAPTCHA/verification challenges automatically.
3. **Concurrent, fault-tolerant scraping** — a thread-safe `BrowserPool` runs **3 reusable Chrome instances** in parallel, with automatic CAPTCHA blocking/cooldown, dead-browser recovery, URL requeueing, and incremental JSON persistence.

The project is a complete 3-phase pipeline: **Mass URL Discovery → URL Cleaning → Mass Scraping**, and is designed to scale to **10,000+ product URLs** while protecting results against data loss at every step.


## 🎯 Project Goals

| Goal | How it is achieved |
|------|--------------------|
| Automate product data collection at scale | 3-phase pipeline (Discovery → Cleaning → Scraping) with a concurrent `BrowserPool` |
| Evade bot detection reliably | `undetected-chromedriver` + anti-detection flags + persistent Chrome profiles |
| Solve CAPTCHAs automatically | Gemini Vision AI reads screenshots and returns structured click/drag actions executed via CDP |
| Never lose scraped data | Incremental, atomic writes to `Outputs/products.json` with duplicate-URL detection |
| Handle anti-bot blocking gracefully | Browser status tracking, cooldown, URL requeueing, and automatic browser recreation |
| Provide structured, analyst-friendly output | Unified JSON schema with prices split into integer/decimal parts and structured descriptions |
| Document the engineering decisions | Comprehensive README, module docstrings, and centralized configuration |


## ✨ Project Highlights

> ✅ = Implemented and verified in the repository

- ✅ **AI-powered CAPTCHA solving** — Gemini Vision + CDP mouse events
- ✅ **Concurrent BrowserPool** — 3 reusable Chrome instances running in parallel
- ✅ **Thread-safe architecture** — `threading.Lock`, `queue.Queue`, `ThreadPoolExecutor`
- ✅ **Automatic browser recovery** — dead browsers are detected and recreated
- ✅ **Persistent Chrome profiles** — `ChromeProfile/`, `ChromeProfile_1..3`, `ChromeProfile_Discovery`
- ✅ **Duplicate URL detection** — already-scraped URLs are skipped via `products.json`
- ✅ **Incremental JSON saving** — atomic write (`.tmp` + `os.replace`) after every product
- ✅ **10,000+ product scalable design** — configurable `--target` (default 10,000)
- ✅ **Gemini API key rotation** — multiple `Name:Key` keys with automatic rotation on quota exhaustion
- ✅ **Manual session warmup fallback** — `setup_session.py` for human-assisted CAPTCHA clearing
- ✅ **Dual-channel logging** — colored console output + ANSI-stripped log files


## 📊 Repository Statistics

| Attribute | Value |
|-----------|-------|
| **Language** | Python (>= 3.8) |
| **Architecture** | Multi-threaded (thread pool + shared queue) |
| **Browser Engine** | undetected-chromedriver (Playwright fallback) |
| **AI** | Google Gemini Vision (`google-genai`) |
| **Output Format** | JSON (`Outputs/products.json`) |
| **Target Website** | SHEIN US (`us.shein.com`) |
| **Concurrency** | 3 browsers (default) |
| **CAPTCHA Model** | `gemini-3.1-flash-lite` |
| **Pipeline Phases** | 3 (Discovery → Cleaning → Scraping) |
| **Supported Categories** | 26 main SHEIN categories |


## 🏗️ Project Structure

```text
shein_web_scraper_testing/
├── main.py                   # Core execution engine — orchestrates BrowserPool + saving
├── run_pipeline.py           # End-to-end orchestrator: Discovery → Cleaning → Scraping
├── Shein.py                  # Main scraper class — page loading, parsing, CAPTCHA solving
├── browser_pool.py           # Thread-safe pool of reusable Chrome instances (concurrency)
├── browser_manager.py        # Simple single-browser lifecycle manager
├── Gemini.py                 # Google Gemini API wrapper (Vision CAPTCHA solving)
├── config.py                 # Centralized verification/challenge handling configuration
├── category_config.py        # 26 SHEIN main category names and URLs
├── user_selection.py         # Interactive category selection for the discovery phase
├── setup_session.py          # Manual session warmup (solve CAPTCHAs by hand, save session)
├── product_utils.py          # Filesystem-safe product name normalization
├── urls_utils.py             # URL preprocessing helpers (clean, dedupe, sort, backup)
├── urls_input_file_adder.py  # Standalone URL cleaning/deduplication utility
├── Logger.py                 # Dual-channel logger (terminal + ANSI-stripped log file)
├── requirements.txt          # Python dependencies
├── .env                      # Environment variables (GEMINI_API_KEY) — not committed
├── .gitignore                # Ignored runtime artifacts
├── ChromeProfile/            # Persistent Chrome profile (created at runtime)
├── ChromeProfile_1..3/       # Per-browser profiles used by BrowserPool (runtime)
├── Inputs/
│   ├── urls.txt              # Product URLs to scrape (one per line, # = comment)
│   └── urls-backup.txt       # Automatic backup copy of urls.txt
├── Logs/                     # Dual-channel log files (created at runtime)
└── Outputs/
    ├── products.json         # Accumulated scraped product data (JSON array)
    └── .staging/             # Temporary staging area during runs
```


## 🔧 Technologies Used

| Technology | Purpose |
|------------|---------|
| [undetected-chromedriver](https://github.com/undetected-chromedriver/undetected-chromedriver) 3.5.5 | Anti-detection browser automation |
| [Playwright](https://playwright.dev/python/) 1.51 + `playwright-stealth` | Fallback browser automation engine |
| [Selenium](https://www.selenium.dev/) 4.46 | CDP mouse events & WebDriver control |
| [BeautifulSoup 4](https://www.crummy.com/software/BeautifulSoup/) 4.14 | HTML parsing & product data extraction |
| [google-genai](https://github.com/googleapis/python-genai) 1.61 | Gemini Vision API for CAPTCHA analysis |
| [python-dotenv](https://github.com/theskumar/python-dotenv) 1.2 | `.env` configuration loading |
| [colorama](https://pypi.org/project/colorama/) 0.4 | Colored terminal output |
| [tqdm](https://github.com/tqdm/tqdm) 4.67 | Progress reporting |
| [opencv-python](https://opencv.org/) / [pillow](https://python-pillow.org/) / [PyAutoGUI](https://github.com/asweigart/pyautogui) | Image / screenshot support |
| [requests](https://requests.readthedocs.io/) / [httpx](https://www.python-httpx.org/) | HTTP layer used by the SDKs |


## 📋 Requirements

- **Python** >= 3.8
- **Google Chrome** installed (for `undetected-chromedriver`)
- A **Google Gemini API key** for automated CAPTCHA solving (from [Google AI Studio](https://aistudio.google.com/))

Install all dependencies:

```bash
pip install -r requirements.txt
```


## 🚀 Quick Start

### 1. Configure the `.env` file

Create a `.env` file in the project root with your Gemini API key(s).

**Single key:**

```dotenv
GEMINI_API_KEY=your_gemini_api_key_here
```

**Multiple keys (automatic rotation on quota exhaustion):**

```dotenv
GEMINI_API_KEY=OwnerA:KEY_A,OwnerB:KEY_B,OwnerC:KEY_C
```

> 💡 **Tip:** Legacy plain comma-separated keys are also supported and are auto-named `key_1`, `key_2`, ...

**Optional environment variables:**

| Variable | Description |
|----------|-------------|
| `CHROME_EXECUTABLE_PATH` | Path to a custom Chrome executable |
| `CHROME_PROFILE_PATH` | Path to an existing Chrome profile (for authenticated sessions) |
| `HEADLESS` | Set `true` to run headless (default `false`) |
| `BROWSER_PROXY` | Optional `--proxy-server` value for all browser instances |

### 2. Warm up a Chrome profile (recommended)

The scraper works best with an authenticated Chrome profile. Run the manual session setup once:

```bash
python setup_session.py
```

Solve any CAPTCHAs manually in the opened browser window, then press **ENTER** once the product page loads. The session (cookies) is saved to the `ChromeProfile/` directory and reused by the automated scraper.

### 3. Run the full pipeline

```bash
python run_pipeline.py
```

You will be prompted to select SHEIN categories. The pipeline then:

1. **Phase 1 – Discovery** — navigates category pages, dismisses cookie popups, solves verification challenges with Gemini, and harvests product URLs into `Inputs/urls.txt`.
2. **Phase 2 – Cleaning** — deduplicates and sanitizes the discovered URLs.
3. **Phase 3 – Scraping** — launches `main.py` to scrape every URL with 3 concurrent browsers and save results.


## 🧑‍💻 Usage Reference

### Option A — Full automated pipeline (recommended)

```bash
python run_pipeline.py
```

**Pipeline options:**

| Command | Description |
|---------|-------------|
| `python run_pipeline.py --target 1000` | Stop discovery after 1000 URLs (default 10000) |
| `python run_pipeline.py --out Inputs/urls.txt` | Custom output file for discovered URLs |
| `python run_pipeline.py --categories file.txt` | *Defined in CLI parser; file-loading is currently disabled in code* — categories are chosen interactively |

### Option B — Scrape a fixed list of URLs

1. Put product URLs into `Inputs/urls.txt` (one per line, `#` lines are ignored).
2. Run the core engine:

```bash
python main.py
```

**Main options:**

| Command | Description |
|---------|-------------|
| `python main.py --verbose` | Enable verbose debug output |
| `python main.py --target 10` | Scrape at most 10 URLs (0 = unlimited) |

### Option C — Clean your URL list

```bash
python urls_input_file_adder.py
```


## 🧠 Architecture Overview

The system is split into three cooperating layers:

```mermaid
flowchart TB
    subgraph CLI["🖥️ CLI Entry Points"]
        RP["run_pipeline.py<br/><i>Orchestrator</i>"]
        M["main.py<br/><i>Core Engine</i>"]
        SS["setup_session.py<br/><i>Manual Warmup</i>"]
        UA["urls_input_file_adder.py<br/><i>URL Cleaner</i>"]
    end

    subgraph CORE["⚙️ Core Modules"]
        S["Shein.py<br/><i>Scraper Class</i>"]
        BP["browser_pool.py<br/><i>BrowserPool</i>"]
        BM["browser_manager.py<br/><i>Single Browser</i>"]
        G["Gemini.py<br/><i>Vision API Wrapper</i>"]
        CFG["config.py<br/><i>Tunables</i>"]
    end

    subgraph UTIL["🧰 Utilities"]
        PU["product_utils.py"]
        UU["urls_utils.py"]
        LG["Logger.py"]
        CC["category_config.py"]
        US["user_selection.py"]
    end

    subgraph DATA["📁 Data Layer"]
        URLS["Inputs/urls.txt"]
        JSON["Outputs/products.json"]
        LOGS["Logs/*.log"]
        PROFILES["ChromeProfile_*"]
    end

    RP --> S
    RP --> BP
    RP --> CC
    RP --> US
    M --> BP
    M --> S
    M --> G
    S --> G
    S --> CFG
    BP --> S
    S --> PU
    RP --> UU
    M --> UU
    RP --> LG
    M --> LG
    BP --> PROFILES
    M --> URLS
    BP --> JSON
    S --> LOGS
```

### BrowserPool Architecture

```mermaid
flowchart LR
    subgraph Main["🧵 Main Thread"]
        Q["🔀 Shared queue.Queue<br/><i>thread-safe URL queue</i>"]
        R["🔄 URL Requeue<br/>max 2 retries / URL"]
    end

    subgraph Pool["🌐 BrowserPool (3 workers)"]
        W1["Worker Thread 1<br/>Browser-1"]
        W2["Worker Thread 2<br/>Browser-2"]
        W3["Worker Thread 3<br/>Browser-3"]
    end

    subgraph States["📊 Browser States"]
        S1["INITIALIZING"]
        S2["AVAILABLE"]
        S3["SCRAPING"]
        S4["BLOCKED ⏳ cooldown"]
        S5["STOPPED"]
    end

    Q --> W1
    Q --> W2
    Q --> W3
    W1 --> S1 --> S2 --> S3
    S3 -- CAPTCHA unsolved --> S4
    S4 -- cooldown expired --> S2
    W1 -- dead browser --> R
    R -- requeue --> Q
```

### Complete Pipeline Flow

```mermaid
flowchart TD
    A["🎯 Run run_pipeline.py"] --> B["📋 select_categories()<br/>26 categories"]
    B --> C["🟢 PHASE 1: Discovery<br/>undetected-chromedriver"]
    C --> D["Navigate category pages<br/>page=1..N"]
    D --> E{"Verification<br/>challenge?"}
    E -- Yes --> F["Gemini Vision solver"]
    F --> G{"Cleared?"}
    G -- No --> D
    G -- Yes --> H["Dismiss cookie popup"]
    E -- No --> H
    H --> I["Scroll + extract<br/>product URLs (-p-)"]
    I --> J["Append to Inputs/urls.txt"]
    J --> K{"Empty pages<br/>> 3?"}
    K -- No --> D
    K -- Yes --> L["🟡 PHASE 2: Cleaning<br/>dedupe + sanitize"]
    L --> M["🔵 PHASE 3: Scraping<br/>subprocess main.py"]
    M --> N["BrowserPool (3 browsers)"]
    N --> O["Shein.scrape() per URL"]
    O --> P["💾 save_product_data_json()<br/>atomic write to products.json"]
```

### UML Sequence Diagram — Concurrent Scraping

```mermaid
sequenceDiagram
    participant Main as main.py
    participant Pool as BrowserPool
    participant Q as URL Queue
    participant W as Worker (Browser-N)
    participant S as Shein scraper
    participant G as Gemini Vision
    participant F as products.json

    Main->>Pool: BrowserPool(num_browsers=3)
    Pool->>W: submit worker loop
    Main->>Q: submit_url(url) x N
    loop While queue not empty
        W->>Q: get(timeout=0.1)
        W->>S: navigate + dismiss cookie popup
        S->>G: screenshot → analyze
        G-->>S: structured JSON action
        S->>S: execute CDP click/drag
        S->>S: poll until verification cleared
        S-->>W: product_data dict
        W->>F: save_callback(product_data, url)
        W->>W: mark AVAILABLE
    end
    Main->>Pool: shutdown(wait=True)
```


## 🧵 Threading & Concurrency Model

- **1 main thread** submits all URLs into a shared `queue.Queue`.
- **3 worker threads** (via `ThreadPoolExecutor`, `thread_name_prefix="BrowserWorker"`) each own exactly one Chrome instance.
- Browser state (`status`, `blocked_until`, `current_url`) is guarded by a `threading.Lock` (`browser_lock`).
- URL retry counts are guarded by a separate `retries_lock`.
- Processed counters use `_processed_lock` to avoid race conditions.
- Each browser uses its **own profile directory** (`ChromeProfile_1`, `ChromeProfile_2`, `ChromeProfile_3`) to avoid Chrome profile-lock conflicts.
- Browser instances are created **sequentially** at startup (`uc.Chrome` is not thread-safe for creation).

> ⚠️ **Important:** Browser creation is intentionally serialized — `uc.Chrome` cannot be instantiated concurrently.


## 🧠 How It Works — CAPTCHA Solving

### Gemini Vision CAPTCHA Workflow

```mermaid
flowchart TD
    A["📸 Capture fresh screenshot<br/>(captcha_screenshot.png)"] --> B["🤖 Send to Gemini Vision<br/>gemini-3.1-flash-lite"]
    B --> C["📝 Parse structured JSON response"]
    C --> D{"has_verification?"}
    D -- No --> E["✅ Poll DOM for product markers"]
    D -- Yes --> F["Execute action via CDP<br/>click / multi_click / type / slide"]
    F --> G["🔄 Poll URL + DOM<br/>VERIFICATION_POLL_TIMEOUT=25s"]
    G --> H{"Cleared?"}
    H -- Yes --> E
    H -- No --> I{"Attempts<br/>< 7?"}
    I -- Yes --> A
    I -- No --> J["🔄 Browser restart required<br/>(max 2 restarts)"]
    J --> K["Return RESTART_REQUIRED → pool"]
```

### Detailed Steps

1. The page is loaded with anti-detection flags (persistent Chrome profile, `--disable-blink-features=AutomationControlled`, etc.).
2. Cookie consent popups are dismissed via aggressive CSS/JS injection (no API call needed).
3. A fresh screenshot is captured and sent to **Gemini Vision**.
4. Gemini returns a **structured JSON** action plan:
   ```json
   {
     "has_verification": true,
     "verification_type": "checkbox",
     "action": "click",
     "target_x": 640,
     "target_y": 380,
     "is_cleared": false
   }
   ```
5. The action is executed via **CDP mouse events** (with `ActionChains` fallback for Selenium).
6. The page is **polled** (`VERIFICATION_POLL_INTERVAL` / `VERIFICATION_POLL_TIMEOUT`) until the URL no longer contains verification patterns and the DOM shows product markers.
7. On repeated failure, the browser is restarted (`BROWSER_RESTART_THRESHOLD`, `MAX_CONSECUTIVE_RESTARTS`), or the URL is requeued / the browser is cooled down.

### Verification Detection Signals

| Signal | Mechanism |
|--------|-----------|
| URL patterns | `captcha`, `challenge`, `verify`, `security-check`, `turnstile` |
| DOM keywords | `"verify you are human"`, `"slide to complete"`, `"i'm not a robot"`, `"turnstile"` |
| Product page markers | `productintro`, `add to bag`, `sku`, `productMainPriceId`, `fsp-element` |


## 📦 Data Extraction Strategy

### Price extraction (3 fallback layers)

1. **JSON-first** — `promotionInfoPrice.amountWithSymbol` and `originalPrice.amountWithSymbol` from embedded `<script type="application/json">`.
2. **HTML selectors** — centralized `HTML_SELECTORS` dictionary in `Shein.py`.
3. **Computational fallback** — old price derived from `current_price / (1 - discount%)`.

Brazilian currency format (`R$2.299,08`) is normalized into separate integer and decimal parts (`("2299", "08")`).
