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

**View-Only PDF (most common case)**

1. Open the Google Drive PDF link in **Chrome** or **Edge**
2. Click the three-dot menu (top right) → **"Open in new window"**
3. **Manually scroll to the bottom** to ensure all pages are rendered
4. Press `F12` to open Console
5. If Chrome shows a paste warning, type `allow pasting` and press Enter first
6. Paste the script below and press **Enter**

**All other formats**

Open the file preview in Google Drive, open Console, paste and run the script. It will auto-detect the type.

### Limitations

| Situation | Result |
|-----------|--------|
| Owner disabled downloads (Docs/Sheets/Slides) | Export API blocked by Google |
| DRM-protected video | Script opens source URL; manual save required |
| View-Only PDF not fully scrolled | Script will warn you to scroll first |

### Dependencies

View-Only PDF mode dynamically loads [jsPDF](https://github.com/parallax/jsPDF) via unpkg. All other strategies require no external dependencies.

### Credits

View-Only PDF capture method based on [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader)

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

**View-Only PDF（最常見情況）**

1. 用 **Chrome** 或 **Edge** 開啟 Google Drive PDF 連結
2. 點右上角三點選單 → **「在新視窗開啟」**
3. **手動捲動到底部**，確認所有頁面都已載入
4. 按 `F12` 開啟 Console
5. 若 Chrome 提示無法貼上，先輸入 `allow pasting` 按 Enter
6. 複製下方腳本貼入 Console，按 **Enter**

**其他格式**

直接在 Google Drive 預覽頁面開啟 Console 執行即可，腳本會自動偵測類型。

### 限制

| 情況 | 結果 |
|------|------|
| 擁有者關閉下載（Docs/Sheets/Slides） | Export API 被 Google 封鎖 |
| DRM 保護的影片 | 腳本會開啟來源 URL，需手動另存 |
| View-Only PDF 未捲動到底 | 腳本會提示你先捲動 |

### 依賴

View-Only PDF 模式動態載入 [jsPDF](https://github.com/parallax/jsPDF)（via unpkg），其餘模式無需任何外部依賴。

### 致謝

View-Only PDF 擷取方法參考自 [zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader](https://github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader)

---

## Script / 腳本

```javascript
// ================================================================
// GDrive Universal Downloader v2.0
// Supports: View-Only PDF, Docs, Sheets, Slides, Forms, Drawings,
//           Images, Video, Audio, and all other Drive-hosted files
// Reference: github.com/zavierferodova/Google-Drive-View-Only-PDF-Script-Downloader
// ================================================================

(function () {
  console.log('🚀 GDrive Universal Downloader v2.0 starting...');

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
    const meta = document.querySelector('meta[itemprop="name"]')?.content;
    const raw  = meta || document.title;
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

  const type  = detect();
  const title = getTitle();
  const ext   = getTitleExt();

  console.log('🔍 Detected: ' + type + ' | File: ' + title);

  // ── Strategy 1: View-Only PDF ───────────────────────────────────
  if (type === 'blob-pdf') {
    const blobImgs = [...document.getElementsByTagName('img')]
      .filter(img => img.src.startsWith('blob:https://drive.google.com/'));
    if (blobImgs.length === 0) {
      console.error('❌ No page images found.');
      console.log('👉 Please scroll to the BOTTOM of the PDF first, then run the script again.');
      return;
    }
    loadScript('https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js').then(() => {
      const { jsPDF } = window.jspdf;
      console.log('📄 Found ' + blobImgs.length + ' pages, generating PDF...');
      let pdf = null;
      for (let i = 0; i < blobImgs.length; i++) {
        const img    = blobImgs[i];
        const canvas = document.createElement('canvas');
        const ctx    = canvas.getContext('2d');
        canvas.width = img.naturalWidth; canvas.height = img.naturalHeight;
        ctx.drawImage(img, 0, 0, img.naturalWidth, img.naturalHeight);
        const imgData     = canvas.toDataURL('image/png');
        const orientation = img.naturalWidth > img.naturalHeight ? 'l' : 'p';
        if (i === 0) {
          pdf = new jsPDF({ orientation, unit: 'px', format: [img.naturalWidth, img.naturalHeight] });
        } else {
          pdf.addPage([img.naturalWidth, img.naturalHeight], orientation);
        }
        pdf.addImage(imgData, 'PNG', 0, 0, img.naturalWidth, img.naturalHeight, '', 'SLOW');
        console.log('  🖼️ Page ' + (i + 1) + '/' + blobImgs.length + ' (' + Math.floor((i + 1) / blobImgs.length * 100) + '%)');
      }
      pdf.save(title + '.pdf', { returnPromise: true }).then(() => {
        console.log('🎉 Done! Downloaded: ' + title + '.pdf');
      });
    }).catch(err => console.error('jsPDF load failed:', err));
    return;
  }

  // ── Strategy 2: Google Docs → .docx ────────────────────────────
  if (type === 'gdoc') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('Cannot get document ID'); return; }
    triggerDownload('https://docs.google.com/document/d/' + id + '/export?format=docx', title + '.docx');
    console.log('📝 Downloading → ' + title + '.docx');
    return;
  }

  // ── Strategy 3: Google Sheets → .xlsx ──────────────────────────
  if (type === 'gsheet') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('Cannot get spreadsheet ID'); return; }
    triggerDownload('https://docs.google.com/spreadsheets/d/' + id + '/export?format=xlsx', title + '.xlsx');
    console.log('📊 Downloading → ' + title + '.xlsx');
    return;
  }

  // ── Strategy 4: Google Slides → .pptx ──────────────────────────
  if (type === 'gslides') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('Cannot get presentation ID'); return; }
    triggerDownload('https://docs.google.com/presentation/d/' + id + '/export/pptx', title + '.pptx');
    console.log('📑 Downloading → ' + title + '.pptx');
    return;
  }

  // ── Strategy 5: Google Forms → .csv ────────────────────────────
  if (type === 'gforms') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('Cannot get form ID'); return; }
    triggerDownload('https://docs.google.com/forms/d/' + id + '/export?format=csv', title + '.csv');
    console.log('📋 Downloading → ' + title + '.csv');
    return;
  }

  // ── Strategy 6: Google Drawings → .svg ─────────────────────────
  if (type === 'gdrawings') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('Cannot get drawing ID'); return; }
    triggerDownload('https://docs.google.com/drawings/d/' + id + '/export/svg', title + '.svg');
    console.log('🎨 Downloading → ' + title + '.svg');
    return;
  }

  // ── Strategy 7: Video ───────────────────────────────────────────
  if (type === 'video') {
    const video = document.querySelector('video');
    const src   = video?.src || video?.querySelector('source[src]')?.src
      || [...(video?.querySelectorAll('source') || [])].find(s => s.src)?.src;
    if (!src) {
      console.warn('⚠️ Cannot get video URL (may be DRM protected).');
      console.log('👉 Right-click the video → Save video as');
      return;
    }
    console.log('🎬 Opening video URL in new tab → right-click to save');
    window.open(src, '_blank');
    return;
  }

  // ── Strategy 8: Audio ───────────────────────────────────────────
  if (type === 'audio') {
    const audio = document.querySelector('audio');
    const src   = audio?.src || audio?.querySelector('source[src]')?.src;
    if (!src) { console.warn('⚠️ Cannot get audio URL.'); return; }
    const aExt = src.match(/\.(mp3|wav|ogg|flac|aac|m4a)/i)?.[1] || ext || 'mp3';
    triggerDownload(src, title + '.' + aExt);
    console.log('🎵 Downloading → ' + title + '.' + aExt);
    return;
  }

  // ── Strategy 9: Image ───────────────────────────────────────────
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

  // ── Strategy 10: Text / CSV ─────────────────────────────────────
  if (type === 'text') {
    const el      = document.querySelector('.drive-viewer-text-container, pre');
    const content = el?.innerText ?? document.body.innerText;
    const blob    = new Blob([content], { type: 'text/plain;charset=utf-8' });
    const tExt    = ext || 'txt';
    triggerDownload(URL.createObjectURL(blob), title + '.' + tExt);
    console.log('📋 Downloading → ' + title + '.' + tExt);
    return;
  }

  // ── Strategy 11: Generic file export ───────────────────────────
  if (type === 'file-export') {
    const id   = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('Cannot get file ID'); return; }
    const fExt = ext || 'pdf';
    triggerDownload('https://drive.google.com/uc?export=download&id=' + id, title + '.' + fExt);
    console.log('📁 Downloading → ' + title + '.' + fExt);
    console.log('⚠️ If a "no permission" page appears, the owner has disabled downloads.');
    return;
  }

  // ── No strategy matched ─────────────────────────────────────────
  console.warn('⚠️ Could not auto-detect file type.');
  console.log(
    'Please check:\n' +
    '1. You are on a Google Drive file preview page\n' +
    '2. For PDFs: scroll to the bottom first to load all pages\n' +
    '3. Try refreshing the page and running the script again'
  );

})();
```
