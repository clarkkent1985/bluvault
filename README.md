# 💿 BluVault

A progressive web app (PWA) for managing a physical Blu-ray / 4K UHD / DVD disc collection — plus Jellyfin NAS integration. Single `index.html`, no backend, no build step.

**Live:** [clarkkent1985.github.io/bluvault](https://clarkkent1985.github.io/bluvault)

\---

## Features

### 📦 Collection

* 3-column responsive grid (auto-scales to screen width: 3→8 columns)
* Virtual scroll — handles thousands of films without lag
* Info panel on every card: source (💿 / NAS), format (4K/BD/DVD), HDR (DV/HDR10/SDR), audio, 🇭🇺 flag
* List view + S/M/L/XL poster size selector
* Multi-filter + sort (title, year, rating, format, HDR, Steelbook, watched status, source)
* Search with history (last 10 queries)
* Long-press (mobile) / right-click (PC) context menu

### 🎬 Film Details

* Hero backdrop + poster (language-aware: HU poster if available, EN fallback)
* Clickable cast → Person view (all films in collection, profile photo, character name)
* Clickable director → Person view
* Clickable genres → genre filter
* Sections: Release info, Overview, Cast, Genres, Studio, Audio tracks, Trailer (YouTube), bluray.com link, NAS/Jellyfin info, Similar films
* Version tabs (e.g. theatrical + extended + 4K editions of same film)
* Swipe left/right (mobile) / arrow keys (PC) to navigate between films
* Position indicator (e.g. "12 / 337")

### 🎭 Audio

* Interactive audio picker: language flag chips + format chips (Atmos, TrueHD, DTS-HD MA, PCM, DD+, etc.)
* LOSSLESS / OBJECT / LOSSY quality badge
* Auto-populated from Jellyfin MediaStreams on sync

### 📺 Series

* Series view: season cards with progress bars
* Season view: episode list with overview + runtime + watched toggle
* Auto-mark series as watched when all episodes done
* Jellyfin episode fetch (title, overview, runtime, watched status)
* Physical disc extras: featurette, making-of, deleted scenes, commentary, interview

### 🔍 Discovery

* **🎲 Random picker** — "What to watch tonight?" with filters (unwatched, 4K, HU audio, NAS, disc, etc.)
* **🎬 Franchise timeline** — 47 built-in franchises (MCU, Bond, Star Wars, Nolan, LOTR, Bourne, Alien, DC, Jurassic, Terminator, Indiana Jones, Mission Impossible, Fast \& Furious, Spider-Man, Harry Potter, John Wick, and more), powered live from TMDB Collection API with 7-day cache. Add custom franchises by TMDB Collection ID or name search.
* **🎬 Similar films** — scored by shared director (+4), genres (+2), studio (+1), decade (+1), collection (+3)
* **🖼️ Showcase / Wall view** — fullscreen poster grid (S/M/L/XL sizes), click for detail

### 📊 Statistics

* 8 KPI cards (total, 4K, DV, Steelbook, HU audio, watched, avg rating, poster coverage)
* Bar charts: format breakdown, top directors, top genres, by decade, release country, audio formats, studios
* Fun facts (oldest/newest film, most discs, top director, Steelbook %, Atmos count)
* **🎬 Movie of the Day** — 3 layers: premiere anniversaries from collection, 25-entry cultural calendar (Star Wars Day, Halloween, Back to the Future Day, etc.), TMDB "released on this day in history" with collection ownership badges. Daily cache.

### 🔎 TMDB Wizard

* Poster Fix Wizard: slides through all films missing posters, auto-searches TMDB, shows 8 candidates with posters, apply saves all TMDB data
* TMDB Data Wizard: same flow for films without tmdb\_id
* Single-film wizard from detail view (🔍 button)
* After enrichAll, auto-offers wizard for remaining misses
* `tmdb\_fixed: true` flag prevents future overwrites

### 📀 bluray.com Import

* Import CSV exported from bluray.com My Movies
* Matches by EAN/UPC barcode first, then title
* Updates: studio, casing (Steelbook/DigiPack), disc count, slipcover, release date, ASIN, notes
* Adds new items not yet in DB
* Offers TMDB enrichment for new items
* bluray.com search link stored per item, shown in detail view

### ☁️ Sync \& Backup

* **Google Drive** — saves `bluvault\_db.json`, auto-save 10s after change, token auto-refresh
* **Jellyfin** — full sync: HDR detection (DV/HDR10/HLG), resolution label (4K/1080p/720p), source type (Remux/WEB-DL), structured audio tracks, watched status (Played + PlaybackPositionTicks), season/episode data
* **Manual backups** — up to 10 named snapshots in localStorage, restore with pre-restore auto-backup
* **JSON export** — full database download
* **CSV/JSON export** — collection export
* **XLSX import** — column-name matched import
* Auto-backup every 50 saves + before major operations (enrichAll, bluray import)

### 🔒 Security \& PWA

* PIN lock (4-digit) with auto-lock on app background
* Offline detection banner + silent Drive sync on reconnect
* iOS localStorage limit warning + auto-slim (drops nas\_info for NAS-only items)
* Service Worker: app shell cache + persistent poster cache (TMDB + Jellyfin images never auto-deleted)
* PWA install prompt (Android/Chrome/Edge) + iOS instructions
* Hourly SW update check with toast notification

### 💸 Utilities

* **Sellable discs** — finds Blu-ray discs where you also own the 4K version
* **Duplicate finder** — groups by normalized title
* **Missing data report** — counts: no poster, no TMDB, no barcode, no audio, no country
* **Cache management** — size display, batch cache all posters, clear cache

\---

## Tech Stack

|||
|-|-|
|**Frontend**|Pure HTML/CSS/JS — no framework, no build step|
|**Data**|localStorage (`bluvault\_db`, `bluvault\_settings`) + Google Drive `bluvault\_db.json`|
|**APIs**|TMDB (posters, cast, genres, studios, trailers, collections), Google Drive OAuth2, Jellyfin REST|
|**PWA**|Service Worker via Blob URL (app shell + poster cache), Web App Manifest|
|**Barcode**|Native `BarcodeDetector` API + inline EAN-13 decoder|

\---

## Setup

1. Open [clarkkent1985.github.io/bluvault](https://clarkkent1985.github.io/bluvault)
2. First-run wizard asks for:

   * **TMDB API key** — free at [themoviedb.org](https://www.themoviedb.org/settings/api)
   * **Google OAuth Client ID** — for Drive sync (optional)
3. Optional: connect Google Drive in Settings → ☁️ sync
4. Optional: set Jellyfin server URL + API key in Settings → 🖥️

\---

## Database Schema

```js
{
  id, type,                          // 'Film' | 'Sorozat'
  title\_hu, title\_en,
  year, director, cast, cast\_detailed, genres,
  country, bluray\_country, release\_year, release\_date,
  format, edition, casing, slipcover,
  hdr, discs, dvd\_discs,
  hu\_sync, hu\_sub,
  source,                            // 'disc' | 'nas' | 'both'
  barcode, upc, asin,
  poster\_url, poster\_url\_hu, poster\_url\_en, cover\_url,
  tmdb\_id, tmdb\_fixed,
  overview, studios, trailer\_key,
  audio\_tracks,                      // \[{lang, format}]
  watched,                           // null | 'watched' | 'watching' | 'watchlist' | 'want'
  rating,                            // 0–10
  notes,
  versions\_group,                    // links multiple editions of same film
  collection\_name,
  jellyfin\_id, nas\_info,
  bluray\_link, bluray\_title,
  seasons,                           // \[{name, year, disc\_count, episodes\[], extras\[]}]
  extras,                            // \[{type, title, disc}]
}
```

\---

## Deployment

```
Edit index.html → commit to main → GitHub Pages auto-deploys (\~2 min) → hard refresh
```

\---

