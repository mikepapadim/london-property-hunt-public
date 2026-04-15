# London Property Hunt — Claude Code Skill

> **How to use:** Copy this file's contents into Claude Code as a skill, or run it directly.
> Before running, fill in your personal details in `config.md` (copy from `config.example.md`).
> All `[PLACEHOLDER]` values below are read from your config file.

---

## Skill prompt (paste into Claude Code)

```
You are running [YOUR_NAME]'s London room hunt. Do this automatically — no user present. Complete all steps fully.

## WHO IS [YOUR_NAME]
- [YOUR_AGE]yo [YOUR_PROFESSION]
- Work: [YOUR_WORK_POSTCODE]
- Partner/friend: lives at [PARTNER_AREA] — [TUBE_LINE]
- Best areas: [LIST_TARGET_AREAS]
- Move-in: ~[MOVE_IN_DATE] (±1 week, can do mid-[MONTH] if needed)
- Tenant profile: [YOUR_PROFILE_DESCRIPTION]

## WHAT TO SEARCH FOR

### TYPE A — Furnished double rooms in shared flats/houses
- **CRITICAL: MAX 3 BEDROOMS in the property. Skip any listing in a 4-bed, 5-bed, 6-bed or larger property.**
- **FLATMATE AGE: Preferred [MIN_AGE]–[MAX_AGE]. If a listing explicitly states students only, under-25s only, or mentions a very young household — skip or mark LOW.**
- Ideally at least 2 bathrooms (note in tracker but don't exclude if unknown)
- Budget: up to £[ROOM_BUDGET] pcm (bills included preferred, but up to £[ROOM_BUDGET_NO_BILLS] if separate)
- Must be furnished

**Bed count filter — MANDATORY before adding any room:**
- 2-bed or 3-bed stated → ADD ✅
- 4-bed, 5-bed, 6-bed or more → SKIP ❌
- Bed count unknown → ADD with note "Verify ≤3 bed before messaging"

**Age filter:**
- Listing mentions professionals, 30s, working professionals, mature → HIGH ✅
- Listing mentions students, young, party, 18-25 → mark LOW or skip
- Age not mentioned → add normally, note "age unconfirmed"

Search SpareRoom for rooms:
[PASTE YOUR SPAREROOM ROOM SEARCH URLS HERE — one per area]
Example format:
- https://www.spareroom.co.uk/flatshare/london/[AREA]?max_rent=[ROOM_BUDGET]&sort=posted_date&mode=list

Search OpenRent for rooms:
- https://www.openrent.co.uk/properties-to-rent/london?term=[AREA1],[AREA2]&prices_max=[ROOM_BUDGET]&isLive=true&furnishedStatus=1&bedrooms_max=0

For OpenRent room listings, visit the individual listing page before adding. Check bed count — skip if 4+.

### TYPE B — Studios and 1-bedroom flats (whole unit)
- Budget: up to £[STUDIO_BUDGET] pcm, furnished, no bed count restriction

Search SpareRoom (flats-to-rent):
[PASTE YOUR SPAREROOM STUDIO SEARCH URLS HERE]
Example format:
- https://www.spareroom.co.uk/flats-to-rent/london/[AREA]?max_rent=[STUDIO_BUDGET]&sort=posted_date&mode=list

Search OpenRent (studios/1-beds):
- https://www.openrent.co.uk/properties-to-rent/london?term=[AREAS]&prices_max=[STUDIO_BUDGET]&isLive=true&furnishedStatus=1&bedrooms_max=1

Search Rightmove (furnished studios/1-beds):
- https://www.rightmove.co.uk/property-to-rent/find.html?searchType=RENT&locationIdentifier=REGION%5E87490&maxBedrooms=1&maxPrice=[STUDIO_BUDGET]&propertyTypes=flat&letFurnishType=furnished&includeLetAgreed=false

Search Zoopla:
- https://www.zoopla.co.uk/to-rent/flats/london/?beds_max=1&price_frequency=per_month&price_max=[STUDIO_BUDGET]&furnished_state=furnished&results_sort=newest_listings&pn=1

## TRACKER

File: [YOUR_HUNT_DIR]/london_room_hunt.xlsx
- Rooms → sheet: `Listings`
- Studios/1-beds → sheet: `Studios & 1-Beds`

Deduplication: skip if URL already exists in the sheet.
New rows: Status = `NEW 🔴`, Found On = today's date

Priority for rooms:
- HIGH: [PRIMARY_AREAS] + ≤£[ROOM_BUDGET] + available by ~[MOVE_IN_DATE + 1 week] + furnished + ≤3 bed + flatmates [MIN_AGE]+ (or age unconfirmed)
- MEDIUM: [SECONDARY_AREAS] within budget and timing, OR prime area with age/bed count flags
- LOW: late availability, outer area, student flat, or confirmed 4+ beds

Priority for studios:
- HIGH: Zone 1-2 [YOUR_PREFERRED_ZONES] + ≤£[STUDIO_BUDGET] + available by ~[MOVE_IN_DATE + 1 week] + furnished
- MEDIUM: Zone 2-3 within budget and timing
- LOW: late availability or outer areas

Row fill colours: HIGH = E2EFDA, MEDIUM = FFFFC7, LOW = FCE4D6

## EMAIL — FULLY SELF-CONTAINED FOR PHONE USE

[YOUR_NAME] may only have their phone. Email must be fully actionable without opening any other file.

Use gmail_create_draft (contentType: text/html), To: [YOUR_EMAIL]
Subject: 🏠 London Room Hunt — {DATE} ({morning/evening}) — {N} new listings

HTML body sections:

**A — Header:** Date, run, platforms. Bold counts: 🟢 HIGH: N | 🟡 MEDIUM: N | ⚪ LOW: N | 📋 TOTAL: N

**B — 🟢 HIGH New Today:** For EVERY HIGH listing, a card with:
- Title as large clickable link | Area | £price | Bills | Available | Beds (if known) | Platform
- Green styled box with ready-to-send message (<100 words, personalised):
  "Hi, [one specific sentence about this listing — location, price, availability, or unique feature]. I'm [YOUR_NAME], [YOUR_AGE], [YOUR_PROFESSION]. [YOUR_PROFILE_SUMMARY]. Looking to move ~[MOVE_IN_DATE]. Happy to arrange a viewing. [YOUR_NAME]"

**C — 🟡 MEDIUM New Today:** Title (link) | Area | Price | Available | short outreach box

**D — ⚪ LOW/SKIP:** Bullet list only

**E — 🔁 Backlog (uncontacted HIGH, not today):** Up to 8 listings from tracker where Status = NEW 🔴, Priority = High, Found On ≠ today. Prime areas first. Each gets a card with clickable URL and ready-to-send message.

**F — Stats:** Totals, area breakdown, next run time. End: "⚠️ X days to [MOVE_IN_DATE]. Message at least 5 listings today."

After creating draft: navigate to https://mail.google.com/mail/u/[GMAIL_ACCOUNT_INDEX]/#drafts, open the draft, click Send.

## OUTREACH FILES
Save .txt files for HIGH priority to [YOUR_HUNT_DIR]/outreach/

## SUCCESS CRITERIA
- Both sheets updated, no 4+ bed rooms added, no student/under-[MIN_AGE] flats added
- Outreach files saved for HIGH priority
- Email sent (always, even if zero new)
```

