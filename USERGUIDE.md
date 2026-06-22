# AutoFlow AI — User Guide

**Auto VEO & Nano Banana**

---

## Overview

AutoFlow AI is a Chrome browser extension that fully automates the media generation workflow on Google Labs (VideoFX / ImageFX). It eliminates the need for manual prompt entry, button clicking, and file downloading by orchestrating the browser UI programmatically.

The extension supports three primary modes of operation:

- **Image Mode** — Generate still images from text descriptions using ImageFX.
- **Video Mode** — Generate short-form videos from text prompts using VideoFX / Veo.
- **Ingredients to Video Mode** — A two-stage pipeline that first generates an image from source ingredients and then uses that image as the seed for video generation.

Additional features include a configurable queue system with bulk prompt support, automatic retry with exponential backoff, configurable inter-action delays to avoid rate limiting, auto-download of completed files, and real-time logging with progress tracking. Chain Mode links image outputs directly into video inputs, creating a seamless automated pipeline.

The extension is designed for content creators, researchers, and developers who need to generate media at scale without repetitive manual labor.

---

## Requirements

### Browser
- Google Chrome version 114 or later (Chromium-based browsers are not officially supported).
- JavaScript must be enabled.
- Pop-up and redirect permissions must be allowed for `labs.google`.

### Google Account
- A Google account with access to Google Labs (VideoFX and/or ImageFX).
- Some features (particularly VideoFX / Veo video generation) may require being on a waitlist or having a specific Google Workspace tier.
- You must be signed into your Google account in Chrome prior to running the extension.

### System
- **Operating System:** Windows 10 or later, macOS 11+, or Linux (X11/Wayland).
- **RAM:** 4 GB minimum (8 GB recommended for large queues).
- **Storage:** Sufficient free space for downloaded images and videos (videos average 5–50 MB each).
- **Network:** Broadband internet connection (10 Mbps or faster recommended for stable operation).

### Permissions
The extension requests the following permissions:

| Permission | Purpose |
|-----------|---------|
| `storage` | Saves your configuration and queue state locally |
| `tabs` | Interacts with Google Labs tabs |
| `downloads` | Saves generated media files to your computer |
| `host_permissions` (`labs.google`) | Reads and writes UI elements on the Google Labs domain |
| `clipboardRead` | Optionally reads copied prompts |
| `alarms` | Schedules retry and delay timers |

---

## Installation

### From Source (Development)

1. Clone the repository:
   ```bash
   git clone https://github.com/S-Q-Ali/VEO-Automation-Extension.git
   ```
2. Open Google Chrome and navigate to `chrome://extensions/`.
3. Enable **Developer mode** using the toggle in the top-right corner.
4. Click the **Load unpacked** button.
5. Navigate to the cloned repository directory and select the extension folder (the one containing `manifest.json`).
6. The AutoFlow AI icon will appear in the Chrome toolbar. Click the puzzle piece icon to pin it if it does not appear automatically.
7. Verify the extension loaded successfully by checking that no errors appear on the extension management page.

### From Chrome Web Store (if published)

1. Visit the AutoFlow AI listing on the Chrome Web Store.
2. Click **Add to Chrome**.
3. Confirm the permission prompt.
4. The extension will install automatically and appear in the toolbar.

### Verifying Installation

- The extension icon should display the AutoFlow AI logo (a stylized "AF" or flow icon).
- Click the icon to open the popup. If the popup displays the configuration panel, installation succeeded.
- Open `chrome://extensions/` and confirm the extension is enabled with no error messages.

---

## Quick Start

Follow these steps to generate your first video in under two minutes:

1. **Open Google Labs** — Navigate to `https://labs.google` and sign in with your Google account. Open VideoFX or ImageFX in a separate tab.
2. **Open the extension** — Click the AutoFlow AI icon in the Chrome toolbar.
3. **Enter prompts** — In the **Prompts** text area, type one prompt per line. For example:
   ```
   A cat walking on a tightrope
   A dog surfing on a wave
   ```
