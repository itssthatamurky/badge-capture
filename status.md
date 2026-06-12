# Badge Capture — build log
**Project Type:** code
**Git Tracking:** no (OneDrive folder convention; publish via GitHub web upload in Task 12)
**Next CLAUDE.md Review:** 2026-06-29

2026-06-10 — Task 1: shell + tokens.
2026-06-10 — Task 2: storage layer.
2026-06-10 — Task 3: capture flow.
2026-06-10 — Task 4: home view.
2026-06-10 — Task 5: detail view.
2026-06-10 — Task 6: raffle.
2026-06-11 — Task 6 review: spec ✅; quality fixes applied (try/finally guard release, drawGen counter for mid-shuffle abort, confetti rAF cancel, winURL revoke hoist); runtime-verified incl. mid-shuffle pool-switch abort. Deferred to Task 10: SR live region + segment aria-pressed, empty-pool note, confetti DPR. Also fixed serve.ps1 em-dash encoding parse error.
2026-06-11 — Task 7: export + nudge + clear all. Two-stage review done; fixes applied (blobToDataURL onerror→null, exportAll in-flight guard, .view safe-area padding). Runtime-verified: contract keys exact, photo dataURL/null, nudge show/hide, double-confirm clear. Deferred to Task 10: exportAll catch+alert, nudge role=status, nudge-x rename.
2026-06-11 — Task 8: import / desktop viewer. Two-stage review done; fixes applied (imported ids re-keyed via randomUUID — injection sealed; defensive capturedAt sort; loadExport .catch alert). Round-trip test (spec §9) PASSED incl. hostile-id and missing-timestamp files; DB isolation confirmed. Deferred to Task 10: disable detail pills in readOnly (keyboard focus), optional version check.
2026-06-11 — Task 9: PWA (sw.js, manifest, icon.svg + 180/512 PNGs, head links, registration). Two-stage review done; fix applied (SW offline fallback scoped to navigation requests so failed font fetches don't cache HTML). SW verified: activates, all 6 shell entries cached, console clean. Note for Task 12 smoke test: consider 192px manifest icon for older Android; iOS standalone camera-input historically regression-prone — test explicitly.
2026-06-11 — Task 10: hardening pass, all 19 items (plan items A1–A4 + full review backlog B5–E19). Two-stage review done (Opus spec ✅ all 19; Fable quality found 1 fix-now: stale retakeFor after cancelled retake + back could overwrite wrong contact's photo on next snap — fixed: retakeFor cleared in closeDetail + cam cancel listener). Runtime QA passed: nophoto flow, retake-flag lifecycle, save-err banner throttle, empty-pool note, aria-pressed sync, esc apostrophes, console clean. Note tier (accepted, not fixed): openRaffle/clear-btn lack try/catch if openDB failed at boot; save-err banner can appear over home view; new-contact null-compress silently saves photo-less (capture not lost).
2026-06-11 — Task 11: full verification PASSED at 390×844. (1) Home/detail/raffle structurally verified via a11y snapshots — preview_screenshot still times out in this environment (known issue; Mark eyeballs the live preview instead; 5 realistic demo contacts left loaded at localhost:5530). (2) Export→import round-trip re-confirmed post-Task-10: 173KB export of 5 photo contacts re-imported clean, header/rows/thumbs correct, readOnly sealed (fields+pills disabled, bar hidden), phone DB untouched. (3) Reload persistence re-confirmed: keystroke edit survived hard reload (SW+caches cleared), all photos intact. (4) Raffle starred-pool draw verified: correct pool, winner card+photo, SR announcement, confetti, Draw again. Console clean (only intentional quota-test warns in buffer). Deferred: nothing. READY FOR TASK 12 (publish — needs Mark).

---

## 2026-06-10 - End of Day

### Key Decisions
- **Full lifecycle in one session:** brainstorm → spec → 3 mockup rounds → 12-task plan → subagent-driven build of Tasks 1–6.
- **Delivery:** single HTML app hosted on Mark's existing GitHub Pages (rep gets a link, adds to home screen). Contact data never leaves the phone until the rep exports one JSON file. 2FA on the GitHub account is the tamper mitigation.
- **Design picks (Mark, from rendered mockups):** Home = "thumb first" (bottom snap bar); Detail = photo-hero blend with follow-up pill LEFT / icon-only star RIGHT (right-thumb reach); Raffle = "dark stage" theater mode + cyan/violet confetti. Toggle convention app-wide: on = cyan `#81DCE4` with dark-teal icon.
- **Terminology rule (hard):** the flag is `starred`, icon-only — the word "hot" must never appear in UI, data, or code.
- **Raffle repeats allowed:** re-draw may pick a past winner; explicitly OK'd — do not "fix".
- **No in-app OCR:** Claude reads badge photos post-event on Mark's side (intake step, Task 8 viewer supports it).
- **No keystroke debounce on saves:** instant per-keystroke dbPut is the spec mandate; Blob structured-clone is by-reference so it's cheap enough.
- **Styling:** Panaya Web UI Kit (Mona Sans, ink/grey + cyan/violet, pill buttons, flow-lines motif). Kit's `colors_and_type.css` missing from Mark's zip — proceeding on visible tokens.

### Completed
- Spec: `spec-2026-06-10-badge-capture-design.md` (approved, mockup picks folded into §5).
- Plan: `plan-2026-06-10-badge-capture.md` (12 tasks, full code).
- App (`app/index.html`, Tasks 1–6): shell + tokens; IndexedDB layer (`starred` field contract); capture pipeline (camera → compress to ≤300KB via explicit quality ladder → instant save; verified 4.7MB→247KB); home view (list, search, fixed bottom bar); detail view (photo hero, zoom, retake, instant-commit fields — reload-persistence verified; teal toggles, XSS-escaped rendering); raffle (dark stage, All/★ pool, easing shuffle, confetti, empty-pool + double-click guards).
- Reviews: Tasks 1–5 spec+quality reviewed and fixes applied (object-URL leak, float-drift quality loop, box-sizing reset, single-`<main>` validity, stray `app/.claude/` stub deleted before it could ship to Pages).
- Infra: `serve.ps1` QA server (port 5530, launch config `badge-capture`).

### Blockers/Open Items
- **Resume point: Task 6 review.** Raffle is implemented + self-verified by its implementer, but the independent spec+quality review was not yet run (paused at usage limit). Review angles queued: shuffle-loop guard release, confetti rAF overlap on rapid draws, winURL revoke timing, aria-live on result, severity of known mid-shuffle pool-switch issue.
- **Hardening backlog for Task 10** (accumulated from reviews): `#search` aria-label; `openDB().then(renderList)` rejection handling; `esc()` doesn't escape apostrophes (latent); `aria-pressed` missing on `#fup-btn`; `commit()` failure is silent while UI says "Saved automatically" → once-per-minute banner; `cur=null` in closeDetail; raffle in-flight draw not cancelled on pool-switch/back (generation counter); retake-null-blob silent failure feedback.
- `preview_screenshot` times out in this environment (eval/snapshot fine) — retry at Task 11; fallback is Mark eyeballing localhost:5530.
- Unrelated loose thread: `~/.claude/plans/sequential-sleeping-spark.md` (FY27 North Stars animated HTML page, 6/8) is active but belongs to another project — not copied here.

### Next Steps
- [ ] Run Task 6 (raffle) spec+quality review; apply fixes
- [ ] Tasks 7–11: export + nudge + clear-all → desktop viewer → PWA (sw/manifest/icons) → hardening (use backlog above) → full verification with screenshots for Mark
- [ ] Task 12 with Mark: create GitHub repo, web-upload `app/` contents, enable Pages, confirm 2FA, iPhone smoke test, text rep the link + 3-line install instructions

---
