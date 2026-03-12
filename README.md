# GDrive Universal Downloader

> ⚠️ **合法使用提醒**：本腳本僅供用於您本人擁有或已獲授權存取的文件。請遵守著作權及 Google 服務條款。

一段瀏覽器 Console 腳本，自動偵測 Google Drive 檔案類型，選擇最佳下載策略。

---

## 支援格式

| 類型 | 偵測方式 | 下載格式 |
|------|---------|---------|
| 📄 PDF / Office 預覽 | DOM canvas + `data-page-number` | `.pdf` |
| 📝 Google Docs | URL 含 `/document/` | `.docx` |
| 📊 Google Sheets | URL 含 `/spreadsheets/` | `.xlsx` |
| 📑 Google Slides | URL 含 `/presentation/` | `.pptx` |
| 🖼️ 圖片 | DOM `img.stretch-fit` | 原始格式 |
| 🎬 影片 | DOM `<video>` | 來源 URL |
| 📋 文字 / CSV | DOM 文字容器 | `.txt` |

---

## 使用方式

1. 用 **Chrome** 或 **Edge** 開啟 Google Drive 檔案預覽頁面
2. 按 `F12`（或 `Ctrl+Shift+J` / Mac：`⌘⌥J`）開啟 **Console**
3. 複製下方腳本，貼入 Console 後按 **Enter**
4. 腳本自動偵測類型並下載，檔名沿用原始標題

---

## 腳本

