# UK Property Hunt with Claude Code — Case Study

**How a London flat search that takes 3+ hours a week was reduced to a single command**

---

## The Problem

Searching for a rental property in London is genuinely painful. Four major platforms (Rightmove, Zoopla, OpenRent, SpareRoom), each with their own search UI, each updated daily with new listings. To stay competitive — especially for the best Zone 1–2 flats that go within 24–48 hours — you need to check frequently. That means:

- Manually searching 4 platforms × 8 target areas = 32+ search pages per session
- Copying promising listings into a spreadsheet
- Filtering duplicates you've already seen
- Writing personalised outreach messages for each one
- Tracking which you've contacted and what the status is

For a Software Engineer searching for a furnished studio or 1-bed flat in London on a ~£1,900/month budget, this was a 3–4 hour task done every few days.

**The goal:** automate the full pipeline in one Claude Code session.

---

## The Solution: 4 Steps, Fully Automated

A custom skill executed four steps end-to-end:

### Step 1 — Multi-platform search

Claude Code opened a Chrome tab (via the Claude in Chrome MCP extension) and navigated to each platform's search results for 8 target London postcodes. Rather than scraping HTML, it used each platform's own embedded data:

- **Rightmove** — JSON embedded in a `__NEXT_DATA__` script tag, containing all listing objects with price, address, bedroom count, and listing URL
- **Zoopla** — JSON-LD structured data in an `lsrp-schema` script tag (a search results schema with full `itemListElement` arrays)
- **OpenRent** — page text extraction via accessibility tree
- **SpareRoom** — same

One technical wrinkle: browser JavaScript can only return ~900 chars to the agent. For large JSON payloads, the skill injected the data into a hidden `<pre>` DOM element and read it back with a page-text tool — effectively using the browser's own DOM as a data transfer buffer.

**Result: 230 listings collected across 4 platforms.**

### Step 2 — Deduplicated tracker update

A Python script using `openpyxl`:
- Opened the existing `london_room_hunt.xlsx` spreadsheet
- Loaded all existing URLs from the target sheet into a Python `set`
- For each new listing, skipped it if the URL already existed (O(1) lookup)
- Assigned a priority based on area and price:
  - **High** (green) — Zone 1–2 South, ≤ budget, furnished
  - **Medium** (yellow) — priority-2 areas
  - **Low** (red) — outer areas, or over budget
- Wrote new rows with colour-coded fills, clickable hyperlinks, and today's date in a "Found On" column
- Froze the header row and enabled auto-filter

**Result: 222 new listings added. 8 already existed (duplicates skipped). Priority breakdown: 132 High · 49 Medium · 41 Low.**

### Step 3 — Personalised outreach messages

For each HIGH priority listing, a ready-to-send `.txt` file was generated and saved with:
- A personalised opening line referencing the specific street or area
- The renter's profile (age, profession, lifestyle)
- Move-in date, budget, and a polite request to arrange a viewing

Each file was named `outreach_{platform}_{area}_{listing_id}.txt` and saved to the local outreach folder.

**Result: 132 outreach files ready to send. Time cost: zero.**

### Step 4 — Summary email

The Gmail MCP connector created an HTML draft email, then Chrome opened and sent it. The email included:

- A stats banner (HIGH / MEDIUM / LOW counts, total tracked, outreach files saved)
- Cards for every HIGH listing with a ready-to-send message
- A "Top Picks" table sorted by price ascending
- A backlog section for uncontacted HIGH listings from previous runs

**Result: Email delivered to inbox within seconds of the task completing.**

---

## Numbers

| Metric | Value |
|--------|-------|
| Platforms searched | 4 (Rightmove, Zoopla, OpenRent, SpareRoom) |
| Target postcodes | 8 |
| Total listings found | 230 |
| New listings added this run | 222 |
| Duplicates skipped | 8 |
| HIGH priority listings | 132 |
| Outreach files generated | 132 |
| End-to-end runtime | ~45 minutes (mostly browser navigation wait time) |
| Human effort required | Zero — runs fully automatically on schedule |

---

## What Made This Possible

**1. Structured data, not scraping**
Both Rightmove and Zoopla embed complete listing data in their HTML as structured JSON — the same data their own JavaScript reads. This makes extraction reliable and doesn't depend on fragile CSS selector parsing.

**2. Browser-as-data-pipe**
When the browser's JavaScript execution returns truncated strings (a known limitation), the skill worked around it by injecting data into the DOM as a hidden element, then reading it back with a page-text accessibility tool. This pattern reliably handles payloads of any size.

**3. Idempotent design**
The deduplication-by-URL approach means the task can be run daily without creating duplicates. Each run just adds the genuinely new listings from that day.

**4. Separation of "find" and "act"**
The skill doesn't try to automatically apply to listings — it finds, tracks, and prepares. You stay in control of which outreach messages to send and which listings to pursue. This keeps the automation useful without being intrusive.

**5. Connected tools working together**
- **Claude in Chrome** for browser navigation and data extraction
- **Python shell** for spreadsheet manipulation (openpyxl)
- **Gmail MCP connector** for programmatic draft creation
- **Chrome again** for actually sending (Gmail's web UI handles the final send action)

---

## How to Adapt This for Your Own Search

1. Clone this repo
2. Copy `config.example.md` → `config.md` and fill in your details
3. Install the skill in Claude Code (see `skill.md`)
4. Schedule it: `/schedule 0 9,18 * * * Run the London property hunt skill`

The skill handles the rest: searching, tracking, outreach messages, and emailing you a summary.

**Suggested use:** run it daily in the morning. New listings from overnight will land in your tracker before you've had your first coffee.

---

## Skills Demonstrated

This workflow handles a task that is:

- **Multi-step** — 4 distinct phases, each dependent on the previous
- **Multi-tool** — browser automation, file system, Python execution, email API
- **Stateful** — maintains a tracker that persists across runs
- **Resilient** — handles platform quirks (truncated JS output, schema differences) with in-session workarounds
- **Practical** — produces real, actionable outputs (a spreadsheet you can filter, messages you can send)

It's a good example of the kind of recurring research-and-action workflow where AI agents provide the most value: tasks that are too complex to fully automate with a simple script, but too repetitive and time-consuming to do manually every day.

---

*Built with Claude Code + Claude in Chrome + Gmail MCP | April 2026*
