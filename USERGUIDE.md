# AutoFlow AI — User Guide

**Auto VEO & Nano Banana** · v2.1 · [Apache 2.0](https://github.com/S-Q-Ali/VEO-Automation-Extension/blob/main/LICENSE)

---

## ✨ Features

- **📋 Queue System** — Add multiple prompts and process them sequentially with configurable delays
- **🎥 Video Mode** — Generate short-form videos using Veo (Omni Flash, Veo 3.1 variants)
- **🖼️ Image Mode** — Generate still images using Nano Banana Pro/Nano Banana 2
- **🎬 Ingredients to Video** — Upload images and pair each with a text prompt for video generation
- **⛓️ Chain Mode** — Automatically feed generated image outputs as video inputs
- **💾 Auto Download** — Save completed media at your chosen quality (1K–4K, 270p–1080p)
- **🔄 Auto Retry** — Automatic retry on failure (configurable up to 10 attempts)
- **📂 Bulk Import** — Upload `.txt` / `.csv` files to populate prompts in bulk

---

## 📋 Requirements

| Category | Requirement |
|----------|-------------|
| **Browser** | Google Chrome 114+ (MV3), JavaScript enabled, pop-ups allowed for `labs.google` |
| **Google Account** | Active account with access to [Google Flow](https://labs.google/fx/tools/flow) |
| **System** | 4 GB RAM minimum, stable broadband (10 Mbps+) |
| **Permissions** | `storage` (settings), `tabs` (tab access), `downloads` (save files), `debugger` (CDP automation) |

---

## 📥 Installation

### From Source (Development)

1. Clone the repo:
   ```bash
   git clone https://github.com/S-Q-Ali/VEO-Automation-Extension.git
   ```
2. Open `chrome://extensions/` and enable **Developer mode** (top right).
3. Click **Load unpacked** and select the cloned folder.
4. Click the puzzle piece icon to pin AutoFlow AI to the toolbar.
5. Verify — the side panel opens when you click the icon on a Google Flow tab.

> [!NOTE]
> To update, `git pull` and click the refresh icon on the extension card in `chrome://extensions/`.

---

## 🚀 Quick Start

1. Open [Google Flow](https://labs.google/fx/tools/flow) and sign in.
2. Click the AutoFlow AI icon to open the side panel.
3. Select a mode tab: **Image**, **Video**, or **Ingredients to Video**.
4. Enter prompts — one per blank line:
   ```
   A cat walking on a sunny beach

   A dog playing in the park
   ```
5. Configure settings (model, quantity, aspect ratio, auto-download, etc.).
6. Click **Run Automation**.
7. Monitor progress in the side panel's log and progress bars.
8. Completed files are automatically saved to your `Downloads/autoflow/` folder.

---

## 📖 Mode Guides

### 🎥 Video Mode

Use Veo models to generate short videos from text prompts.

1. Select the **Video** tab.
2. Choose a model: Omni Flash, Veo 3.1 (Lite / Fast / Quality / Lower Priority).
3. Set duration (4s–10s), aspect ratio (16:9 / 9:16), and quantity (1x–4x).
4. Enable **Chain Mode** if you want generated images from the previous run to be used as the video's start frame.
5. Enter prompts (separated by blank lines).
6. Click **Run Automation**.

> [!NOTE]
> Chain Mode in Video mode uses the most recently generated image from the Image mode's last successful output as the video's reference frame.

### 🖼️ Image Mode

Use Nano Banana models to generate images from text.

1. Select the **Image** tab.
2. Choose a model (Nano Banana Pro / Nano Banana 2).
3. Set aspect ratio (16:9 / 4:3 / 1:1 / 3:4 / 9:16) and quantity.
4. Enter prompts (separated by blank lines).
5. Click **Run Automation**.

### 🎬 Ingredients to Video Mode

Upload images and pair each with a unique prompt to generate videos.

1. Select the **Ingredients to Video** tab.
2. Upload images by clicking the upload zone or drag-and-drop.
3. Enter prompts in the textarea — **one prompt per image, separated by blank lines**:
   ```
   A cat on a sofa

   A dog in a park
   ```
4. Each image row shows a `#1`, `#2`, … badge indicating which prompt pairs with it. The prompt text appears in the inline input and is editable.
5. The **Run** button enables only when the prompt count matches the image count.
6. Click **Run Ingredients to Video**.

> [!TIP]
> After pairing, you can edit individual prompts inline before running. Changing the textarea re-syncs all prompts.

---

## ⚙️ Settings Reference

### General

| Setting | Options | Description |
|---------|---------|-------------|
| Model | Per-mode model list | AI model used for generation |
| Quantity | 1x / x2 / x3 / x4 | Number of outputs per prompt |
| Aspect Ratio | 16:9 / 9:16 / 4:3 / 1:1 / 3:4 | Output dimensions |
| Duration | 4s / 6s / 8s / 10s | Video length (Video / Ingredients modes) |

### Download & Retry

| Setting | Options | Description |
|---------|---------|-------------|
| Auto Download | Off / 1K / 2K / 4K (Image); Off / 270p–4K (Video) | Quality level for automatic saving |
| Auto Retry | On/Off + max (1–10) | Automatically retry on failure |
| Random Delay | Min–Max seconds (0–60) | Random wait between prompts to avoid rate limits |

### Folders

| Setting | Description |
|---------|-------------|
| Save Folder | Custom subfolder under `Downloads/`. Default: `autoflow` |

> [!WARNING]
> If auto-download is not working, go to Chrome Settings → Downloads and turn off "Ask where to save each file before downloading".

---

## 📂 File Import

You can populate prompts without typing by uploading files:

- **`.txt`** — one prompt per blank line (same format as the textarea)
- **`.csv`** — prompts in the first column, one per row

Click **Upload Bulk File** in any mode tab and select your file. The prompts replace the current textarea content.

---

## 📋 Queue Management

- The queue panel shows all pending, running, completed, and failed items.
- **Pause** pauses execution after the current item finishes; **Skip** moves to the next item.
- **Clear Completed** removes all finished items from the queue list.
- Completed items show a ✓, failed items show a ✗ with retry state.

---

## 💡 Tips & Best Practices

1. **Rate Limits** — Set random delay to 20–30 seconds and avoid running more than 3–4 concurrent queues to stay under Google's rate limits.

2. **Prompt Format** — Always separate prompts by **at least one blank line**. Single newlines are part of the same prompt.

3. **Ingredients Mode** — Upload images first, then type prompts. The count badge (`#1`, `#2`, …) confirms which prompt pairs with which image.

4. **Unusual Activity Error** — Before running automation, ensure you can manually submit 2–3 prompts on Google Flow without errors. If you hit a CAPTCHA, clear the cache via the **Clear Cache** button in the log panel.

5. **Download Folder** — Use unique folder names per project to keep outputs organized. The folder is created automatically under `Downloads/`.

---

## 🔧 Troubleshooting

| Issue | Cause & Solution |
|-------|------------------|
| **"Google Flow Required" error** | The extension only works on `labs.google/fx/tools/flow`. Open Google Flow in the active tab. |
| **Unusual Activity / Verification prompt** | Google flagged your session. Click **Clear Cache** in the log header, try a new project, or use a fresh browser profile. |
| **Downloads not saving** | Chrome's "Ask where to save" dialog blocks automation. Turn it off in Chrome Settings → Downloads. |
| **Policy Error during generation** | Google rejected the prompt/image content. The extension skips the item and continues automatically. |
| **Screen zoomed out or buttons not found** | The extension auto-zooms to locate UI elements. Do not resize the browser or zoom manually during automation. |
| **Queue stuck on one item** | Check the Google Flow tab for a visible error or CAPTCHA. Pause, resolve manually, then resume. |

---

## 🔒 Privacy

- **Local Execution** — All automation runs entirely inside your browser. Nothing is sent to external servers.
- **No Data Collection** — Your prompts, uploaded images, and settings are stored in Chrome's local storage only. AutoFlow AI does not collect, track, or transmit any personal data.
- **Open Source** — The full source code is available on [GitHub](https://github.com/S-Q-Ali/VEO-Automation-Extension) for audit.

---

## License

Licensed under the Apache License, Version 2.0.
Copyright © 2026 S-Q-Ali.

See the [LICENSE](https://github.com/S-Q-Ali/VEO-Automation-Extension/blob/main/LICENSE) for full terms.

---

*AutoFlow AI is an independent project and is not affiliated with, endorsed by, or connected to Google or the Google Flow team.*
