# Seizure Seeker

Seizure Seeker is a browser based EEG event detector; built by multiple students; designed for research and learning. The algorithm runs fully on the client; it reads `.edf` and `.csv` EEG files with many channels; computes simple energy features with overlapping RMS windows; finds candidate segments using a robust baseline; and can fuse detections across channels using a K of N vote. Your data stays on your machine.

---

## Table of Contents

1. [Overview](#overview)  
2. [Quick Start](#quick-start)  
3. [Features](#features)  
4. [Usage](#usage)  
5. [Repository Structure](#repository-structure)  
6. [How the algorithm works](#how-the-algorithm-works)  
7. [Parameter Tuning](#parameter-tuning)  
8. [Performance](#performance)  
9. [Customization](#customization)  
10. [Roadmap](#roadmap)  
11. [Contributing](#contributing)  
12. [License](#license)  
13. [Contact](#contact)

---

## Overview

Seizure Seeker performs detection per channel; then it can combine results across channels if you ask it to. Everything happens in the browser; nothing is uploaded. The goal is to help you flag time ranges that deserve a closer look; not to diagnose.

---

## Quick Start

1. Serve `index.html` with a simple static server.  
2. Load an EEG file; `.edf` or `.csv` both work.  
3. For `.csv` files; enter the sampling rate in Hz.  
4. Adjust sensitivity and optional fusion.  
5. Run the analysis; inspect per channel and fused segments; export to CSV when ready.

> Tip; try `examples/sample.edf` for a fast check.

---

## Features

* EDF and CSV input with many channels  
* Per channel RMS energy with overlap  
* Optional cross channel fusion with K of N voting  
* Controls for Z score; minimum duration; merge gap; and K  
* Instant feedback in the page  
* Privacy; processing stays local

---

## Usage

1. Select a file; choose `.edf` or `.csv`.  
2. If you chose `.csv`; enter the sampling rate in Hz.  
3. Tune the controls; Z score; Min Duration; Merge Gap; K; and toggle Fusion if needed.  
4. Press Analyze.  
5. Review the table; you will see per channel rows and fused rows if fusion is on.  
6. Click Download as CSV to save the segments.

---

## Repository Structure

```
/
├─ index.html         # Main UI and detection logic
├─ README.md          # This file
```

---

## How the algorithm works

**Per channel windowing**  
The signal is split into 1 s windows with 0.5 s steps; this overlap helps catch edges.  

**Per channel energy**  
For each window the algorithm computes RMS; a compact proxy for signal amplitude.  

**Robust baseline**  
For each channel the algorithm sorts window RMS values; takes the quietest half; then computes the median and the median absolute deviation MAD. These two numbers resist outliers and artifacts.  

**Thresholding**  
For each channel the threshold is `median + z * MAD`; frames above the threshold are candidates. With Auto Z; the algorithm picks z so that only a small fraction of frames exceed the threshold; you control this with Aggressiveness.  

**Segmentation and merge**  
Consecutive candidate frames form runs; runs separated by at most Merge Gap are merged; segments shorter than Min Duration are dropped.  

**Optional fusion K of N**  
The algorithm converts per channel runs to a shared time grid; counts votes across channels; and marks time points as positive when at least K channels agree; then it converts those positive points back into segments with the same merge and duration rules.  

---

## Parameter Tuning

* Z score; higher values mean stricter detection; fewer segments pass  
* Min Duration in seconds; removes very short blips after merging  
* Merge Gap in seconds; glues nearby runs when the quiet gap is small  
* Fusion; enable K of N voting to reduce single channel artifacts  
* K; suggested default is about twenty percent of channels with a floor of two  
* Sampling rate for CSV; must match the file; EDF rates are read automatically

Practical advice; if you see nothing; lower Z or reduce Min Duration or switch Auto Z to Sensitive. If you see too much; raise Z or increase Min Duration or pick Conservative; and with many channels try enabling fusion or increasing K.

---

## Performance

* Complexity; O(N * channels) for RMS and segmentation  
* Memory; about O(N/2 * channels) for RMS arrays  
* Benchmark; around 0.1 s for a 1 hour; 256 Hz; 16 channel recording on a modern laptop

Numbers will vary with hardware; file size; and browser.

---

## Customization

Edit the `detect()` function in `index.html` if you want to change any of the following  
* window size and hop  
* baseline estimation  
* merge rules  
* fusion logic and voting  
* channel selection

---

## Roadmap

* Multi channel detection and per lead thresholds  
* Frequency band filtering  
* Interactive plot overlay  
* Command line and API wrapper  
* Real time streaming mode

---

## Contributing

1. Fork the repo  
2. Create a branch  
3. Submit a PR  
4. Celebrate

---

## License

This project uses the CHB MIT Scalp EEG Database from PhysioNet; available under the PhysioNet Credentialed Health Data License. Use is limited to non commercial research purposes.

---

## Contact

Questions or feedback; email sarthaktayal2@gmail.com
