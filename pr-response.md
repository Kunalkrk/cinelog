# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to match the project's `verb_to_noun` convention (`add_to_collection()`, `remove_from_collection()`). Updated the one call site in `routes/watchlist/watchlist.py` (both the import and the function call).
**How I verified:** Ran a project-wide search (`grep -rn save_to_watchlist`) after the rename and confirmed zero remaining references to the old name.

## Comment 2 — Deduplication
**What I did:** Added an `AlreadyInWatchlistError` exception to `services/watchlist_service.py`, mirroring `AlreadyInCollectionError` in `collection_service.py`. `add_to_watchlist()` now queries for an existing `WatchlistEntry` with the same `user_id`/`film_id` before inserting, and raises `AlreadyInWatchlistError` if one is found — same pre-check-then-raise pattern as `add_to_collection()`.
**How I verified:** Read through `add_to_collection()` in `collection_service.py` to confirm the pattern (exception class + `.filter_by(...).first()` check before insert) and mirrored it exactly.

## Comment 3 — Missing test
**What I did:**
**How I verified:**

## Comment 4 — Default visibility
**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->
