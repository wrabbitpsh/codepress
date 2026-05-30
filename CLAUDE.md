# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**부산 AI·DX 지원사업 문의 포털** — A single-page inquiry portal for BIPA (Busan Information Technology Agency). Educational/demo project: no backend, no build tooling.

Full requirements are in [docs/PRD.md](docs/PRD.md).

## Tech Stack

- **Single HTML file** — no build step, no package manager, no framework
- **Tailwind CSS v3** via CDN
- **SheetJS (xlsx 0.20.3)** via CDN for Excel import/export
- **Google Fonts** — Noto Sans KR
- **localStorage** for persistence (`bipa-portal:inquiries`)

## Running the App

Open `index.html` directly in a browser (no server needed). For live reload during development:

```bash
npx serve .          # or
python3 -m http.server 8080
```

## Architecture

Everything lives in a single `index.html`:

- **Data layer** — `localStorage` key `bipa-portal:inquiries`, array of `{id, category, title, name, content, ts}` objects. On first load, seeds 5 sample records. ID sequence starts at `max(existing ids) + 1`, minimum 100.
- **Rendering** — vanilla JS DOM manipulation; no virtual DOM or templating library.
- **Relative timestamps** — refreshed every 30 seconds via `setInterval`.
- **XSS protection** — all user input must be HTML-escaped (`&`, `<`, `>`, `"`) before insertion into the DOM.

## Data Model

```json
{ "id": 100, "category": "AI·DX 솔루션 도입", "title": "...", "name": "...", "content": "...", "ts": 1748573600000 }
```

`ts` is a Unix timestamp in **milliseconds**.

## Key Behaviors

- **Form validation**: inline error below form ("모든 항목을 입력해주세요.") on empty fields; hide error while user is typing.
- **On successful submit**: reset form, reset category filter to "전체", refresh list.
- **Delete**: `confirm()` dialog before removing; update count badge after.
- **Excel export**: full list, newest-first, filename `문의목록_YYYY-MM-DD.xlsx`, date formatted with `ko-KR` locale.
- **Excel import**: parse first sheet; require columns 카테고리/제목/이름/내용 (alert if missing); skip rows with empty 제목 or 이름; prepend to existing list; alert "N건을 가져왔습니다."; reset file input.

## Design Tokens

| Token | Value |
|---|---|
| primary | `#1E40AF` |
| accent | `#0EA5E9` |
| surface | `#F8FAFC` |
| card border-radius | `8px` |

Max content width: `max-w-3xl` (768 px), centered.