4. **Select Mode** — Choose **Video** from the mode dropdown.
5. **Configure settings** — Set aspect ratio to 16:9, duration to 5 seconds, and enable Auto-Download.
6. **Start** — Click the green **Start** button. The extension will take control of the Google Labs tab.
7. **Monitor** — Watch the **Logs** panel for real-time status updates.
8. **Collect results** — Completed videos are automatically saved to your Downloads folder.

> **Do not interact with the browser tab while automation is running.** Moving the mouse, clicking, or typing can interfere with the extension's control flow and cause errors.

---

## Configuration

The extension provides granular control over every stage of the automation pipeline. Settings are organized by mode and saved automatically to Chrome's local storage.

### Global Settings

These settings apply regardless of the selected mode:

| Setting | Default | Range | Description |
|---------|---------|-------|-------------|
| Auto-Download | On | On / Off | Automatically save generated media to disk |
| Auto-Retry | On | On / Off | Automatically retry failed generations |
| Max Retries | 3 | 0–10 | Maximum retry attempts per prompt |
| Delay Between Prompts | 5000 ms | 1000–60000 | Pause after completing one prompt before starting the next |
| Delay Between Actions | 1000 ms | 200–10000 | Pause between individual UI interactions (mouse clicks, keystrokes) |
| Delay on Error | 10000 ms | 2000–120000 | Pause before retrying after an error |
| Chain Mode | Off | On / Off | Pipe image output into video input automatically |
| Max Concurrency | 1 | 1–5 | Number of tabs to process simultaneously (experimental) |

### Image Mode

Generates still images using Google ImageFX. Configure these parameters before starting automation:

| Setting | Options | Description |
|---------|---------|-------------|
| Model | ImageFX v1, ImageFX v2 | The image generation model version |
| Aspect Ratio | 1:1 (square), 16:9 (landscape), 9:16 (portrait), 4:3, 3:4 | Output image dimensions |
| Number of Images | 1, 2, 4 | How many variations to generate per prompt |
| Image Style | Auto, Photorealistic, Cinematic, Anime, Minimalist, 3D Render, Oil Painting | Visual style guidance (some models only) |
| Negative Prompt | (text input) | Things to exclude from the generated image |
| Seed | (number, optional) | Fixed seed for reproducible results |
| Auto-Download | On / Off | Automatically save the generated images |

**Prompt tips for Image Mode:**
- Be descriptive: include subject, action, environment, lighting, and mood.
- Example: *"A serene Japanese garden at sunset with cherry blossoms falling, soft golden lighting, photorealistic style."*
- Use negative prompts to remove unwanted elements: *"blurry, low quality, watermark, text"*

### Video Mode

Generates short videos from text descriptions using VideoFX / Veo. Configuration options:

| Setting | Options | Description |
|---------|---------|-------------|
| Model | Veo 1, Veo 2, Veo 3 | The video generation model (availability depends on account tier) |
| Aspect Ratio | 16:9 (landscape), 9:16 (portrait), 1:1 (square) | Output video dimensions |
| Duration | 5s, 8s | Video length in seconds |
| FPS | 24, 30 | Frames per second (24 is default; 30 may not be available on all models) |
| Style Reference | (image upload, optional) | Reference image for visual style |
| Seed | (number, optional) | Fixed seed for reproducible results |
| Auto-Enhance Prompt | On / Off | Automatically expand short prompts with descriptive detail |
| Auto-Download | On / Off | Automatically save the generated video |

**Prompt tips for Video Mode:**
- Describe motion explicitly: *"A cat walking on a tightrope, camera follows the cat, background is a circus tent"*
- Include camera direction: *"panning left", "zoom in", "tracking shot", "aerial view"*
- Specify lighting and atmosphere: *"golden hour", "neon-lit", "foggy morning", "underwater"*
- Keep prompts under 200 characters for best results.

### Ingredients to Video Mode

This is a two-stage pipeline that automatically links image generation to video generation. It is the most powerful mode and combines all settings from both Image and Video modes.

**How it works:**

1. **Stage 1 — Image Generation:** The extension generates an image from your "ingredients" prompt using Image Mode settings.
2. **Stage 2 — Video Generation:** The generated image is automatically uploaded as a style/image reference, and a video is generated from the same prompt using Video Mode settings.