```javascript
// ================================================================
// GDrive Universal Downloader
// 支援：PDF、Google Docs/Sheets/Slides、圖片、影片、文字、Office 檔
// 使用方式：在 Google Drive 檔案預覽頁面的 Console 執行
// ================================================================

(async function gdriveUniversalDownload() {

  // ── 工具函式 ──────────────────────────────────────────────────

  const getTitle = () =>
    document.title
      .replace(/\s*[-–—]\s*Google.*/i, '')
      .replace(/\.\w{2,5}$/, '')
      .trim() || 'gdrive-file';

  const triggerDownload = (url, filename) => {
    const a = document.createElement('a');
    a.href = url; a.download = filename;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
  };

  const sleep = ms => new Promise(r => setTimeout(r, ms));

  const loadScript = src => new Promise((resolve, reject) => {
    if (document.querySelector(`script[src="${src}"]`)) { resolve(); return; }
    const s = document.createElement('script');
    s.src = src; s.onload = resolve; s.onerror = reject;
    document.head.appendChild(s);
  });

  // ── 偵測檔案類型 ──────────────────────────────────────────────

  const url = window.location.href;

  const detect = () => {
    if (/docs\.google\.com\/document/i.test(url))       return 'gdoc';
    if (/docs\.google\.com\/spreadsheets/i.test(url))   return 'gsheet';
    if (/docs\.google\.com\/presentation/i.test(url))   return 'gslides';
    if (document.querySelector('div[data-page-number] canvas')) return 'canvas';
    if (document.querySelector('video[src], video source'))     return 'video';
    if (document.querySelector('img.stretch-fit, #drive-viewer-main-content img')) return 'image';
    if (document.querySelector('.drive-viewer-text-container, .docs-texteventtarget-iframe')) return 'text';
    return 'unknown';
  };

  const type  = detect();
  const title = getTitle();

  console.log(`🔍 偵測類型：${type}　📝 檔名：${title}`);

  // ── 策略 1：Google Docs → docx ────────────────────────────────
  if (type === 'gdoc') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('無法取得文件 ID'); return; }
    console.log('📄 下載 Google Docs → .docx');
    triggerDownload(
      `https://docs.google.com/document/d/${id}/export?format=docx`,
      `${title}.docx`
    );
    return;
  }

  // ── 策略 2：Google Sheets → xlsx ──────────────────────────────
  if (type === 'gsheet') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('無法取得試算表 ID'); return; }
    console.log('📊 下載 Google Sheets → .xlsx');
    triggerDownload(
      `https://docs.google.com/spreadsheets/d/${id}/export?format=xlsx`,
      `${title}.xlsx`
    );
    return;
  }

  // ── 策略 3：Google Slides → pptx ──────────────────────────────
  if (type === 'gslides') {
    const id = url.match(/\/d\/([a-zA-Z0-9_-]+)/)?.[1];
    if (!id) { console.error('無法取得簡報 ID'); return; }
    console.log('📑 下載 Google Slides → .pptx');
    triggerDownload(
      `https://docs.google.com/presentation/d/${id}/export/pptx`,
      `${title}.pptx`
    );
    return;
  }

  // ── 策略 4：圖片 → 直接下載 ───────────────────────────────────
  if (type === 'image') {
    const img = document.querySelector(
      'img.stretch-fit, #drive-viewer-main-content img'
    );
    if (!img?.src) { console.error('找不到圖片來源'); return; }
    const ext = img.src.match(/\.(png|jpg|jpeg|gif|webp|svg)/i)?.[1] ?? 'jpg';
    console.log(`🖼️ 下載圖片 → .${ext}`);
    triggerDownload(img.src, `${title}.${ext}`);
    return;
  }

  // ── 策略 5：影片 → 擷取來源 URL ──────────────────────────────
  if (type === 'video') {
    const video = document.querySelector('video');
    const src =
      video?.src ||
      video?.querySelector('source')?.src ||
      [...(video?.querySelectorAll('source') ?? [])].find(s => s.src)?.['src'];
    if (!src) {
      console.warn('⚠️ 無法直接取得影片 URL（可能受 DRM 保護）。');
      console.log('📋 請嘗試：右鍵點影片 → 另存影片');
      return;
    }
    console.log(`🎬 影片 URL：${src}`);
    console.log('📋 請在新分頁開啟上方 URL 後右鍵另存，或複製到下載工具。');
    window.open(src, '_blank');
    return;
  }

  // ── 策略 6：文字 / CSV → .txt ────────────────────────────────
  if (type === 'text') {
    const el = document.querySelector('.drive-viewer-text-container, pre');
    const content = el?.innerText ?? document.body.innerText;
    const blob = new Blob([content], { type: 'text/plain;charset=utf-8' });
    console.log('📋 下載文字內容 → .txt');
    triggerDownload(URL.createObjectURL(blob), `${title}.txt`);
    return;
  }

  // ── 策略 7：Canvas 渲染（PDF / Office 預覽）→ PDF ────────────
  if (type === 'canvas') {
    const DELAY_MS = 800; // 每頁等待時間(ms)，網速慢請調高至 1200~1500
    const SCALE    = 2;   // 解析度倍數 (1=原始, 2=高清)

    await loadScript(
      'https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js'
    );
    const { jsPDF } = window.jspdf;

    const getPages = () =>
      [...document.querySelectorAll('div[data-page-number]')]
        .sort((a, b) =>
          parseInt(a.getAttribute('data-page-number')) -
          parseInt(b.getAttribute('data-page-number')));

    let pages = getPages();
    console.log(`📄 偵測到 ${pages.length} 頁，開始捲動載入...`);

    for (let i = 0; i < pages.length; i++) {
      pages[i].scrollIntoView();
      await sleep(DELAY_MS);
      console.log(`  ⏳ ${i + 1} / ${pages.length}`);
    }

    pages = getPages();
    const images = [];
    let maxW = 0, maxH = 0;

    for (let i = 0; i < pages.length; i++) {
      const canvas = pages[i].querySelector('canvas');
      if (!canvas) { console.warn(`第 ${i+1} 頁無 canvas，跳過`); continue; }
      const off = document.createElement('canvas');
      off.width  = canvas.width  * SCALE;
      off.height = canvas.height * SCALE;
      const ctx = off.getContext('2d');
      ctx.scale(SCALE, SCALE);
      ctx.drawImage(canvas, 0, 0);
      images.push({ data: off.toDataURL('image/jpeg', 0.95), w: canvas.width, h: canvas.height });
      if (canvas.width > maxW) { maxW = canvas.width; maxH = canvas.height; }
      console.log(`  🖼️ 擷取第 ${i+1} / ${pages.length} 頁`);
    }

    if (!images.length) { console.error('未能擷取任何頁面'); return; }

    console.log('📦 組合 PDF...');
    const pdf = new jsPDF({
      orientation: maxW >= maxH ? 'landscape' : 'portrait',
      unit: 'px', format: [maxW, maxH], compress: true,
    });
    images.forEach((img, i) => {
      if (i > 0) pdf.addPage([img.w, img.h], img.w >= img.h ? 'landscape' : 'portrait');
      pdf.addImage(img.data, 'JPEG', 0, 0, img.w, img.h);
    });
    pdf.save(`${title}.pdf`);
    console.log(`🎉 完成！已下載：${title}.pdf（${images.length} 頁）`);
    return;
  }

  // ── 找不到對應策略 ────────────────────────────────────────────
  console.warn('⚠️ 無法自動偵測檔案類型。');
  console.log('請確認：\n1. 已在 Google Drive 預覽頁面執行\n2. 檔案已完全載入\n3. 嘗試重新整理後再執行');

})();
```

---

## 注意事項

- **Docs / Sheets / Slides**：Export API 需要您已登入 Google 帳號
- **影片**：部分影片受 DRM 保護，腳本會嘗試開啟來源 URL
- **Canvas 模式**（PDF/Office）：頁數多時較慢，可將 `DELAY_MS` 調高至 `1500`
- **解析度**：`SCALE=2` 為高清，`SCALE=1` 可縮小檔案大小

---

## 依賴

Canvas 模式需動態載入 [jsPDF 2.5.1](https://github.com/parallax/jsPDF)（via cdnjs），其餘模式無需任何依賴。
