# Name Wheel · 点名转盘

Offline random name picker for the classroom. One HTML file, no install, no internet.
The whole interface works in **English and 简体中文** (中 / EN button).

## USB setup

1. Copy `NamePicker.html` to your USB stick.
2. On any Windows computer: double-click `NamePicker.html` (opens in Edge/Chrome).
3. First open shows a sample class — load your own roster to replace it.

## Rosters

Two formats, loaded with **Load roster…** or by dragging the file onto the window:

- **`.txt`** — one name per line (edit in Notepad). The filename becomes the class name.
- **`.xlsx` from SchoolPal** — export the class 学生名单 and load it directly, no
  conversion needed. Names come from the 学生/Students column; the class name is read
  from the 班级/Class row (falling back to the filename). Works offline — the file is
  parsed in the browser.

Each file becomes a class in the sidebar list. Click a class to switch; ✕ removes it
(its pick history is kept).

## Keeping data on the USB

Classroom computers are often shared and may be wiped, so treat the USB as the only
real home of your data:

- When the banner asks, **Open your data file** (`namewheel-data.json` on the USB) —
  then every change saves to it automatically. First time: **Create data file**.
  (Requires Edge/Chrome; one click per session.)
- On browsers without file access, use **Save data file** / **Import…** in the 📋
  panel instead. The saved JSON carries all classes, history, and settings.

## Using it

- **SPIN** (or Space) picks a student — fairly, via crypto randomness.
- **Remove picked names** (on by default) takes winners off the wheel; **Reset round** puts everyone back.
- Uncheck a student to mark them **absent** for the day.
- **Favor least-picked** (off by default) gives less-picked students better odds —
  slice sizes always show the true probability, so the bias is never hidden.
- **⏱** switches to a full-screen classroom countdown timer (presets or custom mm:ss;
  Space starts/pauses it).
- **🌙** dark mode. **Shuffle** reorders the wheel. **☰** hides the roster panel for
  the projector; **F** = fullscreen; 🔊 mutes. **?** opens bilingual help.

## History & equity

📋 shows every pick and per-student counts (least-picked first), scoped to the active
class. **Export CSV** opens in Excel/Sheets.

## Privacy (PIPL)

Student names never leave the computer or the USB drive. The app has no network code
at all — nothing is uploaded, ever. Keep roster files and `namewheel-data.json` on
your USB only, and never commit them to a public repository.
