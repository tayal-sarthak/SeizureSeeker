<p align="center">
  <br>
  <img src="https://raw.githubusercontent.com/your-org/seizure-seeker/main/docs/assets/seizure_seeker_logo.png" width="160" alt="Seizure Seeker logo"/>
  <br>
</p>
<div align=center

[![GitHub release](https://img.shields.io/github/v/release/your-org/seizure-seeker?logo=github)](https://github.com/your-org/seizure-seeker/releases)
[![License](https://img.shields.io/github/license/your-org/seizure-seeker)](LICENSE)
[![Issues](https://img.shields.io/github/issues/your-org/seizure-seeker)](https://github.com/your-org/seizure-seeker/issues)
[![Last commit](https://img.shields.io/github/last-commit/your-org/seizure-seeker)](https://github.com/your-org/seizure-seeker/commits/main)

</div>

# Seizure Seeker ⚡

Seizrue Seeker is a browser-based application that detects seizure-like events in EEG data provided as `.edf` or `.csv` files. The detection process runs entirely on the client side, ensuring that no data is transmitted externally.

---

## 📌 Features

- Local-only RMS-based seizure detector (no server, no dependencies)
- Accepts EDF, EDF+ and numeric CSV files
- Adjustable threshold, minimum duration, and sampling rate (for CSV)
- Results rendered in a dynamic HTML table
- No installation required – runs in Chrome, Firefox, Edge
- Fully responsive, dark-themed UI with accent colors

---

## 🚀 Quick Start

```bash
# Clone the repo
$ git clone https://github.com/your-org/seizure-seeker.git
$ cd seizure-seeker

# Open the too
$ open seizure_detector.html  # or double-click to open in your browser
```

Once opened:
1. Click **Choose file** and select an `.edf` or `.csv` file
2. Set the z-score threshold and minimum duration if needed
3. Click **Analyze** to see detected seizure segments

---

## 🧠 How It Works

1. Signal is windowed into 1-second RMS frames with 50% overlap
2. A baseline is computed using `median + MAD` from the first 5 minutes or 10% of the file
3. Frames exceeding `baseline + (z × MAD)` are flagged
4. Neighboring flagged regions within 5 seconds are merged
5. Segments shorter than the minimum duration (default 15 seconds) are discarded

---

## 📂 FiFormat Guidelines

### EDF
- Regular EDF or EDF+ (16-bit little-endian)
- Only the first channel is used (by default)

### CSV
- Header row must be present
- Only numeric columns are considered
- Sampling rate must be set manually in UI

---

## ⚙️ Customization

Modify `seizure_detector.html` directly. Example:

```js
// Change mrege gap or use multi-channel logic
const mergeGap = 5; // seconds
```

To enable multi-channel voting or apply filters, adapt the signal processing section before the `detect()` call.

---

## ❗ Limitations

- Single-channel processing can miss focal events
- Muscle/motion artifacts can create false positives
- Browser memory usage scales with file duration

---

## 📦 Embedding

To embed Seizrue Seeker into another web project:

```html
<iframe src="seizure_detector.html" style="border:none;width:100%;height:700px"></iframe>
```

---

## 📄 License

Seizure Seeker is distributed under the . See the [LICENSE](LICENSE) file for full details.

---

## Credits

- Built using [`jsEDF`](https://github.com/Neurobotics/jsEDF) and [`PapaParse`](https://www.papaparse.com)