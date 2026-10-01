# RollCall (formerly Name Wheel) — USB Handover Release Design

Date: 2026-09-23
Status: Approved

## Context

RollCall is a single-file offline classroom name picker (`RollCall.html`, originally `NamePicker.html`).
It is being handed over to other teachers, English- and Chinese-speaking, who
will each carry it on their own USB drive. Classroom machines are communal and
may be wiped on a schedule, so **no data may be assumed to survive on the
machine**. The USB drive is the source of truth.

Student names are personal information (PIPL): they must never leave the
machine/USB, and roster files are never committed to the repository.

## Scope

In this release:

1. Multi-class list with click-to-switch
2. Single data file on USB (`rollcall-data.json`) with live sync via the
   File System Access API, manual save/import as universal fallback
3. Dark mode
4. Equity weighting ("favor least-picked"), off by default
5. Classroom timer screen (separate from the wheel)
6. Demo roster on first open
7. Bilingual help panel with privacy note

Explicitly out of scope (possible V2, on teacher request): group picker /
team maker, per-pick answer timer, school logo, background images.

Constraints carried forward: one self-contained HTML file, no dependencies,
no network, fully bilingual UI (roster names are data and never translated).

## 1. Data model

One JSON file holds everything:

```json
{
  "app": "rollcall",
  "v": 2,
  "settings": {
    "lang": "en",
    "theme": "light",
    "autoRemove": true,
    "muted": false,
    "weighted": false
  },
  "classes": [
    {
      "id": "c1",
      "label": "26~27 EFAP AP Business with Personal Finance C4",
      "roster": [ { "name": "蔡彤桐 / Sylvia", "absent": false, "removed": false } ]
    }
  ],
  "activeClassId": "c1",
  "history": [ { "name": "蔡彤桐 / Sylvia", "class": "26~27 EFAP ... C4", "ts": 1758600000000 } ]
}
```

- `history` stays flat and keyed by class **label**, identical to v1, so old
  `history.json` exports import unchanged.
- Class `id` is an opaque generated string; `label` is display + history key.
- Migration: on first boot, a v1 localStorage blob (single `roster`,
  `classLabel`) becomes a one-class `classes` array. v1 `history.json`
  imports merge into `history` by the existing `ts|name` dedupe.

## 2. Persistence

Priority: **data file > manual export/import > localStorage**.

- **Live sync (Chrome/Edge, incl. `file://`):** boot shows a slim banner —
  "Working from a USB? Open your data file" → `showOpenFilePicker` (requires
  a user gesture; auto-open is impossible). "Create data file" →
  `showSaveFilePicker` writes a fresh file. After a handle is held, every
  state change writes the full JSON, debounced ~500 ms.
- **Write failure:** toast, retry on next change, manual save still offered.
- **Manual fallback (all browsers):** "Save data file" downloads the full
  JSON; import via the existing drag-drop / Import picker. Kept even when
  live sync is active (doubles as backup). Importing a v1 `history.json`
  still works and merges history only.
- **localStorage:** written best-effort so a mid-session refresh loses
  nothing, but never trusted across sessions. If the File System Access API
  is unavailable, the banner offers manual save/import only.
- File handles cannot persist across page loads on `file://`; re-opening the
  data file once per session is expected behavior.

## 3. Class list UI

- Sidebar top becomes a class list: one row per class; click switches the
  active class (wheel, top-bar label, equity scope all follow).
- Per-class round state (`removed`, `absent`) is stored per class, so
  switching mid-lesson does not reset anything.
- ✕ on a row deletes the class after confirm; its history entries are kept.
- **Load roster…** (txt/xlsx) now *adds* a class. If the incoming label
  matches an existing class label exactly, that class's roster is replaced
  in place (same id, history continuity) instead of duplicated.

## 4. Dark mode

- 🌙 toggle in the top bar; `data-theme="dark"` on `<html>` re-maps the
  existing CSS custom properties. Persisted in `settings.theme`.
- Canvas code (wheel hub, labels, pointer contrast) reads colors from
  computed styles so both themes render correctly. Segment hues stay as-is;
  label ink/stroke adapt.

## 5. Equity weighting

- Checkbox "Favor least-picked" beside "Remove picked names". **Off by
  default.**
- Weight per eligible student: `1 / (picks_in_active_class + 1)`, sampled
  proportionally with the existing crypto RNG.
- **Honesty rule:** when enabled, wheel segment arcs are drawn proportional
  to actual odds — the bias is visible, never silent. When disabled,
  segments are equal and sampling is uniform (current behavior).
- Help panel explains the feature in both languages.

## 6. Timer screen

- ⏱ top-bar button swaps the main area between wheel and timer; sidebar and
  top bar remain.
- Countdown display sized for the back row; presets 1 / 3 / 5 / 10 min plus
  a custom mm:ss input; Start / Pause / Reset.
- At zero: chime (respects mute) + flashing display until dismissed.
- Wall-clock based (like the spin animation) so it keeps time in hidden tabs.
- Space = start/pause on the timer screen; Space = spin only on the wheel
  screen. Nothing about the timer is persisted.

## 7. Demo roster

- If boot finds no data file, no localStorage state, and no classes: load a
  built-in sample class ("Sample Class / 示例班级") with obviously fictional
  bilingual names so the wheel spins immediately.
- It is a normal class in the list; the teacher deletes it like any other.

## 8. Help panel

- **?** top-bar button opens a bilingual modal (follows current UI
  language): quick start, USB data-file explanation, keyboard shortcuts,
  and a privacy note: *student names never leave this computer/USB; nothing
  is uploaded anywhere.*
- The same privacy note is added to the README, along with the instruction
  never to commit roster files.

## i18n

Every new UI string is added to both dictionaries (`en`, `zh`). Roster
names, class labels, and history entries remain untranslated data.

## Error handling summary

| Failure | Behavior |
| --- | --- |
| FS Access API missing | Banner offers manual save/import only |
| Data file write fails | Toast, retry on next change |
| Data file JSON corrupt on open | Toast, file ignored, in-memory state kept |
| localStorage blocked | Silent (existing behavior) |
| Roster/xlsx parse failures | Existing toasts unchanged |

## Testing

No test infrastructure exists in the repo; verification is manual in the
browser pane: boot/migration, file open/create/sync, class switching, both
themes, weighted vs uniform draws, timer accuracy across hidden tabs, both
languages, demo roster, help panel.