**Configuration considerations:**
- All Image Mode settings apply to Stage 1.
- All Video Mode settings apply to Stage 2.
- Chain Mode **must** be enabled for this workflow (it is automatically enabled when selecting this mode).
- Stage 2 cannot begin until Stage 1 completes successfully.
- If either stage fails, the entire item is retried (subject to Max Retries).
- Images generated in Stage 1 are saved even if Chain Mode pipes them into Stage 2.

**Example use case:**
*Prompt:* "chocolate chip cookies on a cooling rack, rustic kitchen background"
- Stage 1 generates an image of cookies.
- Stage 2 uses that image + the same prompt to produce a video of the cookies.

---

## Running Automation

### Starting a Session

1. Ensure you have an active Google Labs tab open and signed in.
2. Open the extension popup.
3. Enter your prompts (one per line) in the **Prompts** text area.
4. Select a **Mode** and configure **Settings**.
5. Click **Start** (green button).

The extension will immediately:
- Activate the Google Labs tab.
- Navigate to the correct tool (VideoFX or ImageFX).
- Enter the first prompt.
- Click the generate button.
- Wait for the generation to complete.
- Download the result (if Auto-Download is on).
- Move to the next prompt in the queue.

### Stopping a Session

Click the **Stop** (red) button at any time. The extension will:
- Finish any in-progress generation.
- Save any completed results.
- Halt processing of remaining queue items.
- The queue is preserved so you can resume later.

### Queue Management

- **Add prompts:** Type or paste into the text area (one prompt per line).
- **Bulk import:** Paste 50+ prompts at once — the extension handles them sequentially.
- **Remove prompts:** Manually delete lines from the text area before starting.
- **Clear all:** Click **Clear** to empty the queue entirely.
- **Reorder:** Cut and paste lines to reorder.
- **Import from file:** Use the **Import** button to load prompts from a `.txt` file (one per line).

### Behavioral Notes

- The extension will scroll the Google Labs page as needed to find UI elements.
- If the browser tab is refreshed, the extension will detect the state change and re-navigate.
- The extension resets the prompt input between each generation to avoid cross-contamination.
- If multiple Google Labs tabs are open, the extension uses the most recently active one.

---

## Queue System

The queue is the central orchestrator of all automation workflows. Understanding its behavior is key to running efficient batch jobs.

### Architecture

The queue is a first-in-first-out (FIFO) data structure stored in Chrome's local storage. Each item contains:
- **Prompt text** — The user-provided description.
- **Status** — `pending`, `processing`, `completed`, or `failed`.
- **Retry count** — Number of attempts made so far.
- **Timestamps** — When processing started and finished.
- **Output metadata** — File name, size, and download URL (if available).

### Lifecycle of a Queue Item

```
pending → processing → completed (success)
                       → failed (exhausted retries)
```

1. **Pending** — Item is waiting in line. No action has been taken.
2. **Processing** — The extension is currently generating media for this item.
3. **Completed** — Generation succeeded; media was downloaded (if enabled).
4. **Failed** — All retry attempts were exhausted without success.

### Bulk Operations

| Operation | How To |
|-----------|--------|
| Add single prompt | Type in the text area and press Enter |
| Add multiple prompts | Paste a newline-separated list |
| Import from file | Click **Import** and select a `.txt` file |
| Clear queue | Click **Clear** |
| View queue status | The popup displays item count and status summary |

### Persistence

- The queue **persists** across popup open/close cycles until explicitly cleared or all items complete.
- Closing Chrome or the extension tab does not destroy the queue.
- Resetting the extension via `chrome://extensions/` will clear the queue.

### Limitations

- Maximum practical queue size: ~500 items (performance may degrade beyond this).
- Duplicate prompts are **not** automatically filtered.
- Queue reordering is manual only (cut and paste).

---

## Auto-Download

When enabled, Auto-Download saves every successfully generated file to your computer automatically, eliminating the need to manually click download buttons for each result.

### How It Works

