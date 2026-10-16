# OpenOverlay

Transparent iRacing telemetry overlays. OpenOverlay displays live race data in
transparent, always-on-top windows above iRacing: pedal inputs, fuel state, the
cars immediately around you with live gaps, full standings, a side-by-side
proximity warning and a rolling input graph. It reads iRacing's shared memory
directly. No extra tools, no mods, no injection into the game.

Requirements: Windows 10 or 11 (64-bit) and iRacing. Run iRacing in
"borderless windowed" mode, not in exclusive fullscreen (see Installation,
step 3).

## Contents

1. Installation for users (portable exe)
2. Installation for developers (from source)
3. First launch
4. Daily use
5. The overlays and their options
6. Settings file reference
7. Troubleshooting
8. How it works
9. Building the exe
10. FAQ
11. License

---

## 1. Installation for users (portable exe)

You need: Windows 10 or 11, 64-bit. No admin rights, no installation.

1. Get the file OpenOverlay-v0.1.0.zip (from the GitHub releases page or from
   whoever gave it to you).
2. Right-click the zip and choose "Extract all". Pick any folder, for example
   C:\Users\YourName\OpenOverlay.
3. Prepare iRacing:
   Start the iRacing UI, go to Options, then Graphics, and set
   "Window Mode" to "Windowed (Borderless)". This is important: overlays
   cannot appear over an exclusive fullscreen game.
4. Double-click OpenOverlay.exe. Nothing else is needed; the program lives in
   its folder and stores its settings in
   C:\Users\YourName\AppData\Roaming\OpenOverlay.
5. If Windows SmartScreen shows a warning ("Windows protected your PC"):
   click "More info", then "Run anyway". This appears for any unsigned
   program; OpenOverlay is a normal local application.

To uninstall: delete the folder and (optionally) the settings folder
%APPDATA%\OpenOverlay. Nothing else is installed on your system.

## 2. Installation for developers (from source)

