# Video Annotator — Sign Language Segmentation Tool

A fast, keyboard-driven desktop tool to annotate sign language videos by marking sentence boundaries.

---

## Prerequisites

| Requirement | Version |
|---|---|
| [Node.js](https://nodejs.org/) | ≥ 18 |
| [ffmpeg](https://ffmpeg.org/download.html) | any recent |
| Python | ≥ 3.8 (for video cutting only) |

Make sure both `node` and `ffmpeg` are in your system PATH.

---

## Installation & Launch

```bash
cd d:\Tool\video-annotator
npm install
npm start
```

---

## Usage

### 1. Open a Folder
Click **"Open Folder"** in the sidebar → select the folder containing your `.mp4` / `.avi` / `.mov` videos.

The sidebar will list all videos. Already-annotated videos are highlighted in green.  
The app **auto-loads the first unannotated video**.

### 2. Annotate
Watch the video, and press **`Space`** at each sentence boundary.  
A green tick appears on the timeline and the timestamp is added to the Splits panel.

### 3. Save & Continue
- **`S`** → save current annotation  
- **`N`** → auto-save + load the next video

The annotation is saved as a JSON file next to the video:
```
videos/
  video_001.mp4
  video_001.json   ← saved here
```

### 4. Resume
Reload the app and open the same folder — previously marked splits reload automatically.

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| `Space` | Mark split at current timestamp |
| `←` / `→` | Seek ±1 second |
| `Shift + ←` / `→` | Seek ±0.2 second |
| `↑` / `↓` | Speed up / slow down (0.25×–2×) |
| `Ctrl + Z` | Undo last split |
| `S` | Save annotation |
| `N` | Next video (auto-saves) |

---

## Output Format

```json
{
  "video": "video_001.mp4",
  "fps": 30,
  "duration": 876.3,
  "splits": [12.34, 19.80, 25.10, 31.92]
}
```

Segments are inferred from `splits`:  
`[0 → 12.34]`, `[12.34 → 19.80]`, `[19.80 → 25.10]`, ...

---

## Cut Video into Clips

After annotating, run the Python cutter:

```bash
# Cut one video
python cut_video.py videos/video_001.json

# Custom output folder
python cut_video.py videos/video_001.json --out_dir clips/video_001/

# Batch — cut all annotated videos in a folder
for %f in (videos\*.json) do python cut_video.py "%f" --out_dir clips\
```

Output clips: `video_001_001.mp4`, `video_001_002.mp4`, ...

> **Note:** Uses ffmpeg stream-copy — no re-encoding, very fast.  
> Already-existing clips are skipped (idempotent).

---

## Project Structure

```
video-annotator/
├── main.js         Electron main process
├── preload.js      IPC bridge
├── index.html      UI layout
├── style.css       Dark theme styles
├── renderer.js     UI logic & keyboard shortcuts
├── cut_video.py    Video segment cutter (Python + ffmpeg)
├── package.json    Electron app config
└── README.md       This file
```