1. After generation completes, the extension waits for the download link/button to appear on the page.
2. The extension uses Chrome's downloads API to trigger the save.
3. Chrome's default download behavior is used (as configured in Chrome settings).
4. A log entry is created for each download with the file path.

### File Naming Convention

Files are saved with a structured name to aid organization:

```
AutoFlow_YYYYMMDD_HHMMSS_{prompt-slug}.{ext}
```

- `YYYYMMDD` — Current date in year-month-day format.
- `HHMMSS` — Current time in 24-hour format.
- `prompt-slug` — First 40 characters of the prompt, lowercased with spaces replaced by hyphens, with non-alphanumeric characters removed.
- `ext` — File extension (`png`, `webp`, `mp4`, `webm` depending on format).

**Example:** `AutoFlow_20260315_143022_a-cat-walking-on-a-tightrope.mp4`

### Download Location

Files are saved to Chrome's default download directory. To change this:
1. Open Chrome settings.
2. Navigate to **Downloads**.
3. Change the **Location** field.

### Disabling Auto-Download

If you prefer to manually manage files, toggle Auto-Download **Off** in the settings panel. You can then download files manually from the Google Labs interface after generation completes.

### Known Issues

- Chrome may block multiple rapid downloads. The extension respects this and spaces out downloads.
- Some corporate-managed Chrome installations may restrict the downloads API. Auto-Download will fail silently in this case.

---

## Auto-Retry & Delay

### Auto-Retry System

The retry system ensures transient failures do not waste your queue. When a generation attempt fails, the extension logs the error, waits for the configured delay, and retries.

**Retry triggers:**
- UI element not found after waiting for it to appear.
- Generation timeout (generation taking longer than the expected maximum).
- Download link did not appear after generation completed.
- Network errors or page navigation failures.
- Unexpected page state (e.g., popup overlays, consent dialogs).

**Retry behavior:**
- Each retry is a full attempt from the beginning of that prompt's workflow (not a resume).
- Retry count increments with each attempt.
- Once `Max Retries` is reached, the item is marked as `failed` and skipped.
- The queue moves to the next item automatically after a failure.
- A warning log entry is created for each retry attempt.

**Counters example:**

| Attempt | Action |
|---------|--------|
| 1 | Initial attempt — fails |
| 2 | First retry — fails |
| 3 | Second retry — succeeds |
| 4+ | Not attempted (max 3, already exhausted) |

### Delay Configuration

Delays prevent the extension from overwhelming Google's servers and triggering rate limits or CAPTCHAs.

**Delay Between Prompts** (default: 5000 ms)
The pause after a prompt completes (success or failure) before starting the next one. Increase this if you encounter rate limiting.

**Delay Between Actions** (default: 1000 ms)
The pause between individual UI interactions such as clicking a button, typing text, or waiting for a page transition. Lower values speed up execution but increase the risk of detection.

**Delay on Error** (default: 10000 ms)
The pause before the first retry attempt after an error. A longer delay gives transient issues (e.g., network blips) time to resolve.

### Recommended Delay Profiles

| Profile | Between Prompts | Between Actions | On Error | Use Case |
|---------|---------------|----------------|----------|----------|
| Fast | 2000 ms | 500 ms | 5000 ms | Small test queues, quick iteration |
| Balanced | 5000 ms | 1000 ms | 10000 ms | General production use (default) |
| Safe | 10000 ms | 2000 ms | 30000 ms | Avoiding rate limits, large queues |
| Stealth | 20000 ms | 3000 ms | 60000 ms | Maximum caution, overnight runs |

---

## Chain Mode

Chain Mode creates automated pipelines by linking the output of one generation stage as the input to the next.

### How Chain Mode Works

When Chain Mode is enabled:
1. **Stage 1** runs to completion (typically image generation).
2. The **output file** (image) from Stage 1 is tracked via its download path or URL.
3. A **link step** runs that uploads or references the Stage 1 output as the seed/input for Stage 2.
4. **Stage 2** (typically video generation) runs with the seeded input.
5. Both outputs are saved independently.

### When to Use Chain Mode

