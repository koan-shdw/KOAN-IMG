# KOAN.img

Local AI image search for Windows. Give it a seed image or some text and it finds
everything in your library that matches, by subject, by colour, or both, with AI that
runs entirely on your own GPU. Chain the results into visual narratives or AI-generated
videos. Your library, index, and API keys stay on your machine.

---

## For beta testers

### 1. Install

Download the installer from the latest [Release](../../releases) and run it.

**Windows** `KOAN.img Setup x.y.z.exe`. Windows SmartScreen will warn ("Windows
protected your PC") because the app isn't code-signed yet. Click **More info**, then
**Run anyway**. (One-time.)

Windows only for now. A Mac build is not available yet.

### 2. First launch

The app sets up its AI runtime on first launch: a one-time download of about 2.7 GB.
It can take a few minutes; a progress screen shows the steps. You need an **NVIDIA GPU
with CUDA** to index and search, since that is what runs the AI locally.

### 3. Index your library (required)

Search runs against an index, so build one first.

1. Open the **INDEX** tab.
2. Set a **source folder** (your image library) and an **output folder** (where the
   index is stored).
3. **Run**. Every image is read by three AI models and checkpointed as it goes, so you
   can stop and resume.

Optionally index subfolders and video files.

### 4. Search (PICK)

Load one or more seed images, or type a prompt, and hit **RUN**.

- Mix seeds, positive and negative text, and colour, and slide how much each one matters.
- Feed any result back in as a new seed to refine.
- **⚑** saves a result to the **SAVED** shelf; **🔍** previews it without picking.
- Export your picks, or push them to **NARRATIVE**, **VIDEO**, or **GENERATE**.

### 5. Optional cloud features

**GENERATE** and **VIDEO** call cloud services and need your own API keys, added under
**⚙ Settings**. You pay those services directly.

- **Astria** for GENERATE.
- **fal.ai** for VIDEO.
- **Anthropic** for prompt help in VIDEO.

Everything else (indexing, search, PLOT) runs on your machine. Nothing is uploaded.

### Hit a bug?

Open **About**, then **open the log folder**, and send the log. It records errors and
what the app was doing.

### Known beta limitations

- Unsigned installer (the SmartScreen warning above).
- Windows only for now; no Mac build yet.
- Requires an NVIDIA CUDA GPU.
- First launch downloads about 2.7 GB of AI runtime, one time.
- Auto-updates on launch; applies on restart.

---

## For developers

Electron + React + TypeScript (electron-vite) front end, with a local Python engine
(CLIP + BLIP + FAISS) for indexing and search.

Source lives in a separate private repo. This repo hosts the Windows releases only.

Releases are built by GitHub Actions on a version tag: bump `desktop/package.json`, then
push a `v2.x` tag. That builds the installer and publishes it here as a prerelease;
installed copies auto-update from it.

## License

All rights reserved.
