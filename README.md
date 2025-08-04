# Seizure Seeker

Seizure Seeker is a browser-based EEG seizure-like event detector that runs entirely client-side. It processes `.edf` (European Data Format) and `.csv` EEG files, computes signal energy, and identifies candidate seizure segments using overlapping RMS windows and robust statistics.

## Features

- **Input formats**: EDF/EDF+ and CSV
- **Real-time processing**: All computations occur in your browser—no server required
- **Robust baseline estimation**: Uses median + MAD of the quietest 50% of frames
- **Adjustable parameters**:
  - **Z-score threshold**: Number of MADs above median to flag (default `5`)
  - **Minimum duration**: Minimum segment length in seconds (default `15`)
  - **Merge gap**: Max gap to merge segments (fixed at `5` seconds)
- **Instant results**: Detected segment start/end times displayed in seconds
- **Privacy-first**: EEG data never leaves your computer

## Installation

1. **Clone the repository**  
   ```bash
   git clone https://github.com/yourusername/seizure-seeker.git
   cd seizure-seeker
   ```

2. **Serve the files**  
   You can use any static file server:
   ```bash
   # Using Node.js 'serve'
   npx serve .

   # Or Python's built-in HTTP server
   python3 -m http.server 8000
   ```

3. **Open in browser**  
   Navigate to `http://localhost:8000` (or your chosen port) and load an EEG file.

## Usage

1. Click **Choose File** and select an `.edf` or `.csv` EEG file.  
2. (For CSV files) Enter the sampling rate (Hz) in the **Sampling rate** field.  
3. Adjust **Z-score threshold** and **Min duration** as needed.  
4. Click **Analyze** to detect seizure-like segments.  
5. View the results table for segment start/end times and durations.

## Algorithm

Seizure Seeker implements a simple, fast amplitude-based detection:

1. **Windowing**  
   Slide a **1-second** window with **50% overlap** over the EEG signal.
2. **RMS Energy**  
   Compute the root-mean-square (RMS) energy of each window to estimate signal power.
3. **Baseline estimation**  
   Sort all RMS values, take the **quietest 50%**, and compute their **median** and **MAD** (mean absolute deviation).
4. **Thresholding**  
   Flag frames where  
   ```
   RMS > median + z * MAD
   ```  
   (`z` is the Z-score threshold).
5. **Segmentation**  
   Group consecutive flagged frames into segments.
6. **Duration filtering**  
   Discard segments shorter than the **minimum duration** (`minDur`).
7. **Merging**  
   Merge segments separated by gaps ≤ **5 seconds**, then re-apply the minimum duration filter.
8. **Output**  
   Report segment start/end times (in seconds) for review.

## Configuration

All parameters can be tweaked directly in the UI. To customize core behavior (e.g., window size, overlap, merge gap), edit the `detect()` function in `index.html`.

## Contributing

Contributions are welcome! Please open issues or submit pull requests for enhancements, bug fixes, or new features.

## License

MIT License. See [LICENSE](LICENSE) for details.
