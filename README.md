# Name Wheel

Offline random name picker for the classroom. One HTML file, no install, no internet.

## USB setup

1. Copy `NamePicker.html` and the `rosters` folder to the USB stick.
2. On any Windows computer: double-click `NamePicker.html` (opens in Edge/Chrome).

## Rosters

Two formats, loaded with **Load roster…** or by dragging the file onto the window:

- **`.txt`** — one name per line (edit in Notepad). The filename becomes the class name.
- **`.xlsx` from SchoolPal** — export the class 学生名单 and load it directly, no
  conversion needed. Names come from the 学生/Students column; the class name is read
  from the 班级/Class row (falling back to the filename). Works offline — the file is
  parsed in the browser.

## Using it

- **SPIN** (or Space) picks a student — fairly, via crypto randomness.
- **Remove picked names** (on by default) takes winners off the wheel; **Reset round** puts everyone back.
- Uncheck a student to mark them **absent** for the day.
- **Shuffle** reorders the wheel. **☰** hides the roster panel for the projector; **F** = fullscreen; 🔊 mutes.
- **中 / EN** switches the whole interface between English and Simplified Chinese.
  Student names are data, not UI — they always show exactly as written in the roster.

## History & equity

📋 shows every pick and per-student counts (least-picked first). The app remembers
on each computer automatically; to carry history between computers, **Export JSON**
to the USB and **Import…** it on the next machine. **Export CSV** opens in Excel/Sheets.
