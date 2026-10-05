# Pixel Flow Level Finder

**Live Demo:** [https://pixelflowonline.net/image-search](https://pixelflowonline.net/image-search)

A browser-based tool that identifies game levels from screenshots using **perceptual hashing (pHash)**. Upload a screenshot of a Pixel Flow level and it instantly finds the closest match from a prebuilt database.

## How It Works

The tool computes a **768-bit RGB tri-channel pHash** for each image:

1. Center-crop the image to 75% of its original size
2. Resize to 128×128 pixels
3. Apply a 2D Discrete Cosine Transform (DCT) independently on each RGB channel
4. Extract the top-left 16×16 low-frequency coefficients (256 bits per channel)
5. Threshold against the mean to produce a binary hash, then encode as hex
6. Compare against the database using **Hamming distance**

This approach is more color-aware than grayscale pHash, making it better at distinguishing levels with similar layouts but different color schemes.

## Features

- **Level Finder** (`index.html`) — drag-and-drop a screenshot to find the top 5 matching levels
- **Batch Hash Generator** (`fast-batch-phash.html`) — bulk-generate pHash values for a folder of images using multi-threaded Web Workers (parallelism scales with CPU core count)
- **Zero dependencies** for the browser tools — pure HTML + Canvas + Web Workers, no build step required
- Outputs a `level-hashes.json` file that can be dropped directly into the finder

## Getting Started

### Option A: Open directly in browser

Just open `index.html` in any modern browser. No server needed.

> Note: Some browsers restrict Web Workers on `file://` URLs. If you hit that, use Option B.

### Option B: Run the local server

```bash
npm install
node server.js
```

Then visit `http://localhost:8000`.

## Generating Hashes for New Levels

1. Open `fast-batch-phash.html` in your browser
2. Click "Select Image Files" and pick your level screenshots
3. The tool processes them in parallel and auto-downloads `level-hashes.json`
4. Replace the `DB_HASHES` object in `index.html` with the new JSON content

## Hash Format

Each hash is a **192-character hex string** (768 bits = 256 bits × 3 channels: R, G, B).

```
"level-1007": "cae04787b91511c3...14d408fab842c5a0"
              |<-- R (64 chars) -->|<-- G -->|<-- B -->|
```

## Match Interpretation

| Hamming Distance | Meaning        |
| ---------------- | -------------- |
| < 20             | Strong match   |
| 20 – 60          | Likely match   |
| 60 – 120         | Possible match |
| > 120            | Low confidence |

## Project Structure

```
├── index.html              # Level finder UI
├── fast-batch-phash.html   # Multi-threaded batch hash generator
├── batch-phash.html        # Single-threaded batch hash generator
├── meowdoku.html           # Meowdoku guide landing page (links to meowdokuguide.com)
├── phash-worker.js         # Standalone Web Worker for pHash computation
├── server.js               # Minimal static file server (Node.js)
├── level-hashes.json       # Precomputed hash database
└── level_images/           # Sample level screenshots
```

## Requirements

- Any modern browser with Canvas and Web Worker support (Chrome, Firefox, Edge, Safari)
- Node.js (only needed for the local server)

## 🔗 Links

- Website: [https://pixelflowonline.net](https://pixelflowonline.net)
- Meowdoku Guide: [https://meowdokuguide.com](https://meowdokuguide.com)

## License

MIT
