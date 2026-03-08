# RakkaRecorder

RakkaRecorder - Free PerfRecorder

RakkaRecorder is a **free Android application** that measures and
records performance metrics while running games or apps.

It displays **FPS, CPU/GPU usage, temperatures, battery consumption, and
other system metrics in real time**.

Supports **Root or Shizuku**.

> ⚠️ Source code is private. Only APK files are distributed.

------------------------------------------------------------------------

## About

RakkaRecorder can show real-time performance information as an overlay
while gaming and record all measurement data for later analysis.

Features:

-   FPS overlay during gameplay
-   Real-time CPU / GPU / RAM / battery monitoring
-   Session-based data recording
-   Graph visualization
-   CSV export for analysis

------------------------------------------------------------------------

## Requirements

  Item         Requirement
  ------------ ----------------------------------
  OS           Android 14+ (API 34+)
  Permission   Root or Shizuku
  Other        Overlay permission, Usage access

Either **Root or Shizuku is required** for full functionality.

------------------------------------------------------------------------

## Usage

1.  Launch the app
2.  Check that permissions are granted
3.  Select a target app (optional)
4.  Press **Start Recording**
5.  Launch the game/app
6.  Return to RakkaRecorder and press **Stop & Save**
7.  Check results in the Records tab

------------------------------------------------------------------------

## Metrics

### FPS

-   FPS
-   Total Frames

### CPU

-   Total CPU usage
-   Per-core usage
-   Cluster frequencies
-   CPU temperature

### GPU

-   GPU usage
-   GPU frequency
-   GPU temperature

### Memory

-   RAM usage

### Battery

-   Current (mA)
-   Voltage (mV)
-   Battery level (%)
-   Temperature
-   Power consumption (mW)
-   Power per frame (mW/fps)

### Other

-   Skin temperature

------------------------------------------------------------------------

## FPS Measurement

FPS measurement uses a fallback system:

1.  SurfaceFlinger --timestats
2.  dumpsys gfxinfo PROFILEDATA
3.  SurfaceFlinger --latency
4.  gfxinfo Total frames

------------------------------------------------------------------------

## Overlay

Customizable:

-   Position
-   Font size
-   Transparency

Displays:

-   FPS
-   CPU/GPU metrics
-   RAM usage
-   Battery information
-   Power usage
-   Temperature

------------------------------------------------------------------------

## Recording & Graphs

Each session stores:

-   App name
-   Time
-   Duration
-   Sample count

Graphs show **min / avg / max** values.

CSV export supported (1 row = 1 second sample).

------------------------------------------------------------------------

## FAQ

**Q: Can it work without Root?**\
A: Yes, if Shizuku is available.

**Q: FPS shows 0**\
A: Ensure Root or Shizuku permissions are granted.

------------------------------------------------------------------------

## Disclaimer

-   Hobby project
-   Use Root/Shizuku at your own risk
-   APK redistribution or modification is prohibited

*Made by Rakkashin*
