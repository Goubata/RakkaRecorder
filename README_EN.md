# RakkaRecorder 
RakkaRecorder - Free PerfRecorder

JA   https://github.com/Goubata/RakkaRecorder/blob/main/README.md

CN   https://github.com/Goubata/RakkaRecorder/blob/main/README_CN.md


A free Android app that measures and records the performance of games and apps.
View FPS, CPU/GPU usage, temperatures, and battery consumption all in one place.
Works with both Root and Shizuku.

> ⚠️ Source code is private. APK distribution only.

---

## Table of Contents

- [What is this app?](#what-is-this-app)
- [Requirements](#requirements)
- [How to use](#how-to-use)
- [Measurable items](#measurable-items)
- [How FPS measurement works](#how-fps-measurement-works)
- [Jank & Stutter detection](#jank--stutter-detection)
- [Overlay display](#overlay-display)
- [Records and graphs](#records-and-graphs)
- [FAQ](#faq)
- [Disclaimer](#disclaimer)

---

## What is this app?

An app that lets you overlay FPS and temperatures on your screen in real time while gaming, and records all that measurement data for later review.

I built it to offer something close to paid apps, completely for free.

Here's what it can do:

- Display FPS as an overlay while gaming
- Measure CPU, GPU, RAM, and battery in real time
- Save measurement data per session to a database
- View saved data as graphs with elapsed time on the X-axis
- Automatically calculate Jank rate, Stutter rate, and frame time stability
- Export to CSV for analysis in external tools
- Supports 3 languages: 日本語 / English / 中文

---

## Requirements

| Item | Details |
|------|---------|
| OS | Android 14 or higher (API 34+) |
| Permission | Root **or** Shizuku |
| Other | Overlay display permission, Usage access permission |

Either Root or Shizuku is required.
Without one of them, FPS and some other measurements will not work correctly.

> For how to set up Shizuku, see the [Shizuku official site](https://shizuku.rikka.app/).

---

## How to use

### First launch

A setup dialog appears the first time you launch the app.

- **If Shizuku is running** → A "Grant Shizuku Permission" button will appear. Tap it to grant permission.
- **If Root is available** → Automatically detected and shown as "✓ Available". Press "OK" to close.
- **If neither is available** → An error card is shown. Please install and launch Shizuku first, then try again.
- You can press "Later" to skip and continue.

### Basic flow

1. Launch the app
2. Check that permissions are set correctly on the home screen
3. Select one app to measure under "Target App" (optional)
4. Press **Start Measurement**
5. Open the app you want to measure
6. When done, return to RakkaRecorder and press **Stop & Save**
7. Check results in the Records tab

### With a target app selected

When you select a target app, measurement starts automatically the moment you open it.
Measurement ends and is auto-saved the moment you close the app.
It is designed not to stop when you go to the home screen or switch to another app.

> Only **one** target app can be selected at a time (multiple selection is not supported).

### Without a target app (all-app continuous mode)

Measurement continues indefinitely after you press Start.
The FPS measurement target automatically switches when the foreground app changes.

---

## Measurable items

### FPS
- FPS (frame rate)
- Total frames
- Average / Min / 99th percentile / 95th percentile

### CPU
- Overall CPU usage
- Per-core usage
- Per-cluster frequency (Little / Big / Prime)
- CPU temperature

### GPU
- GPU usage
- GPU frequency (supports both Snapdragon and MediaTek)
- GPU temperature

### Memory
- RAM usage (used / total / free / cached)

### Battery & Power
- Battery current (mA)
- Battery voltage (mV)
- Battery level (%)
- Battery temperature
- Power consumption (mW)
- Power per frame (mW/fps)

### Other
- Skin temperature

---

## How FPS measurement works

FPS measurement uses a 4-stage fallback system.
It tries each method in order and uses whichever works.

```
Method A: SurfaceFlinger --timestats (totalFrames delta)
  → Most accurate for all apps on Android 14. Used by default.

Method B: dumpsys gfxinfo PROFILEDATA
  → Fallback for HWUI-based apps (general apps)

Method C: SurfaceFlinger --latency
  → For older games and layers without BLAST support

Method D: gfxinfo Total frames delta
  → Last resort
```

While Method A is succeeding, it will not fall back to other methods.
Buffer contamination countermeasures are in place to prevent FPS spikes
when the screen is idle and FPS is approaching 0.

FPS is automatically capped at the display's refresh rate.
For example, a 60Hz device records a maximum of 60fps, and a 120Hz device a maximum of 120fps.

---

## Jank & Stutter detection

Lets you quantify the feeling of "average FPS looks fine, but the game feels choppy."
Displayed in the **Summary tab** and **FPS tab** of a record's details.

### Jank rate (dropped frame indicator)

The percentage of seconds where FPS fell below 85% of the refresh rate.
These seconds are estimated to have experienced frame drops or freezes.

Example: On a 60Hz device, the percentage of seconds where fps < 51

| Value | Rating |
|-------|--------|
| 0–10% | Good |
| 10–20% | Slightly unstable (yellow) |
| 20%+ | Problematic (red) |

### Stutter rate (choppiness indicator)

The percentage of transitions where FPS changed by more than 5fps between consecutive samples.
Detects irregular frame intervals that cause a choppy feeling, even without full frame drops.

| Value | Rating |
|-------|--------|
| 0–15% | Good |
| 15–30% | Slightly unstable (yellow) |
| 30%+ | Problematic (red) |

### Frame Time σ (standard deviation)

The standard deviation of the estimated frame time (ms), calculated as 1000÷FPS.
Closer to 0 means more stable rendering.

> ※ All of these are approximated from per-second FPS values.
> They differ from precise frame-level measurements.

---

## Overlay display

You can choose which items to display from the settings screen.
Display position (9 locations), font size, and background opacity can also be adjusted.

Supported display items:

- FPS
- CPU usage / per-core / frequency / temperature
- GPU usage / frequency / temperature
- RAM usage / details
- Battery current / voltage / level / temperature
- Power consumption / power per frame
- Skin temperature
- FPS debug info (for developers)

Supports dark mode and Material You (Android 12 and later).

---

## Records and graphs

### Record list

Measured sessions are listed with app name, date/time, duration, and sample count.

### Detail tabs

Tap a record to view details in tab format.

| Tab | Content |
|-----|---------|
| Summary | Average FPS, CPU, RAM, power + Jank/Stutter stats |
| FPS | avg / min / 99th / 95th / total frames + Jank/Stutter stats + graph |
| CPU | Usage and temperature graphs |
| Cluster | Per-cluster frequency graphs |
| GPU | Usage, frequency, and temperature graphs |
| RAM | Usage graph |
| Power | Power, current, voltage, and frame power graphs |
| Temperature | CPU, GPU, skin, and battery temperature graphs |
| Battery | Level, current, and temperature graphs |

Graphs show **min / avg / max**, and the X-axis shows **elapsed measurement time**.

### CSV export

Tap the share button to export a CSV.
Each row is one second of data with all measurement items included.

---

## FAQ

**Q. Can I use it without Root?**  
A. Yes, as long as you have Shizuku. Without either, some measurements will not work.

**Q. FPS stays at 0**  
A. Check that Root/Shizuku permissions are correctly granted.
Permission to execute the `dumpsys` command is required.

**Q. The power values look wrong**  
A. Calculated as `|I(mA)| × V(mV) ÷ 1,000,000 = W`.
Some devices report current in μA, which may cause the values to be off.

**Q. Does it support both Snapdragon and MediaTek?**  
A. Yes. The SoC is automatically detected and the GPU/CPU measurement paths are switched accordingly.
For MediaTek (Dimensity / Helio), multiple GPU frequency paths are tried automatically.

**Q. Can I select multiple target apps?**  
A. No. Only one app can be selected at a time.

**Q. How reliable are the Jank rate and Stutter rate values?**  
A. They are approximated from per-second FPS values, so please treat them as reference figures.
They differ from precise frame-level measurements.

---

## Disclaimer

- This app is a personal hobby project
- Use at your own risk, as it involves Root/Shizuku operations
- No responsibility is taken for bugs, device damage, or any other issues
- Source code is private. Modification or redistribution of the APK is prohibited.

---

## Copyright

Copyright © 2026 Rakkashin.  
All Rights Reserved.

The source code is private.  
Modification, redistribution, or reverse engineering of the APK is prohibited.
