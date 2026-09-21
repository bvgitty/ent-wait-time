# ENT Wait Time

A mobile-first, no-login web page that shows patients the **current wait time** at an ENT clinic, so they can decide when to come in without calling the front desk.

Built as a single self-contained `index.html` with vanilla HTML, CSS, and JavaScript. There is no build step, framework, or dependency to install.

> **Note:** This is a front-end prototype. The clinic ("Willow Creek ENT & Sinus Center"), its phone number, and its address are placeholders, and the wait-time data is simulated in the browser. See [Connecting real data](#connecting-real-data) for how to wire it up to a live source.

## Why this exists

Most clinic wait-time pages assume a tech-comfortable user with a login. This one is designed around the opposite case: a patient, possibly in their 70s or 80s, possibly unwell, glancing at their phone in a parking lot. That drives a few decisions:

- **The answer comes first.** The current wait is the first thing on the page, with nothing above it but the header.
- **No account, no forms, no data collection.**
- **Zoom is never blocked**, and there's a built-in text-size control.
- **Status is never conveyed by color alone.** Every state also has a text label.

## Features

**Live status**
- Large, color-coded wait time in minutes, plus the number of patients ahead of you and today's closing time
- Five states: low (under 15 min), moderate (15-30 min), long (over 30 min), closed, and data unavailable
- Auto-refreshes every 5 minutes with a visible countdown and a manual "Refresh now" button
- A stale-data warning appears if a reading is more than 15 minutes old
- When the clinic is closed, a callout shows when it next opens along with the full weekly hours

**Planning ahead**
- "Typical wait by time of day" bar chart with a day-of-week picker
- The current hour is flagged on today's chart
- "Best times to walk in" summarizes the consecutive hours that typically run under 15 minutes

**Quick actions**
- One-tap call, directions (Google Maps), and jump-to-hours

**Accessibility**
- Skip link to the current wait time
- Screen-reader-friendly live region announces status changes
- Chart values appear on tap or keyboard focus (not hover-only), and a full text list of every bar is provided for screen readers
- Three text sizes (100%, 115%, 130%)
- Light and dark themes: follows the OS setting by default, with a manual override switch
- Visible focus states and `prefers-reduced-motion` support

**Privacy**
- Guest-only, nothing to log out of
- Collects no names, symptoms, or health information
- The only thing stored is per-device convenience preferences in `localStorage` (text size, theme, and whether you dismissed the bookmark tip)

**Discoverability**
- Page metadata, plus [schema.org](https://schema.org/MedicalClinic) `MedicalClinic` JSON-LD in the `<head>`
- Home-screen / PWA-style meta tags for iOS and Android

## Getting started

Open `index.html` in any modern browser. That's it.

Or serve it locally:

```bash
git clone https://github.com/bvgitty/ent-wait-time.git
cd ent-wait-time
python3 -m http.server 8000
# then visit http://localhost:8000
```

Because it's a static file, it can be hosted anywhere static files can be served (GitHub Pages, Netlify, S3, etc.).

### Previewing states

At the bottom of the page there's a collapsed **"Preview other scenarios"** panel (marked as build/testing only). It lets you switch between Live, Low, Moderate, Long, Closed, and Data unavailable so you can see every state without waiting for the real conditions. Remove the `<details class="demo-panel">` block before shipping to patients.

## Project structure

```
ent-wait-time/
├── index.html   # markup, styles, and script in one file
└── README.md
```

Inside `index.html`, the script is organized into small sections: text-size control, dark-mode toggle, hours rendering, the mock live-wait generator, status plate rendering, the day picker and chart, "best times" logic, and the demo panel.

## Connecting real data

Two pieces of data are mocked in the script block:

| What | Where | Replace with |
| --- | --- | --- |
| Live wait and queue length | `computeLiveMock()` | A `fetch()` to your queue-management system or API, returning minutes and patients ahead |
| Typical waits by hour | the `TYPICAL` object | Averages from your historical visit analytics |

`renderPlate()` already handles the closed and error states, so a failed request can map to the existing "Wait time unavailable" view (the `error` scenario) with a "Try again" button.

## Customizing for a clinic

Replace the placeholders throughout `index.html`:

- Clinic name (`<title>`, header, JSON-LD)
- Phone number (`tel:` link and JSON-LD)
- Address (Directions link and JSON-LD)
- Weekly hours (`HOURS_OPEN`)
- Color tokens, defined as CSS variables at the top of the stylesheet (`--brand`, `--low`, `--moderate`, `--long`, etc.)

For the page to surface in searches like "[clinic name] wait time", pair it with a real custom domain, a sitemap entry, and a link from the clinic's Google Business Profile.

## Tech notes

- Vanilla HTML/CSS/JS, no build tooling
- The only external request is the [Public Sans](https://fonts.google.com/specimen/Public+Sans) web font from Google Fonts, with system-font fallbacks
- Responsive, single-column layout capped at 520px

## Disclaimer

Wait times shown by a real deployment would be estimates and can change quickly. The page includes an emergency notice directing patients to call 911 or go to the nearest ER for medical emergencies.
