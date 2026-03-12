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

### File Size Tips

| Setting | File Size | Quality |
|---------|-----------|---------|
| `SCALE = 1.0` (recommended) | Smallest — matches screen resolution | Good |
| `SCALE = 1.5` | Medium | High |
| `SCALE = 2.0` | Largest — full retina resolution | Best |
| `QUALITY = 0.95` | Larger | Near-lossless |
| `QUALITY = 0.82` | Balanced (default) | High |
| `QUALITY = 0.70` | Smallest | Acceptable |

### Limitations

| Situation | Result |
|-----------|--------|
| Owner disabled downloads (Docs/Sheets/Slides) | Export API blocked by Google |
| DRM-protected video | Script opens source URL; manual save required |

### Credits

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
5. 貼上腳本按 **Enter**，腳本會自動捲動並下載，無需手動捲到底

**其他格式**

直接在 Google Drive 預覽頁面開啟 Console 執行，腳本自動偵測類型。

### 檔案大小調整

| 設定 | 檔案大小 | 品質 |
|------|---------|------|
| `SCALE = 1.0`（推薦） | 最小，符合螢幕解析度 | 良好 |
| `SCALE = 1.5` | 中等 | 高 |
| `SCALE = 2.0` | 最大，完整 retina 解析度 | 最佳 |
| `QUALITY = 0.95` | 較大 | 接近無損 |
| `QUALITY = 0.82` | 平衡（預設） | 高 |
| `QUALITY = 0.70` | 最小 | 尚可 |

### 限制

| 情況 | 結果 |
|------|------|
| 擁有者關閉下載（Docs/Sheets/Slides） | Export API 被 Google 封鎖 |
| DRM 保護的影片 | 腳本會開啟來源 URL，需手動另存 |

### 致謝