- **Ingredients to Video:** Always required. Without Chain Mode, the image and video stages are disconnected.
- **Image → Video:** Manually enable when you want any image output to seed a video generation.
- **Multi-turn refinement:** Run the same prompt through Image mode, then use Chain Mode to generate a video from the refined image.

### Chain Mode vs. Standalone Mode

| Aspect | Chain Mode | Standalone Mode |
|--------|-----------|-----------------|
| Stages | 2+ linked stages | Single stage only |
| Image Output | Saved + piped forward | Saved only |
| Video Input | Auto-seeded from image | Text prompt only |
| Use Case | Ingredients to Video | Direct image or video generation |
| State Management | Complex (tracks cross-stage references) | Simple |

### Limitations

- Chain Mode currently supports a maximum of 2 stages (image → video).
- Both stages must use the same queue item and prompt.
- If Stage 1 fails, Stage 2 is not attempted.
- Stage 2 cannot run independently of Stage 1.

---

## Logs & Progress

The Logs & Progress panel gives you full visibility into what the extension is doing at every moment.

### Log Panel

The log panel is a scrollable, color-coded list of timestamped events.

**Log levels and colors:**

| Level | Color | Meaning |
|-------|-------|---------|
| INFO | Blue | Normal operation events (navigating, typing, waiting) |
| SUCCESS | Green | A generation completed successfully |
| WARNING | Yellow | Non-fatal issues (retry attempt, slow operation) |
| ERROR | Red | Failures (element not found, timeout, network error) |
| DEBUG | Gray | Detailed diagnostic output (visible in debug mode only) |

**Sample log output:**
```
[14:30:22] INFO    Starting prompt 3/12: "a cat walking on a tightrope"
[14:30:23] INFO    Navigating to VideoFX
[14:30:24] INFO    Typing prompt into input field
[14:30:25] INFO    Clicking Generate button
[14:30:45] INFO    Waiting for generation to complete...
[14:31:10] SUCCESS Generation completed
[14:31:11] INFO    Downloading video...
[14:31:12] SUCCESS Downloaded: AutoFlow_20260315_143022_a-cat-walking-on-a-tightrope.mp4
[14:31:13] INFO    Delay before next prompt (5000 ms)
```

### Progress Tracking

- **Progress bar** at the top of the popup shows overall queue completion as a percentage.
- **Status indicator** displays: `Processing 3/12` or `Completed 8/12 | Failed 1/12`.
- **Elapsed time** counter shows how long the current session has been running.
- **Estimated time remaining** is calculated based on average time per prompt.

### Clearing Logs

- Click **Clear Logs** to empty the log panel. This does not affect the queue.
- Logs are stored in memory only and are lost when the popup is closed.
- For persistent logging, use the **Export Logs** feature (saves to a `.txt` file).

### Exporting Logs

Click **Export Logs** to save the current session logs to a timestamped text file in your Downloads folder. This is useful for:
- Sharing with developers when reporting bugs.
- Auditing a long queue run.
- Keeping a permanent record of which prompts succeeded or failed.

### Debug Mode

Enable **Debug Mode** in the settings to see additional log entries:
- Raw DOM element queries and their results.
- Network request interceptions.
- Internal state transitions.
- Timing information for each action.

Debug logs can be verbose; only enable when troubleshooting.

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+Enter` (or `Cmd+Enter`) | Start automation |
| `Ctrl+Shift+Enter` | Stop automation |
| `Ctrl+L` | Clear logs |
| `Ctrl+Shift+C` | Clear queue |
| `Ctrl+I` | Import prompts from file |

Shortcuts are configurable via `chrome://extensions/shortcuts`. Note that shortcuts only work when the extension popup is focused.

---

## Tips & Best Practices

### Prompt Engineering

- **Be specific.** "A cat" produces worse results than "A fluffy orange tabby cat stretching after a nap on a windowsill at golden hour."
- **Describe motion** (for video): use action words like *walking, running, flying, spinning, growing, dissolving.*
- **Include camera direction:** *close-up, wide shot, tracking, dolly zoom, bird's eye view.*
- **Specify mood and lighting:** *dark and moody, bright and cheerful, neon-lit cyberpunk, soft natural light.*