You need Node.js 18 or newer (https://nodejs.org, the LTS version is fine)
and npm, which comes with Node.js.

1. Get the source: either clone with git
        git clone https://github.com/YourName/OpenOverlay.git
   or download the source zip from GitHub and extract it.
2. Open a terminal in the project folder.
3. Install the dependencies:
        npm install
   This downloads Electron (~100 MB) and two small libraries. It happens
   once and requires no compiler.
4. Start the app:
        npm start          (real telemetry)
        npm run sim        (simulated telemetry, for trying it out)
5. Useful extra commands:
        npm run dump       CLI diagnostic, prints connection state and live values
        npm run dist       builds the portable exe into dist\

## 3. First launch

1. Start OpenOverlay.exe (or npm start).
2. Start iRacing and enter a test session or race.
3. Within a few seconds the overlay windows fill with live data. Until then
   they show "waiting for iRacing..."; the app retries automatically
   every 2 seconds.
4. Press Ctrl+Shift+O to enter alignment mode: every overlay gets a red
   dashed outline, you can drag windows with the mouse and resize them by
   the edges.
5. Press Ctrl+Shift+O again: overlays lock and the positions are saved.

## 4. Daily use

- Start OpenOverlay, start iRacing, drive. That is all.
- Overlays are click-through while alignment mode is off, so clicks go to
  the game.
- The tray icon (bottom right, green ring) opens the menu:
        Settings                       all options in one window
        Unlock / reposition overlays   same as the hotkey
        Quit                           close the app
- The Settings window has tabs for every overlay plus a Theme section
  (accent color, font, text shadow). Changes apply immediately and are
  saved automatically.
- The hotkey can be changed in Settings, General, "Record".

## 5. The overlays and their options

Pedals
    Throttle, brake and clutch bars, a steering marker and the current gear.
    Options: steering display on/off, gear display on/off.

Fuel
    Fuel remaining, estimated laps remaining, last and best lap time.
    Options: lap times on/off, warning threshold in laps.

Relatives
    The cars immediately ahead of and behind you with live gaps in seconds,
    plus a PIT flag for cars on pit road.
    Options: how many cars ahead/behind, gap decimals, PIT column on/off.

Standings
    The full running order with driver names and gaps to you.
    Options: max rows, car numbers, gap decimals, PIT column on/off.

Car Left/Right
    Side bars that appear only when a car is alongside. A segment on the bar
    shows where the other car is: behind you (bottom), level (middle) or
    ahead (top). Left bar, right bar or both; thicker bar when two cars are
    on one side.
    Options: bar width, color for the two-car state.

Telemetry Graph
    Rolling traces of throttle, brake and steering (clutch optional) over
    the last N seconds, drawn on a canvas at 30 fps.
    Options: time window in seconds, each line on/off, line width.

All overlays share: position (X/Y), size (width/height), background opacity
and font size.

## 6. Settings file reference

File location: %APPDATA%\OpenOverlay\settings.json
Edit by hand only while the app is closed; the app overwrites the file when
settings change.

{
  "configHotkey": "Control+Shift+O",
  "simMode": false,
  "theme": {
    "accent": "#43d675",
    "fontFamily": "Segoe UI",
    "textShadow": true
  },
  "overlays": {
    "pedals": {
      "enabled": true,
      "x": 40, "y": 40, "w": 260, "h": 110,
      "style": { "opacity": 0.72, "fontSize": 13,
                 "showSteering": true, "showGear": true }
    },
    "fuel": {
      "enabled": true, "x": 40, "y": 170, "w": 240, "h": 96,
      "style": { "opacity": 0.72, "fontSize": 13,
                 "showLapTimes": true, "warnLaps": 2 }
    },
    "relatives": {
      "enabled": true, "x": 320, "y": 40, "w": 340, "h": 320,
      "style": { "opacity": 0.72, "fontSize": 13,
                 "rows": 3, "showPit": true, "gapDecimals": 1 }
    },
    "standings": {
      "enabled": true, "x": 680, "y": 40, "w": 360, "h": 380,
      "style": { "opacity": 0.72, "fontSize": 13,
                 "rows": 20, "showPit": true, "gapDecimals": 1,
                 "showCarNumber": false }
    },
    "carLeftRight": {
      "enabled": true, "x": 640, "y": 300, "w": 380, "h": 180,
      "style": { "opacity": 1, "fontSize": 13,
                 "barWidth": 4, "twoCarColor": "#ff4d5e" }
    },
    "inputsGraph": {
      "enabled": true, "x": 1040, "y": 40, "w": 420, "h": 160,
      "style": { "opacity": 0.72, "fontSize": 13, "seconds": 20,
                 "showThrottle": true, "showBrake": true,
                 "showSteering": true, "showClutch": false,
                 "lineWidth": 2 }
    }
  }
}

Notes:
- The theme accent color is used by all overlays (bars, highlight row,
  steering marker). Throttle stays green and brake stays red on purpose:
  those colors carry meaning.
- Sim mode feeds synthetic data with the same structure as real telemetry,
  so every overlay can be tested without iRacing.

## 7. Troubleshooting

Overlays show "waiting for iRacing..." for a long time
    iRacing must be inside a session (test drive, race, AI session). From
    the main menu the shared memory sometimes does not exist yet. Keep the
    app open; it retries every 2 seconds.

Nothing is visible while driving
    Use borderless windowed mode (see Installation, step 3). Overlays cannot
    display over exclusive fullscreen.

Overlays are behind the game window
    Toggle alignment mode off/on once, or restart the app. This re-asserts
    the always-on-top state.

The hotkey does nothing
    Another program has registered the same key combination. Choose a
    different hotkey in Settings.

Positions are wrong after a monitor change
    Open Settings and correct X/Y, or drag the overlays again in alignment
    mode.

Checking the telemetry connection
    In the source folder run:  npm run dump
    It prints the connection state, the variable count and live values.

## 8. How it works

iRacing writes every telemetry frame into a shared memory block
("Local\IRSDKMemMapFileName", about 1.2 MB). The OpenOverlay main process
opens that block with the Windows API (via the koffi FFI library, no
compiler needed), reads the 112-byte header and the 144-byte variable
descriptors, and decodes the newest fully written telemetry row - only when
its tick changed, up to 60 times per second. Session info (driver names,
track, session state) is a YAML text inside the same memory.

The decoded data is broadcast over Electron IPC to the overlay windows,
which are transparent, click-through, always-on-top browser windows.

## 9. Building the exe

In the source folder:

        npm install
        npm run dist

The result is dist\OpenOverlay.exe, a portable single file. To share it,
use the ready-made package in release\, or create your own zip with the exe,
README.md, QUICK-START.txt, DESCRIPTION.txt and LICENSE.

## 10. FAQ

Does this break iRacing rules?
    It reads the same public telemetry data that official spectator tools
    use and draws its windows above the game instead of injecting code into
    it.

Does it work with other sims?
    No. The shared-memory format is specific to iRacing.

Why Electron?
    The overlays are HTML/CSS windows. Electron provides transparent,
    click-through, topmost windows without any native code to compile.

Where do the settings live?
    %APPDATA%\OpenOverlay\settings.json

## 11. License

MIT. See the LICENSE file.