- [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader) — blob 圖片擷取方法
- [mhsohan/How-to-download-protected-view-only-files-from-google-drive-](https://github.com/mhsohan/How-to-download-protected-view-only-files-from-google-drive-) — 螢幕尺寸渲染與檔案大小優化

---

## Script / 腳本

```javascript
// ================================================================
// GDrive Universal Downloader v2.1
// Supports: View-Only PDF, Docs, Sheets, Slides, Forms, Drawings,
//           Images, Video, Audio, and all other Drive-hosted files
//
// Credits:
//   zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader
//   mhsohan/How-to-download-protected-view-only-files-from-google-drive-
// ================================================================

(function () {
  console.log('🚀 GDrive Universal Downloader v2.1 starting...');

  // ── Settings (adjust as needed) ────────────────────────────────
  // SCALE: 1.0 = screen size (smallest file), 2.0 = full retina (largest)
  const SCALE   = 1.0;
  // QUALITY: 0.0~1.0, higher = better quality but larger file
  const QUALITY = 0.82;
  // SCROLL_DELAY: ms to wait between scroll steps (increase on slow connections)
  const SCROLL_DELAY = 200;

  // ── Utilities ──────────────────────────────────────────────────

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

  // Auto-scroll to trigger lazy-loaded pages
  const autoScroll = async () => {
    console.log('⏬ Auto-scrolling to load all pages...');
    const scrollable = document.querySelector('.ndfHFb-c4YZDc-cYSp0e-DARUcf')
      || document.querySelector('[role="main"]')
      || document.documentElement;
    const total = scrollable.scrollHeight;
    const step  = window.innerHeight;
    for (let pos = 0; pos <= total; pos += step) {
      scrollable.scrollTo(0, pos);
      await sleep(SCROLL_DELAY);
    }
    // Scroll back to top
    scrollable.scrollTo(0, 0);
    await sleep(300);
    console.log('✅ Scroll complete');
  };

  // ── File Type Detection ─────────────────────────────────────────

  const url = window.location.href;

  const detect = () => {
    // View-Only PDF: rendered as blob images
    const blobImgs = [...document.getElementsByTagName('img')]
      .filter(img => img.src.startsWith('blob:https://drive.google.com/'));
    if (blobImgs.length > 0) return 'blob-pdf';

    if (/docs\.google\.com\/document/i.test(url))     return 'gdoc';
    if (/docs\.google\.com\/spreadsheets/i.test(url)) return 'gsheet';
    if (/docs\.google\.com\/presentation/i.test(url)) return 'gslides';
    if (/docs\.google\.com\/forms/i.test(url))        return 'gforms';
    if (/docs\.google\.com\/drawings/i.test(url))     return 'gdrawings';

    if (document.querySelector('video'))              return 'video';
    if (document.querySelector('audio'))              return 'audio';
    if (document.querySelector(
      'img.stretch-fit, #drive-viewer-main-content img, .drive-viewer-content img'
    ))                                                return 'image';
    if (document.querySelector(
      '.drive-viewer-text-container, .docs-texteventtarget-iframe, pre'
    ))                                                return 'text';
    if (/drive\.google\.com\/file\/d\//i.test(url))  return 'file-export';

    return 'unknown';
  };

  // ── Strategy 1: View-Only PDF ───────────────────────────────────
  // Key insight from mhsohan: use img.width (display size) NOT img.naturalWidth
  // (natural/retina size). naturalWidth can be 2x larger, making file 4x bigger.
  // Using display size gives much smaller files with perfectly readable quality.

  const processBlobPDF = async () => {
    await autoScroll();

    const blobImgs = [...document.getElementsByTagName('img')]
      .filter(img => img.src.startsWith('blob:https://drive.google.com/'));

    if (blobImgs.length === 0) {
      console.error('❌ No page images found even after scrolling.');
      console.log('👉 Try manually scrolling to the bottom of the PDF, then run the script again.');
      return;
    }

    await loadScript('https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js');
    const { jsPDF } = window.jspdf;

    console.log('📄 Found ' + blobImgs.length + ' pages, generating PDF...');
    console.log('   SCALE=' + SCALE + '  QUALITY=' + QUALITY);

    let pdf = null;

    for (let i = 0; i < blobImgs.length; i++) {
      const img = blobImgs[i];

      // Use display size × SCALE (not naturalWidth) — key to smaller file size
      const w = Math.round(img.width  * SCALE);
      const h = Math.round(img.height * SCALE);

      const canvas = document.createElement('canvas');
      const ctx    = canvas.getContext('2d');
      canvas.width  = w;
      canvas.height = h;
      ctx.drawImage(img, 0, 0, w, h);

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

  console.log('🔍 Detected: ' + type + ' | File: ' + title);

  if (type === 'blob-pdf') {
    processBlobPDF();
    return;
  }

  // Google Workspace exports
  const getId = () => url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];

  if (type === 'gdoc') {
    const id = getId();
    if (!id) { console.error('Cannot get document ID'); return; }
    triggerDownload('https://docs.google.com/document/d/' + id + '/export?format=docx', title + '.docx');
    console.log('📝 Downloading → ' + title + '.docx');
    return;
  }

  if (type === 'gsheet') {
    const id = getId();
    if (!id) { console.error('Cannot get spreadsheet ID'); return; }
    triggerDownload('https://docs.google.com/spreadsheets/d/' + id + '/export?format=xlsx', title + '.xlsx');
    console.log('📊 Downloading → ' + title + '.xlsx');
    return;
  }

  if (type === 'gslides') {
    const id = getId();
    if (!id) { console.error('Cannot get presentation ID'); return; }
    triggerDownload('https://docs.google.com/presentation/d/' + id + '/export/pptx', title + '.pptx');
    console.log('📑 Downloading → ' + title + '.pptx');
    return;
  }

  if (type === 'gforms') {
    const id = getId();
    if (!id) { console.error('Cannot get form ID'); return; }
    triggerDownload('https://docs.google.com/forms/d/' + id + '/export?format=csv', title + '.csv');
    console.log('📋 Downloading → ' + title + '.csv');
    return;
  }

  if (type === 'gdrawings') {
    const id = getId();
    if (!id) { console.error('Cannot get drawing ID'); return; }
    triggerDownload('https://docs.google.com/drawings/d/' + id + '/export/svg', title + '.svg');
    console.log('🎨 Downloading → ' + title + '.svg');
    return;
  }

  if (type === 'video') {
    const video = document.querySelector('video');
    const src   = video?.src || video?.querySelector('source[src]')?.src
      || [...(video?.querySelectorAll('source') || [])].find(s => s.src)?.src;
    if (!src) {
      console.warn('⚠️ Cannot get video URL (may be DRM protected).');
      console.log('👉 Right-click the video → Save video as');
      return;
    }
    console.log('🎬 Opening video in new tab → right-click to save');
    window.open(src, '_blank');
    return;
  }

  if (type === 'audio') {
    const audio = document.querySelector('audio');
    const src   = audio?.src || audio?.querySelector('source[src]')?.src;
    if (!src) { console.warn('⚠️ Cannot get audio URL.'); return; }
    const aExt = src.match(/\.(mp3|wav|ogg|flac|aac|m4a)/i)?.[1] || ext || 'mp3';
    triggerDownload(src, title + '.' + aExt);
    console.log('🎵 Downloading → ' + title + '.' + aExt);
    return;
  }

  if (type === 'image') {
    const img = document.querySelector(
      'img.stretch-fit, #drive-viewer-main-content img, .drive-viewer-content img'
    );
    if (!img?.src) { console.error('Cannot find image source'); return; }
    const iExt = img.src.match(/\.(png|jpg|jpeg|gif|webp|svg|bmp)/i)?.[1] || ext || 'jpg';
    triggerDownload(img.src, title + '.' + iExt);
    console.log('🖼️ Downloading → ' + title + '.' + iExt);
    return;
  }

  if (type === 'text') {
    const el      = document.querySelector('.drive-viewer-text-container, pre');
    const content = el?.innerText ?? document.body.innerText;
    const blob    = new Blob([content], { type: 'text/plain;charset=utf-8' });
    const tExt    = ext || 'txt';
    triggerDownload(URL.createObjectURL(blob), title + '.' + tExt);
    console.log('📋 Downloading → ' + title + '.' + tExt);
    return;
  }

  if (type === 'file-export') {
    const id   = getId();
    if (!id) { console.error('Cannot get file ID'); return; }
    const fExt = ext || 'pdf';
    triggerDownload('https://drive.google.com/uc?export=download&id=' + id, title + '.' + fExt);
    console.log('📁 Downloading → ' + title + '.' + fExt);
    console.log('⚠️ If a "no permission" page appears, the owner has disabled downloads.');
    return;
  }

  console.warn('⚠️ Could not auto-detect file type.');
  console.log(
    'Please check:\n' +
    '1. You are on a Google Drive file preview page\n' +
    '2. Try refreshing the page and running the script again'
  );

})();
```
