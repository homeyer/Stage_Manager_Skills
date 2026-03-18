# Windsports Calendar Blocker — Holes Analysis

**Analyzed by:** Stage Manager Holes Lens
**Source document:** windsports-calendar-blocker-prd.md

---

## What's Here

This is a focused, honest spec. The target user is specific and real — someone who's missed sessions because a meeting landed on a 20-knot afternoon. The feature set is deliberately small, the technical choices are named, and the decision to skip auth in v1 shows someone thinking about scope. Good bones.

---

## The Holes

### 1. Google Calendar OAuth Without a Backend

> *"Google Calendar API for blocking"*
> *"No backend auth — single-user tool for now"*

**What a coding tool will invent here:**
It will set up a Google OAuth 2.0 consent flow with a client-side-only token stored in localStorage alongside your preferences. It'll use an implicit grant flow (or PKCE if you're lucky), request `calendar.events` scope, and handle token refresh by... probably not handling it. When the token expires (1 hour for Google), the app will silently fail to create events until the user re-auths. The tool won't build a refresh token flow because that requires a backend, and you said no backend.

**Why this matters:**
The core value proposition — *automatic* calendar blocking — breaks silently every hour without a token refresh strategy.

**The question to answer before building:**
Does "no backend" mean no server at all, or could a lightweight edge function (Cloudflare Worker, Vercel serverless) handle token refresh so the automation actually stays alive?

---

### 2. When Does the Blocking Actually Run?

> *"Auto-block Google Calendar slots when forecast meets threshold"*

**What a coding tool will invent here:**
It will create a "Check Forecast" button that, when clicked, scans the 10-day forecast and creates calendar events for matching windows. There will be no scheduler, no cron, no polling loop. "Automatic" will mean "happens when you open the app." The tool has no spec for when or how often this runs, so it will make it manual and call it done.

**Why this matters:**
A tool you have to remember to open every morning to block your calendar is a tool you'll stop using in a week.

**The question to answer before building:**
How does this run without you? Browser tab left open with a polling interval? A scheduled cloud function? A cron job? This is the difference between a demo and a tool.

---

### 3. What Gets Created on the Calendar — and What Happens When Forecasts Change?

> *"Auto-block Google Calendar slots when forecast meets threshold"*

**What a coding tool will invent here:**
It will create all-day events or full-hour blocks titled something like "Wind Block — [Location]" with no description. When the forecast changes (wind dies, storm shifts), those stale blocks will stay on your calendar. There's no reconciliation logic. No event IDs stored for cleanup. No update-or-delete cycle.

**Why this matters:**
After a week, your calendar will have phantom wind blocks for sessions that never materialized. Colleagues will see blocked time that isn't real. Trust in the tool — and your calendar — erodes fast.

**The question to answer before building:**
When the forecast changes, what happens to blocks that were already created? Do they get updated, deleted, or flagged?

---

### 4. What Hours Count as "Blockable"?

> *"Auto-block Google Calendar slots when forecast meets threshold"*

**What a coding tool will invent here:**
It will block every hour the wind is above threshold — including 2 AM, 6 AM, and your kid's recital at 4 PM. The spec says nothing about working hours, preferred session windows, or existing calendar conflicts. The tool will create blocks that overlap with events you already have.

**Why this matters:**
The first time this blocks over an important meeting, the user turns it off forever.

**The question to answer before building:**
What hours are eligible for blocking, and should existing calendar events be treated as immovable?

---

### 5. Wind Speed Alone Doesn't Make a Session

> *"Fetch 10-day wind forecasts from OpenWeatherMap API for saved GPS locations"*
> *"User sets personal wind threshold (5-40 mph range)"*

**What a coding tool will invent here:**
It will pull wind speed and maybe wind direction from OpenWeatherMap's free tier — which returns data in 3-hour intervals, not hourly. It won't account for wind direction (offshore vs. onshore matters enormously), gust factor, tide, or swell. A 20mph offshore wind at a kite beach is unflyable. The tool won't know that.

**Why this matters:**
False positives (blocking for bad sessions) will make the user distrust the tool. Windsports people are picky about conditions for good reason.

**The question to answer before building:**
Is wind speed alone sufficient for v1, or does wind direction relative to the spot need to be a minimum filter? And does OpenWeatherMap's 3-hour resolution give you enough precision to block meaningful time windows?

---

### 6. What Happens to localStorage When There Are Five Spots and 10 Days of Forecast Data?

> *"Persist settings in localStorage (no auth required for v1)"*
> *"Support up to 5 saved locations with nicknames"*

**What a coding tool will invent here:**
It will store user preferences, 5 locations, 10 days of forecast data per location, and created calendar event IDs all in localStorage. It won't handle the case where localStorage is cleared (browser settings, incognito, different device). All your spots, thresholds, and pending blocks disappear with no recovery path.

**Why this matters:**
localStorage is a convenience store, not a database. The moment someone clears their browser data, they lose their setup and have orphaned events on their calendar with no way to clean them up.

**The question to answer before building:**
Is localStorage the persistence layer for preferences only (recreatable), or is it also the system of record for which calendar events were created (not recreatable)?

---

## The Pattern You're Seeing

The holes cluster around the **automation lifecycle** — not what the tool does in a single moment, but what happens over time. The spec describes the *first run* well. It doesn't describe the second run, the tenth run, or what happens when conditions change between runs.

---

## What's Ready to Build

- **The forecast display UI** — 5 locations, color-coded wind speeds, threshold slider. Clean, self-contained, no lifecycle questions.
- **The location management** — saving GPS coordinates with nicknames, localStorage for preferences only.
- **The OpenWeatherMap integration** — fetching and parsing forecast data for display.

---

## The One Move

Write a one-paragraph description of what happens on **Day 3** — the app has been running, forecasts have shifted, two blocks from yesterday are now wrong, and the user opens the app. What do they see? What already happened? That paragraph will close most of these holes at once.

---

*Analysis produced by the Stage Manager Holes Lens*
*github.com/Mnfst-AI/Stage_Manager_Skills*
