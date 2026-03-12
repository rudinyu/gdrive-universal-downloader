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
4. For YouTube: script reads `ytInitialPlayerResponse` to get all available stream URLs
5. **Best muxed quality auto-downloads immediately** (up to 720p, video+audio in one file)
6. To pick a specific quality, run `__gdriveDownloadStream(N)` in Console after the list appears

> **YouTube quality note**: Muxed streams (direct download) go up to **720p**. For 1080p+ you need yt-dlp since those streams have separate video/audio tracks that require merging.

**All other formats**

Open the file preview in Google Drive, open Console, paste and run. It auto-detects the type.

### Video Strategy: How It Works

```
Browser XHR/fetch  ──intercept──▶  capture googlevideo.com URLs
                                          │
                              ┌───────────┴───────────┐
                              ▼                       ▼
                    Direct MP4 found          HLS/DASH manifest
                    → auto download           → show URL + yt-dlp command
```

If the video uses HLS (`.m3u8`), the script will print a ready-to-use `yt-dlp` command:
```bash
yt-dlp "https://..." -o "filename.mp4"
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
| `QUALITY` | `0.82` | Balanced ← default |
| `QUALITY` | `0.70` | Smaller file |

#### Recommended Combinations

| Goal | Browser Zoom | SCALE | QUALITY |
|------|-------------|-------|---------|
| Smallest file | 75% | 1.0 | 0.70 |
| Balanced (default) | 100% | 1.0 | 0.82 |
| High quality | 150% | 1.0 | 0.90 |
| Maximum quality | 150% | 1.5 | 0.95 |

### Limitations

| Situation | Result |
|-----------|--------|
| Owner disabled downloads (Docs/Sheets/Slides) | Export API blocked by Google |
| Video with DRM (Widevine) | Cannot decrypt; URL shown but unplayable |
| HLS / DASH / videoplayback stream | Segmented — direct download impossible; yt-dlp command provided |

### Dependencies

- View-Only PDF mode: dynamically loads [jsPDF](https://github.com/parallax/jsPDF) via unpkg
- All other strategies: no external dependencies

### Credits

- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader) — auto-scroll & browser zoom tip
- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob image capture
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — display-size optimization
- [yt-dlp](https://github.com/yt-dlp/yt-dlp) — inspired the XHR/fetch intercept approach

---

<a name="中文"></a>
## 🇹🇼 中文

> ⚠️ **合法使用提醒**：本腳本僅供用於您本人擁有或已獲授權存取的文件。請遵守著作權及 Google 服務條款。

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
4. YouTube：腳本從 `ytInitialPlayerResponse` 讀取所有可用串流 URL
5. **最佳畫質自動開始下載**（最高 720p，影音合一檔案）
6. 若要選擇其他畫質，在 Console 執行 `__gdriveDownloadStream(N)` 即可

> **YouTube 畫質說明**：可直接下載的合併串流最高到 **720p**。1080p 以上需要 yt-dlp，因為那些串流的影像和音訊是分開的，需要額外合併。

**其他格式**

直接在 Google Drive 預覽頁面開啟 Console 執行，腳本自動偵測類型。

### 影片策略：運作原理

```
瀏覽器 XHR/fetch  ──攔截──▶  捕捉 googlevideo.com URL
                                      │
                          ┌───────────┴───────────┐
                          ▼                       ▼
                  找到直接 MP4             找到 HLS/DASH 串流
                  → 自動下載              → 顯示 URL + yt-dlp 指令
```

若影片使用 HLS（`.m3u8`），腳本會印出可直接使用的 `yt-dlp` 指令：
```bash
yt-dlp "https://..." -o "filename.mp4"
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
| `QUALITY` | `0.82` | 平衡 ← 預設 |
| `QUALITY` | `0.70` | 較小檔案 |

#### 推薦組合

| 目標 | 瀏覽器縮放 | SCALE | QUALITY |
|------|-----------|-------|---------|
| 最小檔案 | 75% | 1.0 | 0.70 |
| 平衡（預設） | 100% | 1.0 | 0.82 |
| 高品質 | 150% | 1.0 | 0.90 |
| 最高品質 | 150% | 1.5 | 0.95 |

### 限制

| 情況 | 結果 |
|------|------|
| 擁有者關閉下載（Docs/Sheets/Slides） | Export API 被 Google 封鎖 |
| 影片有 DRM（Widevine）保護 | 無法解密，URL 顯示但無法播放 |
| HLS / DASH / videoplayback 串流 | 分段串流，無法直接下載；提供 yt-dlp 指令 |