### Queue Management

- **Test with 1–2 prompts** before running a large batch to verify settings are correct.
- **Name your prompts clearly** — since the filename is derived from the prompt, descriptive prompts produce recognizable files.
- **Avoid excessively long prompts** (over 500 characters) as they may cause UI truncation in Google Labs.
- **Stagger large queues** — for 100+ prompts, consider splitting into batches of 50 to avoid prolonged browser sessions.

### Stability

- **Close unnecessary tabs** to free system resources during long runs.
- **Disable sleep/hibernation** on your computer when running overnight queues.
- **Use Chrome's built-in task manager** (`Shift+Esc`) to monitor memory usage.
- **Restart Chrome** before long runs to clear accumulated memory.

### Rate Limit Avoidance

- Use the **Safe** delay profile for queues over 50 items.
- Avoid running multiple instances of the extension simultaneously.
- Space out large batch runs by at least 30 minutes.
- If you encounter CAPTCHAs, increase delays significantly.

### File Organization

- Consider creating subfolders in your Downloads directory for different projects.
- The timestamp in filenames naturally sorts files chronologically.
- Use a spreadsheet to map prompts to output filenames for easy reference.

---

## How It Works (Technical Overview)

AutoFlow AI operates by injecting JavaScript into the Google Labs page to interact with its DOM (Document Object Model). It does not use any undocumented APIs or network interception.

### Workflow Engine

1. **State machine** — The extension uses a deterministic state machine to track which step of the workflow it is on.
2. **Element queries** — UI elements are located using CSS selectors and XPath expressions that target known Google Labs component structures.
3. **Action execution** — Once an element is found, the extension dispatches native DOM events (click, input, change) that simulate user interaction.
4. **Polling** — The extension polls for expected UI changes (e.g., generation completion indicator) using `MutationObserver` and timed intervals.
5. **Fallback logic** — If an expected element is not found within the timeout, the extension triggers the error handling and retry system.

### Communication Architecture

```
Popup (UI) ←→ Background Script (Controller) ←→ Content Script (DOM Driver)
                      ↕
              Chrome Storage API (State)
```

- **Popup:** React-based user interface for configuration and monitoring.
- **Background script:** Persistent service worker that manages the queue, timers, and orchestrates workflows.
- **Content script:** Injected into the Google Labs page; performs all DOM interactions.
- **Storage API:** Persists queue state, configuration, and logs across sessions.

### Security Model

- No data is ever sent to external servers.
- All processing occurs locally in the browser.
- The extension only interacts with `labs.google.com` domains.
- No analytics, tracking, or telemetry is included.

---

## Updating

### Automatic Updates

If installed from the Chrome Web Store, updates are applied automatically when Chrome detects a new version.

### Manual Updates (Unpacked Installation)

1. Pull the latest changes:
   ```bash
   git pull origin main
   ```
2. Navigate to `chrome://extensions/`.
3. Click the **Refresh** icon on the AutoFlow AI card.
4. Alternatively, click **Load unpacked** again and select the updated directory.

### Checking Your Version

1. Open the extension popup.
2. Click the settings gear icon.
3. The version number is displayed at the bottom of the settings panel.
4. Compare with the latest version on the GitHub releases page.

### Migration Notes

- Configuration and queue state are stored in Chrome's local storage and persist across updates.
- If breaking changes occur, the extension will display a migration notice in the logs.
- Downgrading may cause state incompatibility; clear storage if you need to roll back.

---

## Uninstalling

1. Navigate to `chrome://extensions/`.
2. Find AutoFlow AI in the list.
3. Click **Remove**.
4. Confirm the removal prompt.

Uninstalling clears all extension data including configuration, queue state, and logs. Downloaded media files on your disk are **not** affected.

---

## Support

### Reporting Issues

If you encounter a bug or unexpected behavior:

1. **Check the Troubleshooting section** of this guide first.
2. **Enable Debug Mode** in the extension settings.
3. **Reproduce the issue** and capture the logs using **Export Logs**.
4. **Open a GitHub issue** at:
   ```
   https://github.com/S-Q-Ali/VEO-Automation-Extension/issues
   ```
