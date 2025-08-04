# Seizure Seeker

Seizure Seizure is a browser-based EEG seizure-like event detector that runs entirely client-side. It processes `.edf` (European Data Format) and `.csv` EEG files, computes signal energy using overlapping RMS windows, and identifies candidate seizure segments via robust statistics.

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

Seizure Seeker provides an instant, privacy-first tool to detect seizure-like segments in EEG recordings. All processing is done in-browser—no data leaves your machine.

---

## Demo

1. **Load the app**: serve `index.html` with a static server.  
2. **Upload** an EEG file (`.edf` or `.csv`).  
3. **Adjust** detection parameters.  
4. **Analyze** and view detected segments with start/end times.

> _Tip_: Check out `examples/sample.edf` for a quick test.

---

## Features

- **Input**: EDF/EDF+ & CSV  
- **On-the-fly parsing**: Papa Parse for CSV, jsEDF for EDF  
- **Robust baseline**: median + MAD of the quietest 50% of frames  
- **Parameters**:
  - Z‑score threshold (`z`)
  - Minimum duration (`minDur`)
  - Merge gap (fixed at 5s)  
- **Instant results**: renders a dynamic table in seconds  
- **Privacy**: EEG never leaves browser

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
3. **Tune**: Set Z‑score & Min Duration.  
4. **Analyze**: Click the button.  
5. **Inspect**: Review start/end times.

---

## Repository Structure

```
/
├─ index.html         # Main UI + detection logic
├─ README.md          # This file
├─ LICENSE            # MIT License
├─ examples/
│  └─ sample.edf      # Example EEG recording
└─ data/
   └─ sample.csv      # Example CSV data
```

---

## Algorithm Details

**1. Windowing**  
- 1 s RMS frames with 50% overlap.

**2. RMS Energy**  
\`\`\`  
rms[i] = sqrt((1/W) * Σ_{j=0..W-1} x[i*hop + j]^2)  
\`\`\`

**3. Baseline**  
- Sort all `rms`, take lowest 50% → array `quiet`.  
- \`median = quiet[mid]\`  
- \`MAD = mean(|quiet - median|)\`

**4. Threshold**  
- \`thr = median + z * MAD\`  
- Frames above \`thr\` are flagged.

**5. Segmentation**  
- Group consecutive hot frames → initial segments.  
- Discard segments \< \`minDur\`.

**6. Merge Gaps**  
- Merge segments separated by ≤ 5 s.  
- Final filter: discard \< \`minDur\`.

---

## Parameter Tuning

| Parameter        | Default | Description                                              |
|------------------|---------|----------------------------------------------------------|
| **z**            | 5       | Multiples of MAD above median to flag a frame.          |
| **minDur**       | 15 s    | Minimum segment length to accept.                       |
| **mergeGap**     | 5 s     | Maximum silences to bridge adjacent detections.         |

Adjust to trade off sensitivity vs. specificity.

---

## Performance

- **Complexity**: O(N) for RMS + segmentation  
- **Memory**: O(N/2) for RMS array  
- **Benchmark**: ~0.1 s for a 1 hr, 256 Hz recording on a modern laptop

---

## Customization

Edit the `detect()` function in `index.html` to modify:

- Window size & overlap  
- Baseline strategy (e.g., first 5 min vs. quietest 50%)  
- Merge rules  
- Multi-channel support

---

## Roadmap

- [ ] Multi-channel detection & per-lead thresholds  
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

MIT License © 2025

---

## Contact

For questions or feedback, contact [sarthaktayal2@gmail.com].
