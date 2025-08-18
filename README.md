# Seizure Seeker

Seizure Seeker is a browser-based EEG seizure-like event detector that runs entirely client-side. It processes `.edf` (European Data Format) and `.csv` EEG files with multi-channel support, computes signal energy using overlapping RMS windows per channel, identifies candidate seizure segments per channel via robust statistics, and can fuse detections across channels using a K-of-N vote for improved accuracy.

---

## Table of Contents

1. [Overview](#overview)  
2. [Demo](#demo)  
3. [Features](#features)  
4. [Installation](#installation)  
5. [Usage](#usage)  
6. [Repository Structure](#repository-structure)  
7. [Algorithm Details](#algorithm-details)  
8. [Parameter Tuning](#parameter-tuning)  
9. [Performance](#performance)  
10. [Customization](#customization)  
11. [Roadmap](#roadmap)  
12. [Contributing](#contributing)  
13. [License](#license)  
14. [Contact](#contact)  

---

## Overview

Seizure Seeker now supports multi-channel EEG recordings, performing detection on each channel independently. It can fuse detections across channels using a configurable K-of-N voting scheme to enhance reliability. All processing is done entirely in-browser—no data leaves your machine—ensuring privacy and instant results.

---

## Demo

1. **Load the app**: serve `index.html` with a static server.  
2. **Upload** an EEG file (`.edf` or `.csv`).  
3. **Adjust** detection parameters including fusion options.  
4. **Analyze** and view detected segments with start/end times per channel and fused results.

> _Tip_: Check out `examples/sample.edf` for a quick test.

---

## Features

- EDF/EDF+ and CSV multi-channel input  
- Per-channel robust RMS detection  
- Optional cross-channel fusion with K-of-N voting  
- User-adjustable Z-score threshold, minimum duration, merge gap, and K value  
- Instant results rendered dynamically  
- Privacy: EEG data never leaves the browser

---

## Installation

```bash
git clone https://github.com/yourusername/seizure-seeker.git
cd seizure-seeker
# Serve locally
python3 -m http.server 8000
```

Open <http://localhost:8000> in your browser.

---

## Usage

1. **Select file**: Click “Choose File” & pick `.edf` or `.csv`.  
2. **(CSV only)**: Enter sampling rate (Hz).  
3. **Tune**: set Z-score, min Duration, merge gap, K value, and enable/disable fusion.  
4. **Analyze**: Click the button.  
5. **Inspect**: Review start/end times per channel and fused detections.

---

## Repository Structure

```
/
├─ index.html         # Main UI + detection logic
├─ README.md          # This file

```

---

## Algorithm Details

**1. Per-Channel Windowing**  
- 1 s RMS frames with 50% overlap computed independently for each channel.

**2. Per-Channel RMS Energy**  
```
rms[i][ch] = sqrt((1/W) * Σ_{j=0..W-1} x[ch][i*hop + j]^2)
```

**3. Per-Channel Baseline**  
- For each channel, sort all `rms` values, take lowest 50% → array `quiet[ch]`.  
- Compute `median[ch]` and `MAD[ch]` from `quiet[ch]`.

**4. Per-Channel Thresholding**  
- Threshold for each channel:  
  `thr[ch] = median[ch] + z * MAD[ch]`  
- Frames above `thr[ch]` flagged as candidate seizure frames.

**5. Per-Channel Segmentation and Merge**  
- Group consecutive hot frames per channel → initial segments.  
- Discard segments shorter than `minDur`.  
- Merge segments separated by ≤ `mergeGap`.  
- Final filter: discard segments < `minDur`.

**6. Optional Fusion Step**  
- Convert each channel’s detections to binary frame vectors.  
- Apply K-of-N voting across channels (at least K channels must flag a frame).  
- Convert fused frames back to segments.

---

## Parameter Tuning

| Parameter        | Default | Description                                              |
|------------------|---------|----------------------------------------------------------|
| **z**            | 5       | Multiples of MAD above median to flag a frame.          |
| **minDur**       | 15 s    | Minimum segment length to accept.                       |
| **mergeGap**     | 5 s     | Maximum silence gap to merge adjacent detections.       |
| **K**            | 1       | Number of channels required to vote for fused detection.|
| **fusion**       | false   | Enable or disable cross-channel fusion step.             |

Adjust parameters to balance sensitivity and specificity.

---

## Performance

- **Complexity**: O(N × channels) for RMS + segmentation  
- **Memory**: O(N/2 × channels) for RMS arrays  
- **Benchmark**: ~0.1 s for a 1 hr, 256 Hz, 16-channel recording on a modern laptop

---

## Customization

Edit the `detect()` function in `index.html` to modify:

- Window size and overlap  
- Baseline calculation logic  
- Merge rules and gap thresholds  
- Fusion logic and voting scheme  
- Channel selection and handling

---

## Roadmap

- [x] Multi-channel detection & per-lead thresholds  
- [ ] Frequency-band filtering (delta/alpha/beta power)  
- [ ] Interactive plot overlay  
- [ ] Command-line & API wrapper  
- [ ] Real-time streaming mode

---

## Contributing

1. Fork the repo  
2. Create a branch  
3. Submit a PR  
4. Celebrate 🎉

---

## License

This project used the CHB-MIT Scalp EEG Database from PhysioNet, available under the PhysioNet Credentialed Health Data License. Data use is for non-commercial, research purposes only.

---

## Contact

For questions or feedback, contact [sarthaktayal2@gmail.com].