---

## Notes on the placeholders

| Placeholder | Example value | Where to set |
|---|---|---|
| `[YOUR_NAME]` | Alex | config.md |
| `[YOUR_AGE]` | 29 | config.md |
| `[YOUR_PROFESSION]` | Software Engineer | config.md |
| `[YOUR_WORK_POSTCODE]` | EC2A 1NT | config.md |
| `[PARTNER_AREA]` | Hackney Central | config.md |
| `[TUBE_LINE]` | Victoria Line | config.md |
| `[LIST_TARGET_AREAS]` | Hackney, Shoreditch, Bethnal Green | config.md |
| `[MOVE_IN_DATE]` | 1 June 2026 | config.md |
| `[YOUR_PROFILE_DESCRIPTION]` | easy-going, clean, tidy, hybrid WFH | config.md |
| `[ROOM_BUDGET]` | 1500 | config.md |
| `[ROOM_BUDGET_NO_BILLS]` | 1700 | config.md |
| `[STUDIO_BUDGET]` | 1900 | config.md |
| `[MIN_AGE]` / `[MAX_AGE]` | 28 / 40 | config.md |
| `[YOUR_EMAIL]` | you@gmail.com | config.md |
| `[YOUR_HUNT_DIR]` | ~/my-hunt | config.md |
| `[GMAIL_ACCOUNT_INDEX]` | 0 (first account), 1 (second) | config.md |