5. Include the following in your report:
   - Chrome version (from `chrome://version/`)
   - Extension version
   - The exported log file
   - A description of the expected vs. actual behavior
   - Screenshots or screen recordings (if applicable)
   - Whether the issue is reproducible every time or intermittent

### Feature Requests

Submit feature requests via GitHub Issues with the label `enhancement`. Provide:
- A clear description of the feature.
- The problem it solves.
- Any implementation ideas you have.

### Community

- **GitHub Discussions:** For questions, tips, and community support.
- **Contributing:** See `CONTRIBUTING.md` in the repository for guidelines.

---

## Troubleshooting

### Common Errors

| Error Message | Cause | Solution |
|---------------|-------|----------|
| "Could not find input field" | Google Labs UI changed or page not loaded | Refresh the Labs tab and ensure it is fully loaded before starting |
| "Generation timed out" | Video generation took longer than expected | Increase the timeout in settings or check your internet connection |
| "Failed to click generate button" | Button not yet enabled or obscured | Ensure the prompt was entered successfully; increase delay between actions |
| "Download failed" | Chrome blocked the download or URL expired | Re-run the prompt; disable Auto-Download and download manually |
| "Session expired" | Google authentication token expired | Sign out and sign back into Google; refresh the Labs page |
| "Queue item stuck on processing" | Extension crashed mid-operation | Stop and restart; the item will be retried if retries remain |
| "Maximum retries exceeded" | Persistent failure across all attempts | Check logs for the specific error; adjust settings or prompt |
| "Cannot read properties of null" | DOM element not found | Usually indicates a Google Labs UI update; report on GitHub |

### Extension Not Starting

1. Verify the Google Labs tab is open and active.
2. Check that you are signed into your Google account.
3. Confirm the extension has the necessary permissions (`chrome://extensions/` → AutoFlow AI → Details).
4. Open the browser console (`F12`) on the Labs tab and check for errors.
5. Reload the extension from `chrome://extensions/`.

### Downloads Not Working

1. Ensure **Auto-Download** is enabled in settings.
2. Check Chrome's download location and permissions (`chrome://settings/downloads`).
3. Disable "Ask where to save each file before downloading" in Chrome settings.
4. Ensure your disk is not full.
5. Some corporate Chrome policies may block the downloads API.

### UI Element Detection Issues

If the extension reports it cannot find buttons or input fields:

- **Refresh the Google Labs page** and try again.
- **Disable other extensions** that may modify the Google Labs page.
- **Check for Google Labs updates** — Google frequently updates their UI.
- **Increase the "Delay Between Actions"** setting — the page may be slow to render.

### CAPTCHA / Rate Limiting

- **Signs:** Repeated failures, "try again later" messages, or CAPTCHA challenges appearing.
- **Solutions:**
  - Increase all delay settings to the **Safe** or **Stealth** profile.
  - Limit queues to 10–20 prompts per session.
  - Wait 1–2 hours between sessions.
  - Use a different Google account if available.

### Browser Performance

- **High memory usage:** Close other tabs and applications. Use Chrome's task manager to kill heavy processes.
- **Chrome becoming unresponsive:** This can happen with very long queues. Restart Chrome and resume with a smaller batch.
- **Tab crashing:** Usually due to memory pressure. Reduce queue size and increase delays.

### Debugging Checklist

Before reporting an issue, complete these steps:

- [ ] Refresh the Google Labs page.
- [ ] Restart Chrome completely.
- [ ] Reinstall the extension (remove and load unpacked again).
- [ ] Enable Debug Mode and capture logs.
- [ ] Test with a single, simple prompt.
- [ ] Disable all other extensions temporarily.
- [ ] Try a different Google account.

---

## FAQ

**Q: Is this extension affiliated with Google?**  
A: No. AutoFlow AI is an independent third-party project. It is not affiliated with, endorsed by, or sponsored by Google or Alphabet Inc.

