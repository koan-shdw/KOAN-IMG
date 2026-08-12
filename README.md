# KOAN.img

<p align="center">
  <a href="https://github.com/koan-shdw/KOAN-IMG/releases/latest/download/KOAN.img-Setup.exe">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset=".github/buttons/download-windows-dark.svg">
      <img src=".github/buttons/download-windows-light.svg" alt="Download for Windows" height="64">
    </picture>
  </a>
</p>

A local-first desktop app for searching enormous image libraries with AI — hundreds of thousands
to millions of images, all on your own machine. Give it a seed image, a text prompt, or both, and
it finds everything in your library that matches — by **what images mean**, **how they look**, and
**what colours they carry**, each on its own independent dial. Chain results into visual
narratives, lay your whole library out on semantic axes, or turn picks straight into AI-generated
video. Indexing, search and plotting run entirely on your GPU — nothing is uploaded, ever.

---

## For beta testers

### 1. Install

Download `KOAN.img Setup x.y.z.exe` from the latest [Release](../../releases) and run it.

Windows SmartScreen will warn ("Windows protected your PC") because the app isn't code-signed
yet. Click **More info → Run anyway**. (One-time.)

The app auto-updates: when a new release is published, your installed copy picks it up on launch.

### 2. First launch

A setup wizard walks you through everything:

- **Engine bootstrap.** The AI engine installs itself on first run — including a CUDA build of
  PyTorch matched to your GPU. This is a one-time multi-GB download; the app shows progress and
  works immediately after.
- **Folders.** Point it at your image library and pick where the index lives.
- **API keys (optional).** Only the cloud tabs need keys — fal.ai for VIDEO, Astria for GENERATE,
  Anthropic for prompt enhancement. Search, indexing and plotting never touch the network.

### 3. Index your library (INDEX)

The app reads every image once and stores four things about it, all locally:

- **CLIP** embedding — the classic search brain (V1).
- **SigLIP 2** embedding — a far stronger meaning brain (V2 · Concept).
- **DINOv2** embedding — a pure visual-resemblance brain: framing, texture, style (V2 · Look).
- **BLIP** caption + a **LAB colour signature** — descriptions and palettes.

Indexing is chunked and checkpointed — cancel or crash anytime, it resumes where it stopped.
Videos index by first frame. A **gentle mode** (default) caps CPU use so your machine stays
usable while a big library grinds through overnight. A million-image library is roughly a
GPU-day; after that, only new files get processed.

### 4. Search (PICK)

The heart of the app. Seeds, text, and dials — everything is a weight, nothing fights anything
else.

- **Engine toggle.** **V1 CLIP** is the original search. **V2 SIGLIP** searches with the new
  brains and replaces the old semantic/colour seesaw with three independent dials:
  - **Concept** — how much ref images pull by *what they're about*
  - **Look** — how much ref images pull by *how they appear* (needs refs; knows no words)
  - **Color** — palette pull
  One notch of Look is worth one notch of Concept — the engine calibrates the brains' raw
  scores against your actual index so the dials are fair.
- **Multi-seed search.** Any number of seed images, each with its own weight (0.1 whispers,
  3.0 dominates). Results can be fed back as seeds with one click.
- **Text steering.** Positive and negative prompts with their own weights.
- **Advanced Colour Mode.** HSV picker with live gradient bars, a screen-wide eyedropper,
  precision and strength gates, and a separate seed-palette gate.
- **Pinned selections** survive across searches. **Dedupe** removes near-identical results at a
  threshold you control. **Sort by axis** re-ranks results by any PLOT axis without
  re-searching. **⚑ Save** bookmarks any card to the SAVED shelf. Video results play on hover.
- **Export** picks to a folder (optionally renamed 001, 002, …), or push them to VIDEO /
  NARRATIVE / GENERATE.
- **Keyboard:** Ctrl+Return run · Ctrl+A select all · Ctrl+E export.

### 5. NARRATIVE

Generation-based drift: search, pick 1–5 favourites, and they become the seeds of the next
generation. A labelled timeline (0A, 1B, 2C…) shows the whole arc; every generation gets its own
prompts and weights; re-run from any point to branch. Export bakes the sequence order into
filenames. Feeds beautifully into VIDEO.