### 依賴

- View-Only PDF 模式：動態載入 [jsPDF](https://github.com/parallax/jsPDF)（via unpkg）
- 其餘模式：無需任何外部依賴

### 致謝

- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader) — 自動捲動 & 瀏覽器縮放技巧
- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob 圖片擷取
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — 螢幕尺寸優化
- [yt-dlp](https://github.com/yt-dlp/yt-dlp) — 啟發了 XHR/fetch 攔截的設計思路

---

## Script / 腳本

```javascript
// ================================================================
// GDrive Universal Downloader v2.3
// Supports: View-Only PDF, Docs, Sheets, Slides, Forms, Drawings,
//           Images, Video (yt-dlp style intercept), Audio, and more
//
// Video strategy inspired by yt-dlp:
//   Hook XHR + fetch to intercept real stream URLs before they load
//
// Credits:
//   zeltox/Google-Drive-PDF-Downloader (auto-scroll, zoom tip)
//   zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader (blob capture)
//   mhsohan/How-to-download-protected-view-only-files-from-google-drive- (display size)
//   yt-dlp (XHR/fetch intercept concept)
// ================================================================

(function () {
  console.log('🚀 GDrive Universal Downloader v2.3 starting...');

  // ── Settings ────────────────────────────────────────────────────
  const SCALE        = 1.0;   // PDF capture scale (1.0 = screen size, recommended)
  const QUALITY      = 0.82;  // PDF JPEG quality (0.0~1.0)
  const SCROLL_DELAY = 200;   // ms between scroll steps

  // ── Video Stream Interceptor (Google Drive only) ────────────────
  // Skip hooks on YouTube — we use ytInitialPlayerResponse instead
  const capturedVideoURLs = new Set();

  if (!/youtube\.com|youtu\.be/i.test(window.location.href)) {
    const VIDEO_PATTERNS = [
      /googlevideo\.com/,
      /\.m3u8/,
      /\.mpd/,
      /videoplayback/,
      /mime=video/,
      /itag=\d+/,
    ];

    const isVideoURL = (url) => VIDEO_PATTERNS.some(p => p.test(url));

    const recordURL = (url) => {
      if (!url || typeof url !== 'string') return;
      if (isVideoURL(url) && !capturedVideoURLs.has(url)) {
        capturedVideoURLs.add(url);
        console.log('🎯 Captured video URL: ' + url.substring(0, 80) + '...');
      }
    };

    const OrigXHR = window.XMLHttpRequest;
    function HookedXHR() {
      const xhr = new OrigXHR();
      const origOpen = xhr.open.bind(xhr);
      xhr.open = function (method, url, ...args) {
        recordURL(url);
        return origOpen(method, url, ...args);
      };
      return xhr;
    }
    HookedXHR.prototype = OrigXHR.prototype;
    window.XMLHttpRequest = HookedXHR;

    const origFetch = window.fetch;
    window.fetch = function (input, ...args) {
      const url = typeof input === 'string' ? input : input?.url;
      recordURL(url);
      return origFetch.apply(this, [input, ...args]);
    };

    console.log('🪝 XHR/fetch hooks installed for Drive video detection');
  } else {
    console.log('📺 YouTube page — skipping XHR hooks, using ytInitialPlayerResponse instead');
  }

  // ── Utilities ───────────────────────────────────────────────────

  const getTitle = () => {
    const meta = document.querySelector('meta[itemprop="name"]')?.content;
    const raw  = meta || document.title;
    return raw
      .replace(/\s*[-–—]\s*Google.*/i, '')
      .replace(/\.\w{2,5}$/, '')
      .trim() || 'gdrive-file';
  };

  const getTitleExt = () => {
    const meta  = document.querySelector('meta[itemprop="name"]')?.content;
    const raw   = meta || document.title;
    const clean = raw.replace(/\s*[-–—]\s*Google.*/i, '').trim();
    return clean.match(/\.(\w{2,5})$/)?.[1]?.toLowerCase() || null;
  };

  const triggerDownload = (url, filename) => {
    const a = document.createElement('a');
    a.href = url; a.download = filename;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
  };

  const sleep = ms => new Promise(r => setTimeout(r, ms));

  const loadScript = (src) => new Promise((resolve, reject) => {
    if (document.querySelector(`script[src="${src}"]`)) { resolve(); return; }
    let trustedSrc = src;
    if (window.trustedTypes) {
      try {
        const policy = trustedTypes.createPolicy('gdrivePolicy', {
          createScriptURL: (input) => input,
        });
        trustedSrc = policy.createScriptURL(src);
      } catch (e) {
        try { trustedSrc = trustedTypes.getPolicy('gdrivePolicy').createScriptURL(src); }
        catch (_) {}
      }
    }
    const s = document.createElement('script');
    s.src = trustedSrc;
    s.onload = resolve;
    s.onerror = () => reject(new Error('Failed to load: ' + src));
    document.body.appendChild(s);
  });

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
      '.drive-viewer-text-container, .docs-texteventtarget-iframe, pre'
    ))                                    return 'text';
    if (/drive\.google\.com\/file\/d\//i.test(url)) return 'file-export';

    return 'unknown';
  };

  // ── Strategy: Video (yt-dlp style) ─────────────────────────────
  const processVideo = async () => {
    const title = getTitle();

    // ── YouTube: extract streams from ytInitialPlayerResponse ──────
    // YouTube embeds all stream URLs directly in the page JS object.
    // `formats` = muxed video+audio (up to 720p), downloadable as single file.
    // `adaptiveFormats` = separate video/audio tracks (1080p+), need merging.
    if (/youtube\.com\/watch|youtu\.be\//i.test(url)) {
      console.log('📺 YouTube detected — scanning stream URLs...');

      const playerResponse = window.ytInitialPlayerResponse
        || window.ytplayer?.config?.args?.raw_player_response;

      if (!playerResponse?.streamingData) {
        console.error('❌ Cannot find ytInitialPlayerResponse. Try refreshing the page and running the script again.');
        return;
      }

      const formats         = playerResponse.streamingData.formats         || [];
      const adaptiveFormats = playerResponse.streamingData.adaptiveFormats || [];

      // Muxed streams (video + audio combined) — directly downloadable
      const muxed = formats
        .filter(f => f.url && f.mimeType?.startsWith('video'))
        .sort((a, b) => (b.height || 0) - (a.height || 0));

      // Adaptive video-only streams (higher quality but audio separate)
      const videoOnly = adaptiveFormats
        .filter(f => f.url && f.mimeType?.startsWith('video'))
        .sort((a, b) => (b.height || 0) - (a.height || 0));

      // Audio-only streams
      const audioOnly = adaptiveFormats
        .filter(f => f.url && f.mimeType?.startsWith('audio'))
        .sort((a, b) => (b.bitrate || 0) - (a.bitrate || 0));

      console.log('📊 Available streams:');
      console.log('   Muxed (video+audio, direct download):');
      muxed.forEach((f, i) => {
        const ext  = f.mimeType.match(/video\/(\w+)/)?.[1] || 'mp4';
        const size = f.contentLength ? ' ~' + (f.contentLength / 1024 / 1024).toFixed(1) + 'MB' : '';
        console.log('   [' + i + '] ' + (f.height || '?') + 'p  ' + ext + size);
      });

      if (videoOnly.length > 0) {
        console.log('   Adaptive video-only (needs audio merge — use yt-dlp for 1080p+):');
        videoOnly.slice(0, 3).forEach((f, i) => {
          const ext = f.mimeType.match(/video\/(\w+)/)?.[1] || 'mp4';
          console.log('   [v' + i + '] ' + (f.height || '?') + 'p  ' + ext);
        });
      }

      if (muxed.length === 0) {
        console.warn('⚠️ No directly downloadable streams found (may be age-restricted or private).');
        console.log('👉 Use yt-dlp instead:');
        console.log('   yt-dlp "' + url + '" -o "' + title + '.mp4"');
        return;
      }

      // Show quality picker in console
      console.log('');
      console.log('👆 Choose a quality — run one of these in Console:');
      muxed.forEach((f, i) => {
        const ext  = f.mimeType.match(/video\/(\w+)/)?.[1] || 'mp4';
        const res  = (f.height || 'unknown') + 'p';
        console.log('  // ' + res);
        console.log('  __gdriveDownloadStream(' + i + ')');
      });
      console.log('');

      // Expose picker function globally
      window.__gdriveDownloadStream = (index) => {
        const f   = muxed[index];
        if (!f) { console.error('Invalid index'); return; }
        const ext = f.mimeType.match(/video\/(\w+)/)?.[1] || 'mp4';
        const res = (f.height || 'unknown') + 'p';
        const filename = title + '_' + res + '.' + ext;
        console.log('⬇️ Downloading: ' + filename);
        triggerDownload(f.url, filename);
      };

      // Auto-download best muxed quality (highest resolution)
      console.log('✅ Auto-downloading best available muxed quality: ' + (muxed[0].height || '?') + 'p');
      console.log('   (Run __gdriveDownloadStream(N) to pick a different quality)');
      const best    = muxed[0];
      const bestExt = best.mimeType.match(/video\/(\w+)/)?.[1] || 'mp4';
      const bestRes = (best.height || 'best') + 'p';
      triggerDownload(best.url, title + '_' + bestRes + '.' + bestExt);
      return;
    }
    const videoEl = document.querySelector('video');
    const domSrc  = videoEl?.src
      || videoEl?.querySelector('source[src]')?.src
      || [...(videoEl?.querySelectorAll('source') || [])].find(s => s.src)?.src;

    if (domSrc && !domSrc.startsWith('blob:')) {
      console.log('✅ Found direct video URL in DOM');
      const ext = domSrc.match(/\.(mp4|webm|mov)/i)?.[1] || 'mp4';
      triggerDownload(domSrc, title + '.' + ext);
      console.log('🎬 Downloading → ' + title + '.' + ext);
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
    // NOTE: videoplayback / googlevideo URLs are segmented streams — downloading
    // one segment gives a broken file. Route all stream URLs to yt-dlp instead.
    if (directMp4.length > 0) {
      const best = directMp4.sort((a, b) => b.length - a.length)[0];
      console.log('✅ Direct MP4 found — downloading...');
      triggerDownload(best, title + '.mp4');
      console.log('🎬 Downloading → ' + title + '.mp4');
      return;
    }

    // Step 6: All streaming formats → yt-dlp / ffmpeg command
    // Includes: HLS (.m3u8), DASH (.mpd), videoplayback, googlevideo
    const ytdlpTarget =
      hlsUrls[0] ||
      dashUrls[0] ||
      streamUrls.sort((a, b) => b.length - a.length)[0]; // longest = most params

    if (ytdlpTarget) {
      const streamType =
        hlsUrls.length     > 0 ? 'HLS (.m3u8)' :
        dashUrls.length    > 0 ? 'DASH (.mpd)' :
                                  'Stream (videoplayback)';
      console.warn('⚠️ ' + streamType + ' detected — segmented stream, cannot download directly.');
      console.log('');
      console.log('📋 yt-dlp command:');
      console.log('  yt-dlp "' + ytdlpTarget + '" -o "' + title + '.mp4"');
      console.log('');
      console.log('📋 ffmpeg command:');
      console.log('  ffmpeg -i "' + ytdlpTarget + '" -c copy "' + title + '.mp4"');
      console.log('');
      console.log('💡 Tip: install yt-dlp →  brew install yt-dlp  or  pip install yt-dlp');
      return;
    }

    // Fallback: show all captured URLs
    console.log('📋 All captured URLs:');
    urls.forEach((u, i) => console.log('  [' + i + '] ' + u));
    console.log('Copy the most relevant URL and use with yt-dlp.');
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

    await loadScript('https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js');
    const { jsPDF } = window.jspdf;
    console.log('📄 Found ' + blobImgs.length + ' pages — SCALE:' + SCALE + ' QUALITY:' + QUALITY);

    let pdf = null;
    for (let i = 0; i < blobImgs.length; i++) {
      const img = blobImgs[i];
      const w   = Math.round(img.width  * SCALE);
      const h   = Math.round(img.height * SCALE);
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

    const filename = getTitle() + '.pdf';
    await pdf.save(filename, { returnPromise: true });
    console.log('🎉 Done! Downloaded: ' + filename);
  };

  // ── Main ────────────────────────────────────────────────────────

  const type  = detect();
  const title = getTitle();
  const ext   = getTitleExt();
  const getId = () => url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];

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
    const id = getId(); if (!id) { console.error('Cannot get form ID'); return; }
    triggerDownload('https://docs.google.com/forms/d/' + id + '/export?format=csv', title + '.csv');
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
    const el      = document.querySelector('.drive-viewer-text-container, pre');
    const content = el?.innerText ?? document.body.innerText;
    const blob    = new Blob([content], { type: 'text/plain;charset=utf-8' });
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
