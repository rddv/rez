# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static PWA mock of the Ukrainian "Резерв+" (Reserve ID) mobile app. No build system, no dependencies, no package manager — plain HTML/CSS/JS served as static files. All UI text is Ukrainian (`lang="uk"`).

## Running

Serve the directory over HTTP (service workers require HTTP, not `file://`):

```bash
python -m http.server 8080
# or
npx serve .
```

Then open `http://localhost:8080/`.

When iterating on [index.html](index.html), bump `CACHE_NAME` in [sw.js](sw.js) (or unregister the service worker in DevTools → Application) — otherwise the SW will serve stale cached assets.

## Architecture

Three files carry all the logic:

- [index.html](index.html) — entire app: markup, inline `<style>`, and inline `<script>`. User data lives in the `USER_DATA` object near the bottom of the script; `render()` writes it into DOM nodes by `id`. A `PLACEHOLDER_PHOTO` inline SVG is used as fallback when `photo.jpg` fails to load.
- [manifest.json](manifest.json) — PWA manifest (standalone display, portrait, theme `#C9C5A6`).
- [sw.js](sw.js) — service worker. Cache-first with network fallback; pre-caches `index.html`, manifest, and icons on install. Note: `photo.jpg`, `photo1.jpg`, and `icons/herb.jpg` are NOT in the pre-cache list — they're only cached opportunistically on first fetch.

Layout is mobile-first with a `max-width: 480px` centered column; above 481px the background turns black to frame the "phone" view. Safe-area insets (`env(safe-area-inset-*)`) are used for iOS notch/home-indicator handling.
