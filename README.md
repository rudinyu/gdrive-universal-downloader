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
| 🎬 Video files | DOM `<video>` element | Source URL |
| 🎵 Audio files | DOM `<audio>` element | Original format |
| 📁 Other files (PDF, Office, zip…) | `drive.google.com/file/d/` URL | Original format |

### Usage

**View-Only PDF**

1. Open the Google Drive PDF link in **Chrome** or **Edge**
2. Click the three-dot menu (top right) → **"Open in new window"**
3. Press `F12` to open Console
4. If Chrome shows a paste warning, type `allow pasting` and press Enter first
5. Paste the script below and press **Enter** — it will auto-scroll and download

**All other formats**

Open the file preview in Google Drive, open Console, paste and run. It auto-detects the type.

### Quality vs File Size Control

There are **two ways** to control output quality — use them independently or combine them:

#### Method A — Browser Zoom (no code change needed)
> Tip from [zeltox](https://github.com/zeltox/Google-Drive-PDF-Downloader)

Before running the script, zoom your browser in or out using `Ctrl +` / `Ctrl -`.
Since the script captures images at **screen display size**, browser zoom directly controls resolution:

| Browser Zoom | Effect |
|-------------|--------|
| 75% | Smaller file, lower quality |
| 100% | Default balanced |
| 150% | Higher quality, larger file |
| 200% | Best quality, largest file (may slow down) |

> ⚠️ Do not exceed 150% when using `SCALE > 1.0` in script settings — combined scaling can cause memory issues on large PDFs.

#### Method B — Script Settings
Adjust these values at the top of the script before running:

| Setting | Value | Effect |
|---------|-------|--------|
| `SCALE` | `1.0` | Capture at screen size (smallest file) ← recommended |
| `SCALE` | `1.5` | 1.5× screen size |
| `SCALE` | `2.0` | Full retina resolution (largest file) |
| `QUALITY` | `0.95` | Near-lossless JPEG |
| `QUALITY` | `0.82` | Balanced ← default |
| `QUALITY` | `0.70` | Smaller file, slightly lower quality |

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
| DRM-protected video | Script opens source URL; manual save required |

### Dependencies

View-Only PDF mode dynamically loads [jsPDF](https://github.com/parallax/jsPDF) via unpkg. All other strategies require no external dependencies.

### Credits

- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader) — auto-scroll & browser zoom quality tip
- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob image capture method
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — display-size rendering & file size optimization

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
| 🎬 影片 | DOM `<video>` 元素 | 來源 URL |
| 🎵 音訊 | DOM `<audio>` 元素 | 原始格式 |
| 📁 其他檔案（PDF、Office、zip…） | `drive.google.com/file/d/` URL | 原始格式 |

### 使用方式

**View-Only PDF**

1. 用 **Chrome** 或 **Edge** 開啟 Google Drive PDF 連結
2. 點右上角三點選單 → **「在新視窗開啟」**
3. 按 `F12` 開啟 Console
4. 若 Chrome 提示無法貼上，先輸入 `allow pasting` 按 Enter
5. 貼上腳本按 **Enter**，腳本會自動捲動並下載

**其他格式**

直接在 Google Drive 預覽頁面開啟 Console 執行，腳本自動偵測類型。

### 品質與檔案大小控制

有**兩種方式**可以控制輸出品質，可單獨使用或組合搭配：

#### 方式 A — 瀏覽器縮放（不需改程式碼）
> 技巧來源：[zeltox](https://github.com/zeltox/Google-Drive-PDF-Downloader)

執行腳本前，用 `Ctrl +` / `Ctrl -` 調整瀏覽器縮放比例。
因為腳本以**螢幕顯示尺寸**擷取圖片，瀏覽器縮放可直接控制解析度：

| 瀏覽器縮放 | 效果 |
|-----------|------|
| 75% | 檔案較小，品質較低 |
| 100% | 預設平衡 |
| 150% | 品質較高，檔案較大 |
| 200% | 最高品質，檔案最大（大型 PDF 可能變慢） |

> ⚠️ 若腳本的 `SCALE > 1.0`，瀏覽器縮放建議不超過 150%，兩者疊加可能造成記憶體問題。

#### 方式 B — 腳本參數
在執行前調整腳本頂部的設定值：

| 參數 | 值 | 效果 |
|------|---|------|
| `SCALE` | `1.0` | 以螢幕尺寸擷取（最小檔案）← 推薦 |
| `SCALE` | `1.5` | 1.5 倍螢幕尺寸 |
| `SCALE` | `2.0` | 完整 retina 解析度（最大檔案） |
| `QUALITY` | `0.95` | 接近無損 JPEG |
| `QUALITY` | `0.82` | 平衡 ← 預設 |
| `QUALITY` | `0.70` | 較小檔案，品質略降 |

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
| DRM 保護的影片 | 腳本會開啟來源 URL，需手動另存 |

### 依賴

View-Only PDF 模式動態載入 [jsPDF](https://github.com/parallax/jsPDF)（via unpkg），其餘模式無需任何外部依賴。

### 致謝

- [zeltox/Google-Drive-PDF-Downloader](https://github.com/zeltox/Google-Drive-PDF-Downloader) — 自動捲動 & 瀏覽器縮放品質技巧
- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob 圖片擷取方法
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — 螢幕尺寸渲染與檔案大小優化

---

## Script / 腳本

```javascript
// ================================================================
// GDrive Universal Downloader v2.2
// Supports: View-Only PDF, Docs, Sheets, Slides, Forms, Drawings,
//           Images, Video, Audio, and all other Drive-hosted files
//
// Quality Tips:
//   - Zoom browser (Ctrl+/Ctrl-) BEFORE running to control resolution
//   - Adjust SCALE and QUALITY below for fine-tuning
//
// Credits:
//   zeltox/Google-Drive-PDF-Downloader (auto-scroll, zoom tip)
//   zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader (blob capture)
//   mhsohan/How-to-download-protected-view-only-files-from-google-drive- (display size)
// ================================================================

(function () {
  console.log('🚀 GDrive Universal Downloader v2.2 starting...');

  // ── Settings ────────────────────────────────────────────────────
  // SCALE: 1.0 = screen size (recommended), 2.0 = full retina
  // Tip: use browser zoom (Ctrl+/-) instead of SCALE for quality control
  const SCALE        = 1.0;
  // QUALITY: 0.0~1.0 JPEG quality (0.82 = balanced, 0.95 = near-lossless)
  const QUALITY      = 0.82;
  // SCROLL_DELAY: ms between scroll steps (increase on slow connections)
  const SCROLL_DELAY = 200;

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

  // Auto-scroll to trigger all lazy-loaded pages (inspired by zeltox)
  const autoScroll = async () => {
    console.log('⏬ Auto-scrolling to load all pages...');
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

    if (document.querySelector('video'))   return 'video';
    if (document.querySelector('audio'))   return 'audio';
    if (document.querySelector(
      'img.stretch-fit, #drive-viewer-main-content img, .drive-viewer-content img'
    ))                                     return 'image';
    if (document.querySelector(
      '.drive-viewer-text-container, .docs-texteventtarget-iframe, pre'
    ))                                     return 'text';
    if (/drive\.google\.com\/file\/d\//i.test(url)) return 'file-export';

    return 'unknown';
  };

  // ── Strategy 1: View-Only PDF ───────────────────────────────────
  const processBlobPDF = async () => {
    await autoScroll();

    const blobImgs = [...document.getElementsByTagName('img')]
      .filter(img => img.src.startsWith('blob:https://drive.google.com/'));

    if (blobImgs.length === 0) {
      console.error('❌ No page images found even after scrolling.');
      console.log('👉 Try manually scrolling to the bottom of the PDF, then run again.');
      return;
    }

    await loadScript('https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js');
    const { jsPDF } = window.jspdf;

    const zoom = window.devicePixelRatio || 1;
    console.log('📄 Found ' + blobImgs.length + ' pages');
    console.log('   Browser zoom: ~' + Math.round((window.outerWidth / window.innerWidth) * 100) + '%'
      + '  SCALE: ' + SCALE + '  QUALITY: ' + QUALITY);

    let pdf = null;

    for (let i = 0; i < blobImgs.length; i++) {
      const img = blobImgs[i];

      // Use display size × SCALE (not naturalWidth) — keeps file size manageable
      // Browser zoom already affects img.width, so zooming browser = free quality boost
      const w = Math.round(img.width  * SCALE);
      const h = Math.round(img.height * SCALE);

      const canvas = document.createElement('canvas');
      canvas.width  = w;
      canvas.height = h;
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

  if (type === 'blob-pdf') { processBlobPDF(); return; }

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

  if (type === 'video') {
    const video = document.querySelector('video');
    const src   = video?.src || video?.querySelector('source[src]')?.src
      || [...(video?.querySelectorAll('source') || [])].find(s => s.src)?.src;
    if (!src) { console.warn('⚠️ Cannot get video URL. Right-click → Save video as'); return; }
    console.log('🎬 Opening video in new tab → right-click to save');
    window.open(src, '_blank'); return;
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
    const tExt    = ext || 'txt';
    triggerDownload(URL.createObjectURL(blob), title + '.' + tExt);
    console.log('📋 Downloading → ' + title + '.' + tExt); return;
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
