# AutoFlow AI — User Guide

## Table of Contents

1. [Overview](#overview)
2. [Installation](#installation)
3. [Dashboard Overview](#dashboard-overview)
4. [Image Mode](#image-mode)
5. [Video Mode](#video-mode)
6. [Ingredients to Video Mode](#ingredients-to-video-mode)
7. [Queue System](#queue-system)
8. [Auto-Retry](#auto-retry)
9. [Auto-Download](#auto-download)
10. [Site Lock](#site-lock)
11. [Troubleshooting](#troubleshooting)
12. [Tips & Best Practices](#tips--best-practices)

---

## Overview

AutoFlow AI is a Chrome extension that automates prompt submission on Google Flow. It queues multiple prompts, applies settings (model, aspect ratio, duration, etc.), and monitors generation progress — all from a side panel.

**3 modes:**
- **Image** — Generate images from text prompts
- **Video** — Generate videos from text prompts
- **Ingredients to Video** — Generate videos from uploaded images with paired prompts

---

## Installation

1. Clone the extension repository:
   ```bash
   git clone https://github.com/S-Q-Ali/VEO-Automation-Extension.git
   ```
2. Open `chrome://extensions` in Chrome
3. Enable **Developer Mode** (toggle at top right)
4. Click **Load unpacked** and select the cloned folder
5. The AutoFlow AI icon appears in the toolbar

> If the icon is hidden, pin it via the puzzle piece icon in the toolbar.

---

## Dashboard Overview

### Header
- **AutoFlow AI** — brand name with version badge
- **?** button — opens this user guide in a new tab

### Mode Tabs
- **Image** — text-to-image generation
- **Video** — text-to-video generation
- **Ingredients to Video** — image-to-video with paired prompts

### Settings Section
Each mode has its own settings panel:
- **AI Model** — selects the model in Google Flow
- **Quantity** — number of outputs per prompt
- **Aspect Ratio** — output dimensions
- **Auto Download** — quality tier for automatic download
- **Duration** (Video modes) — video length
- **Chain Mode** — use previous output as reference
- **Random Delay** — wait time between prompts
- **Auto Retry** — failure recovery settings
- **Save Folder** — custom download subfolder

### Queue Section
Appears when automation is running:
- **Queue count** — items pending/completed/failed
- **Pause/Resume** — controls queue flow
- **Skip** — skip the current running item
- **Clear Completed** — remove finished items
- Per-item **Retry** — retry individual failed items

### Status Bar
- Current automation status (Idle, Typing prompt, Generating, etc.)
- Live progress bars during generation

---

## Image Mode

### Workflow

1. Select the **Image** tab
2. Configure settings (Model, Quantity, Aspect Ratio, Auto Download)
3. Enter prompts in the textarea — **separate each prompt by a blank line**
4. Optionally upload a `.txt` or `.csv` file via **Bulk Upload**
5. Toggle **Chain Mode** if each prompt should use the previous output as a reference image
6. Set **Random Delay** to avoid rate limiting
7. Click **Run Automation**

### Settings Explained

| Setting | Description |
|---------|-------------|
| AI Model | Nano Banana Pro, Nano Banana 2 |
| Quantity | 1x–x4 generations per prompt |
| Aspect Ratio | 16:9, 4:3, 1:1, 3:4, 9:16 |
| Auto Download | Off, 1K, 2K, 4K |
| Chain Mode | Automatically attaches previous generation as reference |
| Random Delay | 0–60s wait between prompts |

### Chain Mode

When enabled, after each successful generation, AutoFlow:
1. Clicks **Create** to open the add-menu
2. Clicks **Sort** → selects **Latest**
3. Clicks **Add to request** to attach the generated image
4. Types the next prompt using the attached reference

> **Locale note:** If Google Flow is set to Turkish, Chain Mode may fail because `SORT_LATEST` and `ADD_TO_REQUEST_BTN` locators expect English text. Switch Flow to English for reliable chain mode operation.

---

## Video Mode

### Workflow

Identical to Image mode with additional settings:

| Setting | Description |
|---------|-------------|
| Duration | 4s, 6s, 8s, 10s |
| Aspect Ratio | 16:9 or 9:16 (portrait/landscape) |
| Auto Download | Off, 270p (GIF), 720p, 1080p, 4K |

### Settings Explained

Same as Image mode, plus:
- **Duration** — sets the video length in the generation menu
- **Auto Download quality ** — 270p exports as animated GIF; 720p+ exports as video

---

## Ingredients to Video Mode

### Workflow

1. Select the **Ingredients to Video** tab
2. Configure settings (Model, Aspect Ratio, Duration, etc.)
3. **Upload images** — drag & drop onto the upload zone, or click to browse
4. **Enter prompts** — either:
   - Type prompts in the textarea (one per blank line, paired 1:1 with images)
   - Or type individual prompts in each image's input field (fallback when textarea is empty)
5. **Match count** — number of images must equal number of prompts (textarea mode) or every image must have a non-empty prompt (per-image mode)
6. **Sort images** — reorder by drag-and-drop, or use sort menu (Newest, Oldest, A–Z, Z–A)
7. Click **Run Ingredients to Video**

### Prompt Pairing

When using the textarea, prompts are paired 1:1 with images in order:

```
Image 1  ←  Prompt 1
Image 2  ←  Prompt 2
Image 3  ←  Prompt 3
```

Counts must match exactly. If textarea has 5 prompts, you must upload exactly 5 images.

### Image Upload Tips

- Supported formats: PNG, JPG, WEBP
- Filenames are preserved and displayed in the queue
- A–Z sorting uses natural numeric order: `1, 2, 3, ..., 10, 11` (not `1, 10, 11, ..., 2, 20`)
- Remove individual images with the ✕ button
- Reorder by dragging the ⠿ handle

---

## Queue System

### Lifecycle

```
Pending → Running → Completed
                   → Failed → Retry → Pending
                   → Skipped
```

### Controls

| Button | When available | Effect |
|--------|---------------|--------|
| **Pause** | Queue is running | Pauses after current item completes |
| **Resume** | Queue is paused | Continues from next pending item |
| **Skip** | An item is running | Skips the current item (marks it skipped) |
| **↻ Retry** | Item is failed | Resets a failed item to pending for retry |
| **✕ Remove** | Any non-running item | Removes the item from the queue |
| **Clear Completed** | Any completed/skipped items | Removes all done items |

### Resume After Failure

When you pause after a failure:
1. The queue skips failed items and continues with pending ones
2. To retry a failed item, click **↻ Retry** on that item
3. Then click **Resume** — it will be processed before remaining pending items

> The queue state is held in memory. If the Chrome service worker is restarted, the queue is lost.

---

## Auto-Retry

When a generation fails, AutoRetry can automatically retry.

| Setting | Effect |
|---------|--------|
| **Auto Retry** (checkbox) | Enables/disables automatic retries |
| **Max retries** | Number of retry attempts before marking as failed (1–10) |

**What counts as failure:**
- Text box not found
- Run button not found after typing prompt
- Generation completes but auto-download fails after exhausting upscale attempts

**Retry behavior:**
- 5-second delay between retries (cancellable via Pause or Stop)
- Each retry re-types the full prompt (and re-uploads the image in Ingredients mode)
- After all retries exhausted, item is marked `failed` and queue moves to next item

---

## Auto-Download

### Quality Tiers

| Mode | Available Qualities |
|------|--------------------|
| Image | Off, 1K (Original), 2K (Upscaled), 4K (Upscaled) |
| Video | Off, 270p (GIF), 720p (Original), 1080p, 4K |

### Flow

1. After generation completes, the automation locates the first tile
2. Hovers over the tile and clicks the **3-dots menu** (⋮)
3. Clicks **Download** in the Radix menu
4. Waits for the quality submenu to appear (side-right)
5. Clicks the selected quality tier
6. Waits for the upscale/download to complete
7. **Redirects the download** to `Downloads/{folder}/{filename}`

### Save Folder

Each mode has its own **Save Folder** setting. Default is `autoflow`. Files are saved to:
```
Downloads/autoflow/{filename.ext}
```

Click the 📂 button to open Chrome's download settings.

---

## Site Lock

AutoFlow AI only operates on Google Flow at:
- `labs.google/fx/tools/flow`
- `labs.google/fx/tr/tools/flow`

### If you see the error overlay:

> **Google Flow Required** — AutoFlow AI needs Google Flow open and active.

**Solution:** Click **Open Google Flow** to open it in a new tab, or navigate to the correct URL manually. The panel will auto-detect when you switch to the correct tab.

### Why?

The site lock prevents the automation from accidentally running on other websites, which could disrupt your browsing or cause unintended interactions.

---

## Troubleshooting

### "4K not found" in auto-download

The quality submenu didn't render. This is a timing issue with Google Flow's Radix menu system. The automation retries up to 10 times. If it persists, lower the quality to 1K or try running during less congested times.

### Chain mode fails

If `SORT_LATEST` or `ADD_TO_REQUEST_BTN` are not found, check your Google Flow locale. These locators expect English text. Switch Flow's display language to English.

### "Failed to attach reference image"

The sort/add-to-request flow in chain mode encountered a UI state issue. Try:
1. Ensuring Google Flow language is English
2. Manually opening the Create menu to verify the UI state
3. Checking if the Google Flow UI has updated (may need locator updates)

### Queue stops unexpectedly

If the queue stops mid-run:
1. Check the console logs (`chrome://extensions` → service worker link)
2. Look for `❌ [FAIL]` or `❌ [FAIL] Locator Missing` entries
3. This usually indicates a Google Flow UI change or a temporary loading issue

### Locator Missing: SORT_LATEST / ADD_TO_REQUEST_BTN

This happens in chain mode when the Google Flow UI is in a non-English language. The locators search for English text strings. Switch Flow to English, or add Turkish locale fallbacks to the content script.

### Extracting logs for debugging

1. Right-click the AutoFlow AI icon → **Inspect popup**
2. Go to the **Console** tab
3. Logs are prefixed with `✅ [PASS]`, `⚠️ [WARN]`, `❌ [FAIL]`
4. Use **Copy** button in the side panel to copy logs to clipboard

---

## Tips & Best Practices

- **Use descriptive prompts** — better prompts produce better results
- **Bulk upload** — for 50+ prompts, use `.txt` or `.csv` files instead of typing
- **Random delay** — set 20–30s delay between prompts to avoid rate limiting
- **Auto-download** — enable for unattended runs; disable for manual review
- **Save folder** — use different folders for different projects
- **Chain mode** — ensure Google Flow language is English for reliable operation
- **Image filenames** — name your images with numbered prefixes (01, 02, ..., 10) for predictable sort order
- **Batch size** — for long queues (50+ items), run in smaller batches if you encounter timeouts
- **Keep Chrome active** — don't minimize the browser window; background tabs may throttle
