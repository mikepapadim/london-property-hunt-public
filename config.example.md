# Property Hunt — Personal Config

> Copy this file to `config.md` and fill in your details.
> `config.md` is gitignored — your personal info stays local.

---

## About you

```
YOUR_NAME=Alex
YOUR_AGE=29
YOUR_PROFESSION=Software Engineer
YOUR_PROFILE_DESCRIPTION=easy-going, clean, tidy, cooks, gym, hybrid WFH 2 days/week, no parties, professional
YOUR_PROFILE_SUMMARY=Clean, tidy, reliable — hybrid WFH, permanent contract
```

## Commute

```
YOUR_WORK_POSTCODE=EC2A 1NT
PARTNER_AREA=Hackney Central
TUBE_LINE=Victoria Line
```

## Target areas

List your areas in priority order, comma-separated.
The skill will use these to set priority tiers and generate the SpareRoom/OpenRent search URLs.

```
PRIMARY_AREAS=Hackney, Shoreditch, Bethnal Green, Clapton
SECONDARY_AREAS=Dalston, Stoke Newington, Islington
```

Tip: pick areas on the same tube/rail line as your work and social anchor points.
The shorter the line change, the better.

## Budget

```
ROOM_BUDGET=1500
ROOM_BUDGET_NO_BILLS=1700
STUDIO_BUDGET=1900
```

## Dates

```
MOVE_IN_DATE=1 June 2026
MOVE_IN_MONTH=June
```

## Flatmate preferences

```
FLATMATE_MIN_AGE=28
FLATMATE_MAX_AGE=40
```

## Email

```
YOUR_EMAIL=you@gmail.com
GMAIL_ACCOUNT_INDEX=0
```

`GMAIL_ACCOUNT_INDEX`: Gmail MCP uses account index to identify which Gmail account to use.
- `0` = your first/primary Google account signed in
- `1` = second account (useful if your hunt email is different from your main account)

## File paths

```
YOUR_HUNT_DIR=~/my-room-hunt
```

The skill will:
- Read/write `$YOUR_HUNT_DIR/london_room_hunt.xlsx`
- Save outreach files to `$YOUR_HUNT_DIR/outreach/`

Make sure the directory exists before running:
```bash
mkdir -p ~/my-room-hunt/outreach
```

---

## SpareRoom search URLs

After filling in your areas above, generate your SpareRoom room search URLs.
Format: `https://www.spareroom.co.uk/flatshare/london/[area_slug]?max_rent=[ROOM_BUDGET]&sort=posted_date&mode=list`

Area slugs use underscores: `elephant_and_castle`, `canada_water`, `bethnal_green`, etc.

```
SPAREROOM_ROOM_URLS=
  https://www.spareroom.co.uk/flatshare/london/hackney?max_rent=1500&sort=posted_date&mode=list
  https://www.spareroom.co.uk/flatshare/london/shoreditch?max_rent=1500&sort=posted_date&mode=list
  https://www.spareroom.co.uk/flatshare/london/bethnal_green?max_rent=1500&sort=posted_date&mode=list
```

SpareRoom studio/flat search URLs:
```
SPAREROOM_STUDIO_URLS=
  https://www.spareroom.co.uk/flats-to-rent/london/hackney?max_rent=1900&sort=posted_date&mode=list
  https://www.spareroom.co.uk/flats-to-rent/london/shoreditch?max_rent=1900&sort=posted_date&mode=list
```

---

## Notes

- You can update this file any time — just re-run the skill and it picks up changes
- If you find a place and want to stop the scheduled runs, delete the schedule: `/schedule list` then `/schedule delete [id]`
