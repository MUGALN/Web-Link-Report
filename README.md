
# Site Link Crawl Report

Generate an Excel report of all hyperlinks found across a website (including sub‑pages), complete with **cropped screenshots** of each hyperlink element and rich metadata.

> **What you get:**
> - An Excel workbook with two sheets:
>   - **Links**: Screenshot, Link Text, Raw Href, Resolved URL, HTTP Status, Target, Rel, Internal/External, plus the **Source Page URL and Title** for every link.
>   - **Crawl Summary**: Each crawled page with title, fetch status, and the number of links captured.

---

## 🔎 Overview
The script `site_link_crawl_report.py` crawls a website starting from a given URL, visiting internal pages up to a configurable **depth** and **page limit**. For every page, it extracts all `<a>` elements, takes a **cropped screenshot** of each visible link, resolves the final URL (follows redirects), and exports everything to an **Excel report**.

---

## ✅ Features
- **Site crawl** with BFS: limit by **max pages** and **depth**
- **Element‑only screenshots** of each hyperlink (optional)
- **Excel export** with embedded images + link metadata
- **Crawl Summary** sheet (page titles, fetch status, link counts)
- **robots.txt** support (optional; recommended to respect)
- **URL filtering** via include/exclude **regex** patterns
- **Headless or headful** Chrome; **pause for login** if needed
- Handles **internal vs external** classification and **redirect resolution**

---

## 🛠 Prerequisites
- **Python** 3.7+
- **Google Chrome** (or Chromium) installed
- **Selenium ≥ 4.6** (uses Selenium Manager to auto-manage ChromeDriver)

> If you run into driver issues, make sure Chrome is installed and up-to-date.

---

## 📦 Installation
Install required Python packages:

```bash
pip install --upgrade selenium requests openpyxl pillow
```

> On some systems you may use `pip3` instead of `pip`, and `python3` instead of `python`.

---

## ▶️ Quick Start

```bash
python site_link_crawl_report.py \
  --start-url "https://example.com" \
  --out "site_links_report.xlsx" \
  --max-pages 30 \
  --depth 2 \
  --delay 0.6 \
  --include-subdomains \
  --respect-robots
```

This will crawl up to **30 pages** within **2 levels** from the start URL, being polite with a small delay between pages, treat subdomains as internal, and obey `robots.txt`. The output Excel will be saved as `site_links_report.xlsx`.

---

## ⚙️ Command-line Options

```
--start-url              Start URL for the crawl (required)
--out                    Output Excel file path (default: site_links_report.xlsx)

# Crawl controls
--max-pages              Max pages to crawl (default: 30)
--max-links-per-page     Max links per page to capture (default: 300)
--max-total-links        Global max links to capture (default: 3000)
--depth                  Max crawl depth (default: 2)
--delay                  Delay (seconds) between page fetches (default: 0.5)
--same-domain-only       Restrict crawl to same domain (default: True)
--include-subdomains     Treat subdomains as internal
--keep-query             Keep query params when normalizing URLs (default strips queries)
--include-fragments      Include #fragment-only links when extracting from pages
--pattern-include REGEX  Only crawl URLs matching this regex (case-insensitive)
--pattern-exclude REGEX  Exclude URLs matching this regex (case-insensitive)

# Networking / robots
--timeout                HTTP timeout (seconds) for URL resolution (default: 8)
--respect-robots         Obey robots.txt (recommended)
--ignore-robots          Ignore robots.txt (use responsibly)

# Browser
--headful                Run a visible browser (default is headless)
--pause-on-first         Load the first page and wait for ENTER (login/CAPTCHA)

# Output speed toggles
--no-resolve             Skip resolving final URLs / HTTP status (faster)
--no-screenshots         Skip screenshots and only export metadata
```

---

## 🧪 Examples
1. **Crawl only internal pages, shallow depth**:
   ```bash
   python site_link_crawl_report.py --start-url "https://example.com" --depth 1
   ```

2. **Include subdomains and focus on docs**:
   ```bash
   python site_link_crawl_report.py \
     --start-url "https://example.com" \
     --include-subdomains \
     --pattern-include "docs"
   ```

3. **Skip screenshots for speed**:
   ```bash
   python site_link_crawl_report.py --start-url "https://example.com" --no-screenshots
   ```

4. **Login first, then crawl** (useful for private sites):
   ```bash
   python site_link_crawl_report.py \
     --start-url "https://portal.example.com" \
     --headful --pause-on-first --respect-robots
   # A Chrome window opens; log in, then return to the terminal and press ENTER
   ```

5. **Polite crawl with tighter limits**:
   ```bash
   python site_link_crawl_report.py \
     --start-url "https://example.com" \
     --max-pages 10 --depth 1 --delay 1.2 --respect-robots
   ```

---

## 📄 Output Details
**Excel workbook** with two sheets:

### Sheet: `Links`
Columns:
- **Source Page** (URL)
- **Source Title**
- **Screenshot** (embedded image of the link element; if enabled)
- **Link Text**
- **Raw Href (absolute)**
- **Resolved URL** (after redirects)
- **HTTP Status** (if resolution enabled)
- **Target** (e.g., `_blank`)
- **Rel** (e.g., `nofollow`)
- **Internal/External** classification

### Sheet: `Crawl Summary`
Columns:
- **#** (order crawled)
- **Page URL**
- **Page Title**
- **HTTP Status (fetch)**
- **Links Captured**

> Screenshots are taken of the **link element only**, highlighted briefly to make it stand out, then embedded in the Excel cell. For performance or compatibility reasons, you can disable screenshots with `--no-screenshots`.

---

## 💡 Tips & Best Practices
- **Respect robots.txt** unless you have explicit permission (`--respect-robots`).
- Start conservatively: `--max-pages 10 --depth 1`, then scale up.
- Use **filters** to limit scope: `--pattern-include` and `--pattern-exclude`.
- For **auth-protected** sites: `--headful --pause-on-first`, log in, then press **ENTER** to proceed.
- Increase `--delay` (e.g., 1–2 seconds) for heavier sites to be polite.
- If the site uses **infinite scroll**, consider increasing `--depth` and `--max-pages`, but keep limits to avoid very large reports.

---

## 🔧 Troubleshooting
- **ChromeDriver / Selenium errors**: Ensure Google Chrome is installed and up to date; Selenium ≥ 4.6 uses Selenium Manager to auto-resolve drivers.
- **Empty screenshots**: Some elements may be hidden or inside iframes/shadow DOM. This version captures links from the **main document**. If you need iframe/shadow DOM support, extend the script accordingly.
- **Blocked by robots or auth**: Use `--headful --pause-on-first` to log in, or adjust robots settings if you have permission.
- **Too many duplicate links**: Try `--keep-query` off (default) to normalize URLs, or apply `--pattern-include` to narrow scope.

---

## 🛡️ Legal & Ethical Use
Make sure you are **authorized** to crawl and screenshot the site. Respect the website’s **Terms of Service** and **robots.txt** directives. Use appropriate **delays** to avoid overloading the server.

---

## 📁 Project Structure (suggested)
```
.
├── site_link_crawl_report.py   # The crawler & Excel exporter
└── README.md                   # This guide
```

---

## 📬 Support
If you’d like this tailored (e.g., crawl depth, iframe/shadow DOM, page title of destination links, CSV export, or SharePoint/OneDrive output), open an issue or share your requirements.