### 6. PLOT

Your library as a map instead of a query. Pick what X, Y and optionally Z mean — CLIP PCA/UMAP
structure, colour metrics, built-in concept axes (Day ↔ Night, Nature ↔ Urban…), or **your own
two-prompt axes** ("vintage film" ↔ "modern digital"). Orbit, zoom, box-select regions and send
them into PICK. Runs off the stored index — no GPU, no re-embedding.

### 7. GENERATE

Astria image generation with your PICK results as face/style references. Prompts, seeds, 11
aspect ratios, super-resolution, per-image cost sidecars. Push outputs back into PICK to search
your library with them.

### 8. VIDEO

Line up picks as keyframes and generate a clip per transition via fal.ai — Kling 3.0 Pro,
Seedance 2 Pro, Hailuo 02 Pro, Pixverse V6 Transition, Wan 2.2 A14B. Per-clip prompts with
Claude-powered enhancement, shared model settings with presets, multiple render versions with
side-by-side compare, live cost estimates, per-render config sidecars, ffmpeg stitch and export.

### 9. Make it yours

Themes (including full custom colours), first-run wizard re-runnable from Settings, right-click
menus with session undo, resizable thumbnails, and a persistent SAVED shelf. The indexed-image
count always shows top-right.

### Hit a bug?

Open an [issue](../../issues) with what you did, what happened, and the log from
`%APPDATA%\koan-img-desktop\logs` if there is one.

## Requirements

- **OS:** Windows 10/11
- **GPU:** NVIDIA with CUDA (RTX 20xx or newer recommended; the engine picks the right PyTorch
  build automatically)
- **Disk:** your image library + roughly 12 GB per million images for the index, plus ~4 GB of
  model weights
- **Keys:** none for search/index/plot; fal.ai / Astria / Anthropic only for the cloud tabs

## For developers

Source lives in the private dev repo (`koan-shdw/KOAN-IMG-dev`); the public repo hosts releases.

**Layout:** the app is the Electron project in `desktop/` (electron-vite · React · zustand ·
electron-builder). The Python files in the repo root are the **engine** — a FastAPI service
(`engine_server.py`) the app spawns on a local port and talks to for search, indexing, plotting,
thumbnails, generation and export. The installer bundles the engine via `extraResources`; new
engine files must be added there. The original PyQt UI was retired 2026-08-12 and archived
outside the repo — UI work happens in `desktop/src` only.

```bash
git clone https://github.com/koan-shdw/KOAN-IMG-dev.git
cd KOAN-IMG-dev
# engine venv — install CUDA torch FIRST (deliberately not in requirements.txt)
python -m venv .venv && .venv\Scripts\activate
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu128
pip install -r engine-requirements.txt
# app
cd desktop && npm install && npm run dev      # dev app (spawns the engine)
npm run dist:win                              # installer → desktop/dist/
```

**How V2 search works** (full design: `TRI_INDEX_SPEC.md`): three FAISS indexes share one row
space — row N is the same image in `clip.faiss` (512d), `siglip.faiss` (1152d) and `dino.faiss`
(768d), guaranteed by lockstep indexing with atomic checkpoints. V2 retrieves candidates from the
SigLIP index (plus the Dino index when Look is active), reconstructs each candidate's stored
vectors, and scores every channel independently:

```
score = w_pos·N(text⁺) + w_concept·N(concept) + w_look·N(look) + w_color·N(color) − w_neg·N(text⁻)
```

`N()` normalises each channel with constants measured on the real index
(`calibrate_fusion.py` → `fusion_calib.py`) because raw cosines are not comparable across models.
V1 is the frozen original path, golden-tested byte-identical (`golden_v1_live.json`,
`test_pick_v2.py`, `test_server_v2.py`).

**Indexing** is chunked with a DB-never-ahead-of-FAISS checkpoint order, startup self-repair,
catalog-protection guards, per-engine CPU fallback, and `--gentle` (half cores, below-normal
priority — the default from the app). `backfill_new_engines.py` retrofits the new engines onto an
existing V1 index without touching it.