**Q: Is using this extension against Google's terms of service?**  
A: Google's terms for Labs products prohibit automated access. This tool is provided for educational and research purposes. Use at your own risk. The developers are not responsible for any account restrictions that may result from its use.

**Q: Can I get banned for using AutoFlow AI?**  
A: There is a possibility that automated access could lead to account restrictions or loss of access to Labs features. The delays and interaction patterns are designed to mimic human behavior, but detection is always possible. Use responsibly.

**Q: Do I need a paid Google account?**  
A: No. A free Google account with Labs access is sufficient. However, some models (e.g., Veo 2/3) may require being on a waitlist or having a Google Workspace plan with Labs access.

**Q: Can I run this in headless mode?**  
A: No. The extension requires a visible, active browser tab. Headless Chrome or invisible automation is not supported.

**Q: Can I minimize the Chrome window while it runs?**  
A: Yes. You can minimize the window or switch to a different virtual desktop. Do not close the Labs tab or the Chrome window.

**Q: How many prompts can I queue at once?**  
A: There is no enforced limit. Practical limits depend on your system memory and network. Queues of 50–100 prompts are typical. Performance may degrade beyond 500 items.

**Q: Can I pause and resume the queue?**  
A: There is no pause/resume button. Click **Stop** to halt. Completed items are saved. To resume, remove completed prompts and start again with the remaining ones.

**Q: Does the extension work on mobile Chrome?**  
A: No. Chrome extensions are not supported on mobile browsers.

**Q: What file formats are generated?**  
A: Images are typically PNG or WebP. Videos are typically MP4 (H.264) or WebM (VP9), depending on what Google Labs outputs.

**Q: Where are my files saved?**  
A: Files are saved to your Chrome default download directory. On Windows this is usually `%USERPROFILE%\Downloads`. On macOS: `~/Downloads`. On Linux: `~/Downloads`.

**Q: Can I change the download folder?**  
A: Yes. Change Chrome's default download location in Chrome settings (`chrome://settings/downloads`).

**Q: The extension stopped working. What should I do?**  
A: Google Labs updates its UI frequently, which can break the element selectors the extension relies on. Check the GitHub repository for updates. If none are available, open an issue with the details.

**Q: Can I contribute to the project?**  
A: Yes! Contributions are welcome. See `CONTRIBUTING.md` in the repository for guidelines on code style, pull requests, and development setup.

**Q: How do I build the extension from source?**  
A: Clone the repository, run `npm install` to install dependencies, and then run `npm run build` to create the production build. The built extension will be in the `dist/` directory.

**Q: Does the extension collect any data?**  
A: No. All data stays in your browser's local storage. No analytics, telemetry, or external network requests are made by the extension.

**Q: Can I use this with other AI tools?**  
A: The extension is specifically designed for Google Labs (VideoFX / ImageFX). It will not work with other platforms without significant modification.

**Q: What is "Nano Banana"?**  
A: Nano Banana is the internal codename for the lightweight automation engine that powers the extension's DOM interaction layer. It handles element detection, event dispatch, and timing synchronization.

---

## Changelog

### v1.0.0
- Initial release
- Image Mode support
- Video Mode support
- Ingredients to Video Mode support
- Queue system with FIFO processing
- Auto-Download for images and videos
- Auto-Retry with configurable attempts
- Chain Mode for image-to-video pipelines
- Real-time logging and progress tracking
- Configurable delays and timing profiles
- Keyboard shortcuts
- Log export functionality

### v1.1.0
- Added Debug Mode for troubleshooting
- Improved element detection with fallback selectors
- Import prompts from file (.txt)
- Estimated time remaining in progress panel
- Performance improvements for large queues

### v1.2.0
- Chain Mode pipeline enhancements
- Support for Veo 2 and Veo 3 models
- Negative prompt support for Image Mode
- Prompt auto-enhance toggle
- Experimental concurrency support

---

## License

Licensed under the Apache License, Version 2.0.  
Copyright © 2026 S-Q-Ali.

See the [LICENSE](https://github.com/S-Q-Ali/VEO-Automation-Extension/blob/main/LICENSE) for full terms.
