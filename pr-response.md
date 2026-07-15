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
**What I did:** Created `tests/test_watchlist.py`, mirroring `test_add_to_collection_nonexistent_film_raises` from `test_collection.py`. It reuses the same `app` and `sample_user` fixtures and adds `test_add_to_watchlist_nonexistent_film_raises`, which asserts that calling `add_to_watchlist()` with a film_id that doesn't exist raises `FilmNotFoundError`.
**How I verified:** Ran `pytest tests/test_watchlist.py -v` using the project's `.venv` interpreter — 1 passed.

## Comment 4 — Default visibility
**My position:** I'm keeping `public=True` as the default for `WatchlistEntry`.

**Reasoning:** CineLog is described in the README as "a community film tracking app" — the core value of the product is social: seeing what other users are watching, rating, and planning to watch. A watchlist that's private by default is invisible by default, which works against that goal. If most users never touch the visibility setting (which is the common case for any default), a private-by-default watchlist would mean the feature quietly fails to contribute to the discovery/community experience it's meant to support. I'm optimizing for the "browse what people are excited to watch" behavior — low-friction sharing where a user has to actively opt out of visibility rather than opt in. This also keeps `WatchlistEntry` consistent with the rest of the app's posture: nothing else in the collection feature is gated behind a privacy flag, so introducing a private-by-default watchlist would be an inconsistent, unannounced departure from how the rest of the user's activity is treated.

**Tradeoff acknowledged:** The real cost is that a watchlist is a more exposed signal than a collection. A `CollectionEntry` reflects a film someone already watched and (optionally) rated — a completed, considered action. A `WatchlistEntry` reflects intent: films someone hasn't seen yet, which can surface genre preferences, guilty pleasures, or viewing habits the user hasn't decided they're comfortable sharing yet. A user who doesn't realize the default is public could have that list visible before they've thought about who can see it — that's a real privacy cost, not a hypothetical one. If we had evidence that users are surprised by this (e.g., support tickets, or user research showing discomfort), I'd revisit the default rather than treat this decision as final. For now, given the product's social framing and no such signal yet, I'm choosing the default that serves the core discovery use case, with the tradeoff being the un-anticipated visibility risk for users who never check the setting.

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
