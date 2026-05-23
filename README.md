# VIX Spike & Drop Alert — MQL4 Script

A MetaTrader 4 script that monitors the **CBOE Volatility Index (VIX)** in real time, computing the bar-to-bar percentage change between consecutive minute-loop cycles using a persistent `PreviousVIX` global variable, and firing directional spike or drop alerts when the computed percentage change meets or exceeds independently configured positive and negative threshold levels.

---

## Overview

The VIX — often called the "fear gauge" — reflects the implied volatility expectations of S&P 500 options market participants and is one of the most widely watched risk sentiment indicators in global finance. A sharp VIX spike typically signals a rapid increase in fear, risk aversion, and potential flight to safety across equity and forex markets. A sharp drop signals returning complacency and risk appetite. This script provides real-time monitoring of these inflection events: it tracks the VIX close value each cycle via `iClose()`, computes the percentage change from the prior cycle using the standard `(current − previous) / previous × 100` formula, and fires a labeled directional alert the moment either threshold is breached. By running as a lightweight loop with `Sleep(60000)`, the script provides effective near-real-time VIX monitoring without overloading the terminal.

> **Note on file naming:** This file is distributed as `Garman-Klass_Volatility_Estimator_001.mq4` but implements a VIX percentage-change alert — not the Garman-Klass volatility estimator formula. The README documents the actual implemented logic.

---

## Features

- **Percentage-change VIX monitoring** — `change = (currentVIX − PreviousVIX) / PreviousVIX × 100` computed each cycle; first-cycle guard (`PreviousVIX != 0`) prevents false alerts during initialization
- **Dual-direction threshold detection** — `SpikeThreshold` (positive) and `DropThreshold` (negative) evaluated independently each cycle, allowing asymmetric configuration for instruments with directional volatility skew
- **Persistent `PreviousVIX` state** — global double retains the prior cycle's VIX close across loop iterations; updated unconditionally at cycle end regardless of alert state
- **Alert message includes change percentage** — `AlertVIX()` formats with `StringFormat("%.2f, Change: %.2f%%", currentVIX, change)` so each alert carries full context for immediate interpretation
- **Three notification channels:** sound alert via `Alert()`, email via `SendMail()`, and mobile push via `SendNotification()`
- **Configurable VIX symbol and timeframe** — accommodates broker-specific VIX naming conventions (`VIX`, `US.VIX`, `VOLATILITY`) and any `ENUM_TIMEFRAMES` value
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)
- Logs all VIX readings and alert events to the MT4 **Experts** tab for post-session review

---

## How It Works

1. Every minute, `iClose(VIXSymbol, Timeframe, 0)` fetches the current VIX close value
2. If `PreviousVIX != 0`, percentage change is computed: `change = (currentVIX − PreviousVIX) / PreviousVIX × 100`
3. Two threshold comparisons are evaluated independently:
   - `change >= SpikeThreshold` → **Extreme VIX Spike Detected**
   - `change <= DropThreshold` → **Extreme VIX Drop Detected**
4. `AlertVIX()` formats and dispatches via all enabled channels; message includes current VIX and change percentage
5. `PreviousVIX = currentVIX` unconditionally updated at cycle end

---

## Input Parameters

| Parameter        | Type            | Default      | Description                                            |
|------------------|-----------------|--------------|--------------------------------------------------------|
| `VIXSymbol`      | string          | `VIX`        | Symbol name for VIX data in your broker's Market Watch |
| `Timeframe`      | ENUM_TIMEFRAMES | `PERIOD_D1`  | Timeframe for VIX monitoring                           |
| `SpikeThreshold` | double          | `5.0`        | Minimum % increase to trigger a spike alert            |
| `DropThreshold`  | double          | `-5.0`       | Maximum % decrease to trigger a drop alert             |
| `EnableAlerts`   | bool            | `true`       | Fire an on-screen/sound alert                          |
| `EnableEmail`    | bool            | `false`      | Send an email notification                             |
| `EnablePush`     | bool            | `false`      | Send a mobile push notification                        |

---

## Alert Message Format

```
Extreme VIX Spike Detected detected on VIX (Timeframe: PERIOD_D1)
Current VIX: 28.45, Change: 6.32%
```

---

## Installation

1. Copy `Garman-Klass_Volatility_Estimator_001.mq4` to `MQL4/Scripts/` in your MT4 data folder
2. Compile in MetaEditor (F7)
3. Ensure the VIX symbol is available and streaming in Market Watch
4. Drag onto any chart from Navigator → Scripts
5. Configure inputs and click **OK**

> **Note:** The `VIXSymbol` must match exactly how the symbol appears in your broker's Market Watch. Common names: `VIX`, `US.VIX`, `VOLATILITY`.

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)
- Broker providing VIX or equivalent volatility index data feed

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
