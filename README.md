# GDrive Universal Downloader

[English](#english) | [中文](#中文)

---

<a name="english"></a>
## 🇬🇧 English

> ⚠️ **Legal Notice**: This script is intended only for files you own or have been authorized to access. Please comply with copyright laws and Google's Terms of Service.

A browser Console script that auto-detects Google Drive file types and applies the best download strategy.

### Supported Formats

| Type | Detection Method | Output Format |
|------|-----------------|---------------|
| 📄 View-Only PDF (download disabled) | `blob:https://drive.google.com/` img | `.pdf` |
| 📝 Google Docs | URL contains `/document/` | `.docx` |
| 📊 Google Sheets | URL contains `/spreadsheets/` | `.xlsx` |
| 📑 Google Slides | URL contains `/presentation/` | `.pptx` |
| 📋 Google Forms | URL contains `/forms/` | `.csv` |
| 🎨 Google Drawings | URL contains `/drawings/` | `.svg` |
| 🖼️ Image files | DOM `img` element | Original format |
| 🎬 Video files | XHR/Fetch intercept + DOM | `.mp4` / source URL |
| 🎵 Audio files | DOM `<audio>` element | Original format |
| 📁 Other files (PDF, Office, zip…) | `drive.google.com/file/d/` URL | Original format |

### Usage

> 🛡️ **Security Note**: Never paste code into your browser console unless you understand what it does. This script is open-source and runs entirely locally. It does not exfiltrate data, but we recommend you review the code before use.

**View-Only PDF**

1. Open the Google Drive PDF link in **Chrome** or **Edge**
2. Click the three-dot menu (top right) → **"Open in new window"**
3. Press `F12` to open Console
4. If Chrome shows a paste warning, type `allow pasting` and press Enter first
5. Paste the script below and press **Enter** — it will auto-scroll and download

**Video — Google Drive & YouTube (direct browser download)**

1. Open the Google Drive video page or **YouTube watch page**
2. Open Console (`F12`)
3. Paste and run the script
4. For YouTube: script starts **MediaRecorder** on the `<video>` element and records the live stream
5. Video plays automatically — **do not close or navigate away**
6. File downloads automatically when the video ends, or run `__gdrive_stopRecording()` to stop early

> **YouTube quality note**: The recorded quality matches whatever YouTube is currently streaming — usually 360p–720p depending on your connection and YouTube's ABR selection.

**All other formats**

Open the file preview in Google Drive, open Console, paste and run. It auto-detects the type.

### Video Strategy: How It Works

```
YouTube page detected
        │
        ▼
MediaRecorder.captureStream(<video>)
        │
        ▼
Records live stream in real-time
        │
        ▼
Video ends / __gdrive_stopRecording() called
        │
        ▼
Blob → auto-download (.webm / .mp4)
```

### Quality vs File Size (PDF)

#### Method A — Browser Zoom (no code change needed)

| Browser Zoom | Effect |
|-------------|--------|
| 75% | Smaller file, lower quality |
| 100% | Default balanced |
| 150% | Higher quality, larger file |
| 200% | Best quality (may slow down on large PDFs) |

#### Method B — Script Settings

| Setting | Value | Effect |
|---------|-------|--------|
| `SCALE` | `1.0` | Screen size — smallest file ← recommended |
| `SCALE` | `1.5` | 1.5× screen size |
| `SCALE` | `2.0` | Full retina resolution |
| `QUALITY` | `0.95` | Near-lossless JPEG |
| `QUALITY` | `0.78` | Balanced ← default |
| `QUALITY` | `0.70` | Smaller file |
| `MAX_PAGE_DIMENSION` | `2200` | Auto-downscale retina captures ← default |

#### Recommended Combinations

| Goal | Browser Zoom | SCALE | QUALITY |
|------|-------------|-------|---------|
| Smallest file | 75% | 1.0 | 0.70 |
| Balanced (default) | 100% | 1.0 | 0.78 |
| High quality | 150% | 1.0 | 0.90 |
| Maximum quality | 150% | 1.5 | 0.95 |

> 💡 `MAX_PAGE_DIMENSION` (default `2200`) automatically downsizes super-high-res captures to keep file sizes reasonable. Increase it for sharper output or set it to `Infinity` to disable.

### Limitations

| Situation | Result |
|-----------|--------|
| Owner disabled downloads (Docs/Sheets/Slides) | Export API blocked by Google |
| Video with DRM (Widevine) | captureStream() returns empty frames |
| YouTube video | Recorded via MediaRecorder — quality matches current stream |

### Dependencies

- View-Only PDF mode: dynamically loads [jsPDF](https://github.com/parallax/jsPDF) via unpkg (SRI protected)
- All other strategies: no external dependencies

### Credits

- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader) — auto-scroll & browser zoom tip
- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob image capture
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — display-size optimization

---

<a name="中文"></a>
## 🇹🇼 中文

> ⚠️ **合法使用提醒**：本腳本僅供用於您本人擁有或已獲授權存取的文件。請遵守著作權及 Google 服務條款。

> 🛡️ **安全提醒**：除非您了解其運作方式，否則請勿隨意將程式碼貼入瀏覽器控制台（Console）。本腳本為開源專案，完全在本地端執行，不會外洩您的資料，但在執行前建議您先審閱程式碼。

一段瀏覽器 Console 腳本，自動偵測 Google Drive 檔案類型，選擇最佳下載策略。

### 支援格式

| 類型 | 偵測方式 | 下載格式 |
|------|---------|---------|
| 📄 View-Only PDF（禁止下載） | `blob:https://drive.google.com/` img | `.pdf` |
| 📝 Google Docs | URL 含 `/document/` | `.docx` |
| 📊 Google Sheets | URL 含 `/spreadsheets/` | `.xlsx` |
| 📑 Google Slides | URL 含 `/presentation/` | `.pptx` |
| 📋 Google Forms | URL 含 `/forms/` | `.csv` |
| 🎨 Google Drawings | URL 含 `/drawings/` | `.svg` |
| 🖼️ 圖片 | DOM `img` 元素 | 原始格式 |
| 🎬 影片 | XHR/Fetch 攔截 + DOM | `.mp4` / 串流 URL |
| 🎵 音訊 | DOM `<audio>` 元素 | 原始格式 |
| 📁 其他檔案（PDF、Office、zip…） | `drive.google.com/file/d/` URL | 原始格式 |

### 使用方式

**View-Only PDF**

1. 用 **Chrome** 或 **Edge** 開啟 Google Drive PDF 連結
2. 點右上角三點選單 → **「在新視窗開啟」**
3. 按 `F12` 開啟 Console
4. 若 Chrome 提示無法貼上，先輸入 `allow pasting` 按 Enter
5. 貼上腳本按 **Enter**，腳本會自動捲動並下載

**影片 — Google Drive & YouTube（瀏覽器直接下載）**

1. 開啟 Google Drive 影片頁面或 **YouTube 觀看頁面**
2. 開啟 Console（`F12`）
3. 貼上並執行腳本
4. YouTube：腳本對 `<video>` 元素啟動 **MediaRecorder**，即時錄製串流
5. 影片自動播放 — **不要關閉或離開此頁面**
6. 影片結束時自動下載，或執行 `__gdrive_stopRecording()` 提前停止

> **YouTube 畫質說明**：錄製品質取決於 YouTube 當前串流畫質，通常為 360p–720p（依網路速度和 YouTube ABR 決定）。

**其他格式**

直接在 Google Drive 預覽頁面開啟 Console 執行，腳本自動偵測類型。

### 影片策略：運作原理

```
偵測到 YouTube 頁面
        │
        ▼
MediaRecorder.captureStream(<video>)
        │
        ▼
即時錄製串流
        │
        ▼
影片結束 / 執行 __gdrive_stopRecording()
        │
        ▼
Blob → 自動下載（.webm / .mp4）
```

### PDF 品質與檔案大小控制

#### 方式 A — 瀏覽器縮放（不需改程式碼）

| 瀏覽器縮放 | 效果 |
|-----------|------|
| 75% | 檔案較小，品質較低 |
| 100% | 預設平衡 |
| 150% | 品質較高，檔案較大 |
| 200% | 最高品質（大型 PDF 可能變慢） |

#### 方式 B — 腳本參數

| 參數 | 值 | 效果 |
|------|---|------|
| `SCALE` | `1.0` | 螢幕尺寸，最小檔案 ← 推薦 |
| `SCALE` | `1.5` | 1.5 倍螢幕尺寸 |
| `SCALE` | `2.0` | 完整 retina 解析度 |
| `QUALITY` | `0.95` | 接近無損 JPEG |
| `QUALITY` | `0.78` | 平衡 ← 預設 |
| `QUALITY` | `0.70` | 較小檔案 |
| `MAX_PAGE_DIMENSION` | `2200` | 自動壓下高 DPI 圖片 ← 預設 |

#### 推薦組合

| 目標 | 瀏覽器縮放 | SCALE | QUALITY |
|------|-----------|-------|---------|
| 最小檔案 | 75% | 1.0 | 0.70 |
| 平衡（預設） | 100% | 1.0 | 0.78 |
| 高品質 | 150% | 1.0 | 0.90 |
| 最高品質 | 150% | 1.5 | 0.95 |

> 💡 `MAX_PAGE_DIMENSION`（預設 `2200`）會自動壓縮超高解析度的截圖以避免檔案暴增。想要更銳利就提高數值，或設 `Infinity` 完全停用。

### 限制

| 情況 | 結果 |
|------|------|
| 擁有者關閉下載（Docs/Sheets/Slides） | Export API 被 Google 封鎖 |
| 影片有 DRM（Widevine）保護 | captureStream() 只錄到空白畫面 |
| YouTube 影片 | 透過 MediaRecorder 即時錄製，品質取決於當前串流 |

### 依賴

- View-Only PDF 模式：動態載入 [jsPDF](https://github.com/parallax/jsPDF)（via unpkg，受 SRI 保護）
- 其餘模式：無需任何外部依賴

### 致謝

- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader) — 自動捲動 & 瀏覽器縮放技巧
- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob 圖片擷取
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — 螢幕尺寸優化

---

## Script / 腳本

```javascript
// ================================================================
// GDrive Universal Downloader v2.11
// Supports: View-Only PDF, Docs, Sheets, Slides, Forms, Drawings,
//           Images, Video (MediaRecorder capture), Audio, and more
//
// YouTube video strategy:
//   captureStream() on <video> element → MediaRecorder → Blob → download
//   No external tools required, works entirely in the browser.
//
// Credits:
//   zeltox/Google-Drive-PDF-Downloader (auto-scroll, zoom tip)
//   zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader (blob capture)
//   mhsohan/How-to-download-protected-view-only-files-from-google-drive- (display size)
// ================================================================

(function () {
  console.log('🚀 GDrive Universal Downloader v2.11 starting...');

  // ── Settings ────────────────────────────────────────────────────
  const SCALE              = 1.0;   // PDF capture scale (1.0 = screen size, recommended)
  const QUALITY            = 0.78;  // PDF JPEG quality (0.0~1.0)
  const MAX_PAGE_DIMENSION = 2200;  // px cap after SCALE (Infinity to disable)
  const SCROLL_DELAY       = 200;   // ms between scroll steps
  const JSPDF_URL          = 'https://unpkg.com/jspdf@2.5.2/dist/jspdf.umd.min.js';
  const JSPDF_SRI          = 'sha384-en/ztfPSRkGfME4KIm05joYXynqzUgbsG5nMrj/xEFAHXkeZfO3yMK8QQ+mP7p1/';

  // ── Video URL detection (shared across interceptor + processVideo) ──
  const VIDEO_PATTERNS = [
    /googlevideo\.com/,
    /\.m3u8/,
    /\.mpd/,
    /videoplayback/,
    /mime=video/,
    /itag=\d+/,
  ];
  const isVideoURL = (url) => VIDEO_PATTERNS.some(p => p.test(url));

  // ── Video Stream Interceptor (Google Drive only) ────────────────
  const capturedVideoURLs = new Set();
  let _origXHR = null;
  let _origFetch = null;

  const recordVideoURL = (url) => {
    if (!url || typeof url !== 'string') return;
    if (isVideoURL(url) && !capturedVideoURLs.has(url)) {
      capturedVideoURLs.add(url);
      console.log('🎯 Captured video URL: ' + url.substring(0, 80) + '...');
    }
  };

  const installDriveVideoHooks = () => {
    if (_origXHR || _origFetch) return;

    const OrigXHR = window.XMLHttpRequest;
    _origXHR = OrigXHR;
    function HookedXHR() {
      const xhr = new OrigXHR();
      const origOpen = xhr.open.bind(xhr);
      xhr.open = function (method, url, ...args) {
        recordVideoURL(url);
        return origOpen(method, url, ...args);
      };
      return xhr;
    }
    HookedXHR.prototype = OrigXHR.prototype;
    Object.defineProperty(HookedXHR, Symbol.hasInstance, {
      value: (instance) => instance instanceof OrigXHR,
    });
    window.XMLHttpRequest = HookedXHR;

    _origFetch = window.fetch;
    window.fetch = function (input, ...args) {
      const url = typeof input === 'string' ? input : input?.url;
      recordVideoURL(url);
      return _origFetch.apply(this, [input, ...args]);
    };

    console.log('🪝 XHR/fetch hooks installed for Drive video detection');
  };

  const restoreHooks = () => {
    if (_origXHR)   { window.XMLHttpRequest = _origXHR; _origXHR = null; }
    if (_origFetch)  { window.fetch = _origFetch; _origFetch = null; }
  };

  // ── Utilities ───────────────────────────────────────────────────

  const sanitizeFilename = (name) => {
    return name
      .replace(/[<>:"/\\|?*\x00-\x1f]/g, '_')  // illegal filesystem chars
      .replace(/\.\.+/g, '.')                     // path traversal
      .replace(/^\.+|\.+$/g, '')                  // leading/trailing dots
      .substring(0, 200)                          // limit length
      .trim() || 'gdrive-file';
  };

  const getTitle = () => {
    const meta = document.querySelector('meta[itemprop="name"]')?.content;
    const raw  = meta || document.title;
    const name = raw
      .replace(/\s*[-–—]\s*Google.*/i, '')
      .replace(/\.\w{2,5}$/, '')
      .trim() || 'gdrive-file';
    return sanitizeFilename(name);
  };

  const getTitleExt = () => {
    const meta  = document.querySelector('meta[itemprop="name"]')?.content;
    const raw   = meta || document.title;
    const clean = raw.replace(/\s*[-–—]\s*Google.*/i, '').trim();
    return clean.match(/\.(\w{2,5})$/)?.[1]?.toLowerCase() || null;
  };

  const triggerDownload = (url, filename) => {
    const a = document.createElement('a');
    a.href = url; a.download = sanitizeFilename(filename);
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
  };

  const sleep = ms => new Promise(r => setTimeout(r, ms));

  const ALLOWED_SCRIPT_URLS = [JSPDF_URL];

  const loadScript = (src, sri) => new Promise((resolve, reject) => {
    if (!ALLOWED_SCRIPT_URLS.includes(src)) {
      reject(new Error('Blocked loading untrusted script: ' + src));
      return;
    }
    const existing = [...document.querySelectorAll('script')].find(s => s.src === src);
    if (existing) { resolve(); return; }
    let trustedSrc = src;
    if (window.trustedTypes) {
      try {
        const policy = trustedTypes.createPolicy('gdrivePolicy', {
          createScriptURL: (input) => {
            if (ALLOWED_SCRIPT_URLS.includes(input)) return input;
            throw new TypeError('Blocked untrusted script URL: ' + input);
          },
        });
        trustedSrc = policy.createScriptURL(src);
      } catch (e) {
        try { trustedSrc = trustedTypes.getPolicy('gdrivePolicy').createScriptURL(src); }
        catch (_) {}
      }
    }
    const s = document.createElement('script');
    s.src = trustedSrc;
    if (sri) {
      s.integrity = sri;
      s.crossOrigin = 'anonymous';
    }
    s.onload = resolve;
    s.onerror = () => reject(new Error('Failed to load: ' + src));
    document.body.appendChild(s);
  });

  const scalePageDimensions = (width, height) => {
    const baseW = width * SCALE;
    const baseH = height * SCALE;
    const maxDim = Math.max(baseW, baseH);
    if (!Number.isFinite(MAX_PAGE_DIMENSION) || MAX_PAGE_DIMENSION <= 0 || maxDim <= MAX_PAGE_DIMENSION) {
      return {
        w: Math.round(baseW),
        h: Math.round(baseH),
        downsampled: false,
      };
    }
    const factor = MAX_PAGE_DIMENSION / maxDim;
    return {
      w: Math.round(baseW * factor),
      h: Math.round(baseH * factor),
      downsampled: true,
    };
  };

  const getVideoTitleFromURL = (url) => {
    const match = url?.match(/[?&]title=([^&]+)/i);
    if (!match) return null;
    try {
      const decoded = decodeURIComponent(match[1].replace(/\+/g, ' '));
      const clean   = decoded.replace(/\.\w{2,5}$/, '').trim();
      return sanitizeFilename(clean) || null;
    } catch (_) {
      return null;
    }
  };

  const getVideoExtFromURL = (url) => {
    if (!url) return null;
    const extMatch = url.match(/\.(mp4|m4v|mov|webm|mkv|avi|3gp)/i);
    if (extMatch) return extMatch[1].toLowerCase();
    const mimeMatch = url.match(/mime=video\/([^&]+)/i);
    if (mimeMatch) {
      const mime = decodeURIComponent(mimeMatch[1]).toLowerCase();
      if (mime.includes('webm')) return 'webm';
      if (mime.includes('3gpp')) return '3gp';
      if (mime.includes('mov'))  return 'mov';
      if (mime.includes('m4v'))  return 'm4v';
      if (mime.includes('avi'))  return 'avi';
      return 'mp4';
    }
    return null;
  };

  const extractBalancedJSON = (text, startIdx) => {
    let depth = 0, inString = false, escape = false;
    for (let i = startIdx; i < text.length; i++) {
      const c = text[i];
      if (escape)               { escape = false; continue; }
      if (c === '\\' && inString) { escape = true;  continue; }
      if (c === '"')            { inString = !inString; continue; }
      if (inString)             { continue; }
      if (c === '{')            { depth++; }
      else if (c === '}')       { depth--; if (depth === 0) return text.substring(startIdx, i + 1); }
    }
    return null;
  };

  const safeParseJSON = (str) => {
    if (!str) return null;
    try { return JSON.parse(str); } catch (_) { return null; }
  };

  const getWatchFlexyPlayerResponse = () => {
    const flexy = document.querySelector('ytd-watch-flexy');
    if (!flexy) return null;
    const direct = flexy.playerResponse || flexy.__data?.playerResponse || flexy?.data?.playerResponse;
    if (direct) return typeof direct === 'string' ? safeParseJSON(direct) : direct;
    const attr = flexy.getAttribute?.('player-response');
    if (attr) return safeParseJSON(attr);
    return null;
  };

  const findPlayerResponseInScripts = () => {
    const scripts = [...document.querySelectorAll('script')];
    for (const s of scripts) {
      if (!s.textContent.includes('ytInitialPlayerResponse')) continue;
      const start  = s.textContent.indexOf('ytInitialPlayerResponse');
      const eqIdx  = s.textContent.indexOf('=', start);
      if (eqIdx === -1) continue;
      let jsonStart = eqIdx + 1;
      while (jsonStart < s.textContent.length && /\s/.test(s.textContent[jsonStart])) jsonStart++;
      if (s.textContent[jsonStart] !== '{') continue;
      const json = extractBalancedJSON(s.textContent, jsonStart);
      if (!json) continue;
      const parsed = safeParseJSON(json);
      if (parsed) return parsed;
    }
    return null;
  };

  const tryGetYTPlayerResponse = () => {
    return window.ytInitialPlayerResponse
      || window.ytplayer?.config?.args?.raw_player_response
      || window.__ytplayer_config__?.args?.raw_player_response
      || window.ytInitialData?.playerResponse
      || window.__YTPLAYER?.playerResponse
      || getWatchFlexyPlayerResponse()
      || findPlayerResponseInScripts();
  };

  const waitForYTPlayerResponse = async () => {
    let response = tryGetYTPlayerResponse();
    if (response?.streamingData) return response;
    for (let i = 0; i < 15; i++) {
      await sleep(200);
      response = tryGetYTPlayerResponse();
      if (response?.streamingData) return response;
    }
    return response || null;
  };

  // Auto-scroll to trigger lazy loading
  const autoScroll = async () => {
    console.log('⏬ Auto-scrolling...');
    const scrollable =
      document.querySelector('.ndfHFb-c4YZDc-cYSp0e-DARUcf') ||
      document.querySelector('[role="main"]') ||
      document.documentElement;
    const total = scrollable.scrollHeight;
    const step  = window.innerHeight;
    for (let pos = 0; pos <= total; pos += step) {
      scrollable.scrollTo(0, pos);
      await sleep(SCROLL_DELAY);
    }
    scrollable.scrollTo(0, 0);
    await sleep(300);
    console.log('✅ Scroll complete');
  };

  // ── File Type Detection ─────────────────────────────────────────

  const url = window.location.href;

  const detect = () => {
    const blobImgs = [...document.getElementsByTagName('img')]
      .filter(img => img.src.startsWith('blob:https://drive.google.com/'));
    if (blobImgs.length > 0) return 'blob-pdf';

    if (/docs\.google\.com\/document/i.test(url))     return 'gdoc';
    if (/docs\.google\.com\/spreadsheets/i.test(url)) return 'gsheet';
    if (/docs\.google\.com\/presentation/i.test(url)) return 'gslides';
    if (/docs\.google\.com\/forms/i.test(url))        return 'gforms';
    if (/docs\.google\.com\/drawings/i.test(url))     return 'gdrawings';

    if (/youtube\.com\/watch|youtu\.be\//i.test(url)) return 'video';
    if (document.querySelector('video'))  return 'video';
    if (document.querySelector('audio'))  return 'audio';
    if (document.querySelector(
      'img.stretch-fit, #drive-viewer-main-content img, .drive-viewer-content img'
    ))                                    return 'image';
    if (document.querySelector(
      '.drive-viewer-text-container, pre, iframe.docs-texteventtarget-iframe, .docs-texteventtarget-iframe'
    ))                                    return 'text';
    if (/drive\.google\.com\/file\/d\//i.test(url)) return 'file-export';

    return 'unknown';
  };

  // ── Strategy: Video ─────────────────────────────────────────────
  const processVideo = async () => {
    const title = getTitle();

    // ── YouTube: extract streams from ytInitialPlayerResponse ──────
    // YouTube embeds all stream URLs directly in the page JS object.
    // `formats` = muxed video+audio (up to 720p), downloadable as single file.
    // `adaptiveFormats` = separate video/audio tracks (1080p+), need merging.
    if (/youtube\.com\/watch|youtu\.be\//i.test(url)) {
      console.log('📺 YouTube detected — scanning stream URLs...');

      const playerResponse = await waitForYTPlayerResponse();
      const formats         = playerResponse?.streamingData?.formats         || [];
      const adaptiveFormats = playerResponse?.streamingData?.adaptiveFormats || [];

      if (!playerResponse?.streamingData) {
        console.warn('⚠️ Could not read ytInitialPlayerResponse. Continuing with MediaRecorder fallback only.');
      }

      // Muxed streams (video + audio combined) — for reference only
      const muxed = formats
        .filter(f => f.url && f.mimeType?.startsWith('video'))
        .sort((a, b) => (b.height || 0) - (a.height || 0));

      if (muxed.length > 0) {
        console.log('📊 Available muxed streams (for reference): '
          + muxed.map(f => f.height + 'p').join(', '));
      }

      // YouTube videoplayback URLs are signed to cookies/IP — fetch() always 403.
      // Instead, capture the stream directly from the <video> element via MediaRecorder.
      const videoEl = document.querySelector('video');
      if (!videoEl) {
        console.error('❌ No <video> element found. Make sure the video is playing.');
        return;
      }

      let stream;
      try {
        stream = videoEl.captureStream?.() || videoEl.mozCaptureStream?.();
      } catch(e) {
        console.error('❌ captureStream() failed:', e.message);
        return;
      }
      if (!stream) {
        console.error('❌ captureStream() not supported in this browser. Try Chrome.');
        return;
      }

      // Chrome MediaRecorder only supports webm — mp4 silently fails
      const mimeType = ['video/webm;codecs=vp9,opus', 'video/webm;codecs=vp8,opus', 'video/webm']
        .find(t => MediaRecorder.isTypeSupported(t)) || 'video/webm';

      const recorder = new MediaRecorder(stream, { mimeType });
      const chunks   = [];

      recorder.ondataavailable = e => {
        if (e.data && e.data.size > 0) chunks.push(e.data);
      };

      recorder.onstop = () => {
        // Wait one tick to ensure the final ondataavailable has fired
        setTimeout(() => {
          if (chunks.length === 0) {
            console.error('❌ No data recorded. The video may be DRM-protected or captureStream() returned empty frames.');
            return;
          }
          const blob    = new Blob(chunks, { type: 'video/webm' });
          const mb      = (blob.size / 1024 / 1024).toFixed(1);
          const blobUrl = URL.createObjectURL(blob);
          const fname   = sanitizeFilename(title + '.webm');

          const triggerBlobDownload = () => {
            const link = document.createElement('a');
            link.href = blobUrl;
            link.download = fname;
            link.style.display = 'none';
            link.target = '_blank';
            link.rel = 'noopener';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
          };

          triggerBlobDownload();

          const manualDownload = () => {
            if (!window.__gdrive_lastRecording || window.__gdrive_lastRecording.url !== blobUrl) {
              console.warn('⚠️ No recording available to download.');
              return;
            }
            triggerBlobDownload();
          };

          window.__gdrive_lastRecording = { blob, url: blobUrl, filename: fname };
          Object.defineProperty(window, '__gdrive_downloadRecording', {
            value: manualDownload,
            configurable: true,
            writable: false,
          });

          // Method 2: fallback — open blob in helper tab if the browser blocked auto-download
          const openBlobInTab = () => {
            window.open(blobUrl, '_blank', 'noopener,noreferrer');
            console.log('💾 A helper tab was opened with the recording. Use the browser menu to "Save video".');
          };
          const fallbackTimer = setTimeout(openBlobInTab, 4000);

          const cancelFallback = () => clearTimeout(fallbackTimer);
          window.addEventListener('beforeunload', cancelFallback, { once: true });

          const revokeTimer = setTimeout(() => {
            if (window.__gdrive_lastRecording?.url === blobUrl) {
              delete window.__gdrive_downloadRecording;
              delete window.__gdrive_lastRecording;
            }
            URL.revokeObjectURL(blobUrl);
          }, 600000);

          console.log('✅ Recording complete: ' + fname + ' (' + mb + ' MB)');
          console.log('   Download should start automatically. If not, run  __gdrive_downloadRecording()  or wait for the helper tab.');
          console.log('   Keep this tab open until the browser finishes saving the file.');
        }, 300);
      };

      // Make sure video is playing so MediaRecorder gets data
      videoEl.play().catch(() => {});
      recorder.start(1000); // flush chunk every 1s

      const duration  = videoEl.duration || 0;
      const remaining = Math.max(0, (duration - videoEl.currentTime));

      console.log('🔴 Recording started (' + mimeType + ')');
      if (duration > 0) {
        console.log('   Video length: ' + Math.round(duration) + 's');
        console.log('   Recording from current position — ' + Math.round(remaining) + 's remaining');
        console.log('   ⚠️  Do not close or navigate away from this tab!');
        console.log('   Run  __gdrive_stopRecording()  at any time to stop early and download.');
      } else {
        console.log('   Duration unknown — run  __gdrive_stopRecording()  when done watching.');
      }

      const doStop = () => {
        if (recorder.state === 'inactive') return;
        recorder.requestData(); // flush any buffered data before stopping
        setTimeout(() => {
          recorder.stop();
          delete window.__gdrive_stopRecording;
          console.log('⏹ Recording stopped — preparing download...');
        }, 200);
      };

      // Expose stop function with non-writable descriptor to prevent tampering
      Object.defineProperty(window, '__gdrive_stopRecording', {
        value: doStop, writable: false, configurable: true,
      });

      // Auto-stop when video ends
      videoEl.addEventListener('ended', doStop, { once: true });

      return;
    }
    capturedVideoURLs.clear();
    installDriveVideoHooks();
    const videoEl = document.querySelector('video');
    const domSrc  = videoEl?.src
      || videoEl?.querySelector('source[src]')?.src
      || [...(videoEl?.querySelectorAll('source') || [])].find(s => s.src)?.src;

    if (domSrc && !domSrc.startsWith('blob:')) {
      console.log('✅ Found direct video URL in DOM');
      const ext      = getVideoExtFromURL(domSrc) || 'mp4';
      const baseName = getVideoTitleFromURL(domSrc) || title;
      const fname    = baseName + '.' + ext;
      triggerDownload(domSrc, fname);
      console.log('🎬 Downloading → ' + fname);
      restoreHooks();
      return;
    }

    // Step 2: Check Performance API for already-loaded resources
    const perfEntries = window.performance
      .getEntriesByType('resource')
      .map(e => e.name)
      .filter(isVideoURL);

    perfEntries.forEach(u => capturedVideoURLs.add(u));

    // Step 3: Wait for XHR/fetch hooks to capture URLs (trigger video play)
    if (capturedVideoURLs.size === 0) {
      console.log('⏳ No video URLs captured yet. Triggering playback...');
      if (videoEl) {
        videoEl.play().catch(() => {});
      }
      // Wait up to 8 seconds — YouTube may take longer to start streaming
      for (let i = 0; i < 16; i++) {
        await sleep(500);
        if (capturedVideoURLs.size > 0) break;
        if (i % 2 === 1) console.log('  ⌛ Waiting... (' + ((i + 1) * 0.5).toFixed(1) + 's)');
      }
    }

    if (capturedVideoURLs.size === 0) {
      console.warn('⚠️ No video stream URLs captured.');
      console.log(
        'Try:\n' +
        '1. Run the script BEFORE the video starts loading (refresh page first)\n' +
        '2. Right-click the video → "Save video as"\n' +
        '3. Check Network tab in DevTools for video requests manually'
      );
      restoreHooks();
      return;
    }

    // Step 4: Categorise captured URLs
    const urls       = [...capturedVideoURLs];
    const directMp4  = urls.filter(u => /\.(mp4|webm)/i.test(u) || /mime=video/i.test(u));
    const hlsUrls    = urls.filter(u => /\.m3u8/i.test(u));
    const dashUrls   = urls.filter(u => /\.mpd/i.test(u));
    const streamUrls = urls.filter(u => /videoplayback|googlevideo/i.test(u));

    console.log('📊 Captured ' + urls.length + ' video URL(s):');
    console.log('   Direct MP4/WebM : ' + directMp4.length);
    console.log('   HLS (.m3u8)     : ' + hlsUrls.length);
    console.log('   DASH (.mpd)     : ' + dashUrls.length);
    console.log('   Stream URLs     : ' + streamUrls.length);

    // Step 5: Only attempt direct download for true standalone MP4/WebM files
    // NOTE: videoplayback / googlevideo URLs are segmented — one segment = broken file
    if (directMp4.length > 0) {
      const best = directMp4.sort((a, b) => b.length - a.length)[0];
      const ext  = getVideoExtFromURL(best) || 'mp4';
      const baseName = getVideoTitleFromURL(best) || title;
      const fname = baseName + '.' + ext;
      console.log('✅ Direct video stream found — downloading...');
      triggerDownload(best, fname);
      console.log('🎬 Downloading → ' + fname);
      restoreHooks();
      return;
    }

    // Step 6: Segmented stream detected — cannot download directly in browser
    const streamTarget =
      hlsUrls[0] ||
      dashUrls[0] ||
      streamUrls.sort((a, b) => b.length - a.length)[0];

    if (streamTarget) {
      const streamType =
        hlsUrls.length  > 0 ? 'HLS (.m3u8)' :
        dashUrls.length > 0 ? 'DASH (.mpd)' :
                               'Stream (videoplayback)';
      console.warn('⚠️ ' + streamType + ' detected — segmented stream, cannot download directly in browser.');
      console.log('💡 Use a browser extension like "Video DownloadHelper" to capture this stream.');
      restoreHooks();
      return;
    }

    // Fallback: show all captured URLs
    console.log('📋 All captured URLs:');
    urls.forEach((u, i) => console.log('  [' + i + '] ' + u));

    restoreHooks();
  };

  // ── Strategy: View-Only PDF ─────────────────────────────────────
  const processBlobPDF = async () => {
    await autoScroll();
    const blobImgs = [...document.getElementsByTagName('img')]
      .filter(img => img.src.startsWith('blob:https://drive.google.com/'));

    if (blobImgs.length === 0) {
      console.error('❌ No page images found. Scroll to the bottom manually and try again.');
      return;
    }

    await loadScript(JSPDF_URL, JSPDF_SRI);
    const { jsPDF } = window.jspdf;
    const limitInfo =
      Number.isFinite(MAX_PAGE_DIMENSION) && MAX_PAGE_DIMENSION > 0
        ? ' MAX:' + MAX_PAGE_DIMENSION + 'px'
        : '';
    console.log('📄 Found ' + blobImgs.length + ' pages — SCALE:' + SCALE + ' QUALITY:' + QUALITY + limitInfo);

    let pdf = null;
    let downsampledPages = 0;
    for (let i = 0; i < blobImgs.length; i++) {
      const img = blobImgs[i];
      const sourceWidth  = img.naturalWidth  || img.width;
      const sourceHeight = img.naturalHeight || img.height;
      const { w, h, downsampled } = scalePageDimensions(sourceWidth, sourceHeight);
      if (downsampled) downsampledPages++;
      const canvas = document.createElement('canvas');
      canvas.width = w; canvas.height = h;
      canvas.getContext('2d').drawImage(img, 0, 0, w, h);
      const imgData     = canvas.toDataURL('image/jpeg', QUALITY);
      const orientation = w > h ? 'l' : 'p';
      if (i === 0) {
        pdf = new jsPDF({ orientation, unit: 'px', format: [w, h] });
      } else {
        pdf.addPage([w, h], orientation);
      }
      pdf.addImage(imgData, 'JPEG', 0, 0, w, h, '', 'FAST');
      console.log('  🖼️ Page ' + (i + 1) + '/' + blobImgs.length
        + ' (' + Math.floor((i + 1) / blobImgs.length * 100) + '%)');
    }
    if (downsampledPages > 0) {
      console.log('ℹ️ Auto-downscaled ' + downsampledPages + ' page(s) to max ' + MAX_PAGE_DIMENSION + 'px');
    }

    const filename = getTitle() + '.pdf';
    await pdf.save(filename, { returnPromise: true });
    console.log('🎉 Done! Downloaded: ' + filename);
  };

  // ── Main ────────────────────────────────────────────────────────

  const type  = detect();
  const title = getTitle();
  const ext   = getTitleExt();
  const getId = () => url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
  const getFormExportPath = () => {
    const newForm = url.match(/\/forms\/d\/e\/([^/]+)/);
    if (newForm) return 'd/e/' + newForm[1];
    const legacyForm = url.match(/\/forms\/d\/([^/]+)/);
    if (legacyForm) return 'd/' + legacyForm[1];
    return null;
  };

  console.log('🔍 Detected: ' + type + ' | File: ' + title);

  if (type === 'blob-pdf')  { processBlobPDF(); return; }
  if (type === 'video')     { processVideo();   return; }

  if (type === 'gdoc') {
    const id = getId(); if (!id) { console.error('Cannot get document ID'); return; }
    triggerDownload('https://docs.google.com/document/d/' + id + '/export?format=docx', title + '.docx');
    console.log('📝 Downloading → ' + title + '.docx'); return;
  }

  if (type === 'gsheet') {
    const id = getId(); if (!id) { console.error('Cannot get spreadsheet ID'); return; }
    triggerDownload('https://docs.google.com/spreadsheets/d/' + id + '/export?format=xlsx', title + '.xlsx');
    console.log('📊 Downloading → ' + title + '.xlsx'); return;
  }

  if (type === 'gslides') {
    const id = getId(); if (!id) { console.error('Cannot get presentation ID'); return; }
    triggerDownload('https://docs.google.com/presentation/d/' + id + '/export/pptx', title + '.pptx');
    console.log('📑 Downloading → ' + title + '.pptx'); return;
  }

  if (type === 'gforms') {
    const formPath = getFormExportPath(); if (!formPath) { console.error('Cannot get form ID'); return; }
    triggerDownload('https://docs.google.com/forms/' + formPath + '/export?format=csv', title + '.csv');
    console.log('📋 Downloading → ' + title + '.csv'); return;
  }

  if (type === 'gdrawings') {
    const id = getId(); if (!id) { console.error('Cannot get drawing ID'); return; }
    triggerDownload('https://docs.google.com/drawings/d/' + id + '/export/svg', title + '.svg');
    console.log('🎨 Downloading → ' + title + '.svg'); return;
  }

  if (type === 'audio') {
    const audio = document.querySelector('audio');
    const src   = audio?.src || audio?.querySelector('source[src]')?.src;
    if (!src) { console.warn('⚠️ Cannot get audio URL.'); return; }
    const aExt = src.match(/\.(mp3|wav|ogg|flac|aac|m4a)/i)?.[1] || ext || 'mp3';
    triggerDownload(src, title + '.' + aExt);
    console.log('🎵 Downloading → ' + title + '.' + aExt); return;
  }

  if (type === 'image') {
    const img = document.querySelector(
      'img.stretch-fit, #drive-viewer-main-content img, .drive-viewer-content img'
    );
    if (!img?.src) { console.error('Cannot find image source'); return; }
    const iExt = img.src.match(/\.(png|jpg|jpeg|gif|webp|svg|bmp)/i)?.[1] || ext || 'jpg';
    triggerDownload(img.src, title + '.' + iExt);
    console.log('🖼️ Downloading → ' + title + '.' + iExt); return;
  }

  if (type === 'text') {
    const el       = document.querySelector('.drive-viewer-text-container, pre');
    let content    = el?.innerText?.trim();
    if (!content) {
      const iframe = document.querySelector('iframe.docs-texteventtarget-iframe')
        || document.querySelector('.docs-texteventtarget-iframe iframe');
      if (iframe) {
        try {
          content = iframe.contentDocument?.body?.innerText?.trim();
        } catch (err) {
          console.warn('⚠️ Cannot read iframe contents:', err.message);
        }
      }
    }
    if (!content) {
      content = document.body.innerText || '';
    }
    const blob = new Blob([content], { type: 'text/plain;charset=utf-8' });
    triggerDownload(URL.createObjectURL(blob), title + '.' + (ext || 'txt'));
    console.log('📋 Downloading → ' + title + '.' + (ext || 'txt')); return;
  }

  if (type === 'file-export') {
    const id   = getId(); if (!id) { console.error('Cannot get file ID'); return; }
    const fExt = ext || 'pdf';
    triggerDownload('https://drive.google.com/uc?export=download&id=' + id, title + '.' + fExt);
    console.log('📁 Downloading → ' + title + '.' + fExt);
    console.log('⚠️ If "no permission" appears, the owner has disabled downloads.'); return;
  }

  console.warn('⚠️ Could not auto-detect file type.');
  console.log('Please check:\n1. You are on a Google Drive preview page\n2. Try refreshing and running again');

})();
```
