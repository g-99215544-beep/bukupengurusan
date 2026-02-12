# Prompt: Buku Pengurusan SK Sri Aman 2026 - Firebase Editable Document System

## Objektif Aplikasi

Bina Progressive Web Application (PWA) yang mengubah Buku Pengurusan PDF kepada sistem dokumen digital yang boleh diedit secara real-time menggunakan Firebase Realtime Database. **Setiap muka surat PDF akan ditukar kepada data yang boleh diedit**, menggantikan PDF statik dengan dokumen dinamik.

## Konsep Utama

**PENTING:** Aplikasi ini akan:
1. ✅ Ekstrak **SETIAP MUKA SURAT** dari PDF (buku-pengurusan-v2.pdf)
2. ✅ Convert kandungan setiap page kepada editable data (text, tables, images)
3. ✅ Simpan di Firebase dengan struktur page-by-page
4. ✅ Render pages dari Firebase (bukan PDF.js)
5. ✅ Admin boleh edit content terus di browser
6. ✅ Auto-save dan real-time sync

## Firebase Project Setup

**✅ Firebase project telah disediakan:**
- **Project ID:** `buku-pengurusan-sksa`
- **Region:** Asia Southeast 1 (Singapore)
- **Database URL:** `https://buku-pengurusan-sksa-default-rtdb.asia-southeast1.firebasedatabase.app`
- **Storage:** `buku-pengurusan-sksa.firebasestorage.app`

**Services yang perlu diaktifkan:**
1. ✅ Realtime Database - untuk simpan page content, guru, takwim
2. ✅ Authentication - untuk admin login (Email/Password)
3. ✅ Storage - untuk upload images dari PDF
4. ⚠️ Hosting - optional, untuk deploy aplikasi

### Langkah Setup Firebase Console

#### 1. Enable Realtime Database
```
1. Pergi ke Firebase Console: https://console.firebase.google.com/
2. Pilih project: buku-pengurusan-sksa
3. Sidebar > Build > Realtime Database
4. Click "Create Database"
5. Pilih location: asia-southeast1
6. Start in "test mode" (kita akan setup rules kemudian)
```

#### 2. Enable Authentication
```
1. Sidebar > Build > Authentication
2. Click "Get started"
3. Tab "Sign-in method"
4. Enable "Email/Password"
5. Tambah admin users:
   - Tab "Users" > Add user
   - Email: admin@sksa.edu.my (contoh)
   - Password: [set password yang kuat]
   - Copy UID untuk setup admin access
```

#### 3. Enable Storage
```
1. Sidebar > Build > Storage
2. Click "Get started"
3. Start in "test mode"
4. Location: asia-southeast1
```

#### 4. Setup Database Structure
```
1. Pergi ke Realtime Database
2. Click "+" untuk tambah root nodes:
   - metadata
   - pages
   - guru
   - takwim
   - categories
   - tableOfContents
   - admins
```

#### 5. Setup Admin Access
```
1. Di Realtime Database, tambah node: /admins
2. Structure:
   /admins
     /{uid_from_authentication}
       email: "admin@sksa.edu.my"
       nama: "Nama Admin"
       role: "superadmin"
```

---

## 1. Teknologi Stack

### Frontend
- **HTML5, CSS3, Vanilla JavaScript (ES6+)**
- **Quill.js** atau **TinyMCE** - Rich text editor untuk edit kandungan
- **html2canvas** - Screenshot/export page sebagai image (optional)
- **Google Fonts** - Plus Jakarta Sans

### Backend
- **Firebase Realtime Database** - Main database
- **Firebase Authentication** - Admin login
- **Firebase Storage** - Upload images/logos
- **Firebase Hosting** - Deploy aplikasi

### Libraries
```html
<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.x.x/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.x.x/firebase-database.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.x.x/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.x.x/firebase-storage.js"></script>

<!-- Rich Text Editor -->
<script src="https://cdn.quilljs.com/1.3.6/quill.js"></script>
<link href="https://cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">
```

---

## 2. Struktur Data Firebase (Page-by-Page)

```json
{
  "metadata": {
    "namaBuku": "Buku Pengurusan SK Sri Aman",
    "tahun": 2026,
    "versi": "3.0",
    "jumlahMukaSurat": 87,
    "tarikhKemaskini": "2026-02-11T10:30:00Z",
    "dikemaskiniOleh": "admin@sksa.edu.my"
  },

  "pages": {
    "page_001": {
      "pageNumber": 1,
      "tajuk": "Kulit Depan",
      "kategori": "cover",
      "contentType": "html",
      "content": "<div class='cover'><h1>Buku Pengurusan</h1><h2>SK Sri Aman</h2><p>2026</p></div>",
      "images": {
        "logo": "https://firebasestorage.../logo-sekolah.png"
      },
      "editable": true,
      "lastModified": "2026-02-11T10:30:00Z",
      "modifiedBy": "admin@sksa.edu.my"
    },

    "page_002": {
      "pageNumber": 2,
      "tajuk": "Kandungan",
      "kategori": "toc",
      "contentType": "html",
      "content": "<h2>Kandungan</h2><ol><li><a href='#page_003'>Visi & Misi - m/s 3</a></li><li><a href='#page_005'>Carta Organisasi - m/s 5</a></li>...</ol>",
      "editable": true,
      "lastModified": "2026-02-10T14:20:00Z",
      "modifiedBy": "admin@sksa.edu.my"
    },

    "page_003": {
      "pageNumber": 3,
      "tajuk": "Visi & Misi Sekolah",
      "kategori": "pentadbiran",
      "contentType": "html",
      "content": "<div class='section'><h2>Visi</h2><p>Menjadi sekolah cemerlang...</p><h2>Misi</h2><ul><li>Melahirkan murid...</li></ul></div>",
      "editable": true,
      "tags": ["visi", "misi", "objektif"],
      "lastModified": "2026-01-15T09:00:00Z",
      "modifiedBy": "gurubesar@sksa.edu.my"
    },

    "page_010": {
      "pageNumber": 10,
      "tajuk": "Senarai Guru & Kakitangan",
      "kategori": "pentadbiran",
      "contentType": "table",
      "content": {
        "headers": ["Bil", "Nama", "Jawatan", "Gred"],
        "rows": [
          ["1", "EN. AHMAD BIN ALI", "Guru Besar", "DG54"],
          ["2", "PN. SITI AMINAH", "GPK Pentadbiran", "DG48"],
          ["3", "EN. KUMAR A/L RAJA", "GPK HEM", "DG44"]
        ]
      },
      "editable": true,
      "lastModified": "2026-02-05T11:45:00Z"
    },

    "page_025": {
      "pageNumber": 25,
      "tajuk": "Carta Organisasi Pentadbiran",
      "kategori": "pentadbiran",
      "contentType": "image",
      "content": "https://firebasestorage.../carta-organisasi.png",
      "description": "Carta organisasi menunjukkan struktur pentadbiran sekolah",
      "editable": true,
      "allowUpload": true,
      "lastModified": "2026-01-20T16:00:00Z"
    },

    "page_049": {
      "pageNumber": 49,
      "tajuk": "Pengurusan Kurikulum - Bahasa Melayu",
      "kategori": "kurikulum",
      "contentType": "mixed",
      "sections": [
        {
          "type": "text",
          "content": "<h3>Ketua Panitia: PN. NORLIZA BINTI HASSAN</h3>"
        },
        {
          "type": "table",
          "content": {
            "headers": ["Ahli Panitia", "Tahun"],
            "rows": [
              ["CIK NURUL HUDA", "Tahun 1"],
              ["EN. ISMAIL", "Tahun 2"]
            ]
          }
        },
        {
          "type": "text",
          "content": "<h4>Objektif Panitia:</h4><ul><li>Meningkatkan penguasaan BM...</li></ul>"
        }
      ],
      "editable": true,
      "linkedData": {
        "guruId": "guru_015",
        "panitiaId": "panitia_bm"
      }
    }
  },

  "guru": {
    "guru_001": {
      "nama": "EN. AHMAD BIN ALI",
      "jawatan": "Guru Besar",
      "avatar": "👨‍🏫",
      "email": "ahmad@sksa.edu.my",
      "telefon": "012-3456789",
      "gred": "DG54",
      "tugasUtama": {
        "tugas1": {
          "label": "Ketua Pentadbir Sekolah",
          "kategori": "pentadbiran",
          "pageRef": "page_010"
        }
      },
      "editable": true
    }
  },

  "takwim": {
    "2026-01-08": [
      {
        "acara": "Mesyuarat Agung PIBG",
        "jenis": "sekolah",
        "masa": "14:00",
        "lokasi": "Dewan Sekolah",
        "warna": "#10b981",
        "pageRef": "page_075",
        "editable": true
      }
    ]
  },

  "categories": {
    "pentadbiran": {
      "nama": "Pentadbiran",
      "warna": "#f59e0b",
      "ikon": "⚙️",
      "pages": ["page_003", "page_010", "page_015"]
    },
    "kurikulum": {
      "nama": "Kurikulum",
      "warna": "#10b981",
      "ikon": "📚",
      "pages": ["page_045", "page_049", "page_052"]
    },
    "hem": {
      "nama": "Hal Ehwal Murid",
      "warna": "#3b82f6",
      "ikon": "👨‍🎓",
      "pages": ["page_060", "page_062"]
    },
    "kokurikulum": {
      "nama": "Kokurikulum",
      "warna": "#ec4899",
      "ikon": "⚽",
      "pages": ["page_068", "page_070"]
    }
  },

  "tableOfContents": [
    { "tajuk": "Kulit Depan", "pageId": "page_001" },
    { "tajuk": "Kandungan", "pageId": "page_002" },
    { "tajuk": "Visi & Misi", "pageId": "page_003" },
    { "tajuk": "Senarai Guru", "pageId": "page_010" },
    { "tajuk": "Pengurusan Kurikulum", "pageId": "page_045" }
  ]
}
```

---

## 3. Proses Ekstraksi PDF ke Firebase

### Step 1: Ekstrak Kandungan PDF

Gunakan salah satu kaedah:

#### A. Manual Extract (Recommended untuk quality control)
```javascript
// Baca PDF secara manual dan format content untuk setiap page
// Tools: Adobe Acrobat, Preview (Mac), atau PDF readers

Untuk setiap muka surat:
1. Copy text content
2. Identify structure (heading, paragraph, table, image)
3. Format as HTML
4. Save images to Firebase Storage
5. Create page object in Firebase
```

#### B. Automated Extract (Faster, kurang accuracy)
```javascript
// Gunakan PDF.js untuk extract text
// Gunakan OCR untuk images jika perlu

import { getDocument } from 'pdfjs-dist';

async function extractPDFPages(pdfUrl) {
  const pdf = await getDocument(pdfUrl).promise;
  const pages = {};

  for (let pageNum = 1; pageNum <= pdf.numPages; pageNum++) {
    const page = await pdf.getPage(pageNum);
    const textContent = await page.getTextContent();

    // Extract text
    let text = textContent.items.map(item => item.str).join(' ');

    // Convert to HTML structure
    let htmlContent = formatToHTML(text);

    // Save to Firebase
    pages[`page_${String(pageNum).padStart(3, '0')}`] = {
      pageNumber: pageNum,
      content: htmlContent,
      contentType: 'html',
      editable: true,
      lastModified: new Date().toISOString()
    };
  }

  return pages;
}

// Upload to Firebase
async function uploadPagesToFirebase(pages) {
  const db = firebase.database();
  await db.ref('pages').set(pages);
  console.log('All pages uploaded to Firebase!');
}
```

#### C. Hybrid Approach (Recommended)
```
1. Auto-extract semua pages menggunakan PDF.js
2. Review setiap page secara manual
3. Format tables, headings, lists dengan betul
4. Upload images ke Firebase Storage
5. Update page content dengan proper HTML
6. Verify semua links dan references
```

### Step 2: Upload Images ke Firebase Storage

```javascript
async function uploadImage(file, pageId) {
  const storageRef = firebase.storage().ref();
  const imageRef = storageRef.child(`pages/${pageId}/${file.name}`);

  await imageRef.put(file);
  const url = await imageRef.getDownloadURL();

  return url;
}

// Update page dengan image URL
async function updatePageImage(pageId, imageUrl) {
  await firebase.database()
    .ref(`pages/${pageId}/images`)
    .push(imageUrl);
}
```

### Step 3: Verify Data Structure

```javascript
// Check semua pages ada
async function verifyPages() {
  const snapshot = await firebase.database().ref('pages').once('value');
  const pages = snapshot.val();

  const totalPages = Object.keys(pages).length;
  console.log(`Total pages in Firebase: ${totalPages}`);

  // Check for missing pages
  for (let i = 1; i <= totalPages; i++) {
    const pageId = `page_${String(i).padStart(3, '0')}`;
    if (!pages[pageId]) {
      console.error(`Missing page: ${pageId}`);
    }
  }
}
```

---

## 4. Antara Muka Pengguna (3 Tab + Edit Mode)

### Tab 1: Buku (Document Viewer)

#### View Mode (Default)
```html
<div id="book-tab">
  <!-- Sidebar: Table of Contents -->
  <aside class="sidebar">
    <h3>Kandungan</h3>
    <nav id="toc-list">
      <!-- Generated from Firebase tableOfContents -->
    </nav>
  </aside>

  <!-- Main Content: Page Viewer -->
  <main class="page-viewer">
    <div class="page-controls">
      <button onclick="previousPage()">← Sebelum</button>
      <span id="page-indicator">Muka surat 1 / 87</span>
      <button onclick="nextPage()">Seterusnya →</button>
      <button onclick="toggleEditMode()" class="admin-only">✏️ Edit</button>
    </div>

    <div id="page-content" class="page-display">
      <!-- Rendered from Firebase pages/page_XXX/content -->
    </div>
  </main>
</div>
```

#### Edit Mode (Admin sahaja)
```html
<div id="edit-mode" style="display: none;">
  <div class="edit-toolbar">
    <button onclick="savePageContent()">💾 Simpan</button>
    <button onclick="cancelEdit()">❌ Batal</button>
    <select id="contentType">
      <option value="html">Text/HTML</option>
      <option value="table">Table</option>
      <option value="image">Image</option>
      <option value="mixed">Mixed</option>
    </select>
  </div>

  <!-- Rich Text Editor -->
  <div id="quill-editor"></div>

  <!-- Table Editor (if contentType = table) -->
  <div id="table-editor" style="display: none;">
    <button onclick="addRow()">+ Row</button>
    <button onclick="addColumn()">+ Column</button>
    <table id="editable-table"></table>
  </div>

  <!-- Image Upload (if contentType = image) -->
  <div id="image-uploader" style="display: none;">
    <input type="file" accept="image/*" onchange="uploadPageImage(event)">
    <img id="preview-image" src="">
  </div>
</div>
```

### Tab 2: Guru (Directory dengan link ke pages)

```html
<div id="guru-tab">
  <!-- Filter -->
  <div class="filter-bar">
    <button onclick="filterGuru('all')">Semua</button>
    <button onclick="filterGuru('pentadbiran')">Pentadbiran</button>
    <button onclick="filterGuru('kurikulum')">Kurikulum</button>
    <button onclick="filterGuru('hem')">HEM</button>
    <button onclick="filterGuru('kokurikulum')">Kokurikulum</button>
  </div>

  <!-- Guru Grid -->
  <div class="guru-grid">
    <!-- Each card generated from Firebase guru/ -->
    <div class="guru-card" onclick="viewGuruProfile('guru_001')">
      <div class="avatar">👨‍🏫</div>
      <h3>EN. AHMAD BIN ALI</h3>
      <p>Guru Besar</p>
      <button class="admin-only" onclick="editGuru('guru_001')">Edit</button>
    </div>
  </div>

  <!-- Guru Detail Modal -->
  <div id="guru-detail-modal" style="display: none;">
    <h2 id="guru-nama"></h2>
    <p id="guru-jawatan"></p>
    <p id="guru-email"></p>

    <h3>Tugas & Tanggungjawab:</h3>
    <ul id="guru-tugas">
      <!-- Each tugas with link to page -->
      <li>
        <span>Ketua Pentadbir</span>
        <a href="#" onclick="goToPage('page_010')">→ Lihat (m/s 10)</a>
      </li>
    </ul>

    <button class="admin-only" onclick="editGuru()">✏️ Edit Guru</button>
  </div>
</div>
```

### Tab 3: Takwim (Calendar dengan link ke pages)

```html
<div id="takwim-tab">
  <div class="calendar-header">
    <button onclick="prevMonth()">←</button>
    <h2 id="current-month">Januari 2026</h2>
    <button onclick="nextMonth()">→</button>
    <button class="admin-only" onclick="addEvent()">+ Tambah Acara</button>
  </div>

  <div class="calendar-grid">
    <!-- Generated monthly calendar -->
  </div>

  <!-- Event Detail Modal -->
  <div id="event-modal" style="display: none;">
    <h3 id="event-title"></h3>
    <p id="event-details"></p>
    <a href="#" onclick="goToPage('page_075')">📄 Lihat dalam Buku (m/s 75)</a>
    <button class="admin-only" onclick="editEvent()">✏️ Edit</button>
  </div>
</div>
```

---

## 5. Fungsi-fungsi Utama JavaScript

### A. Firebase Operations

```javascript
// Initialize Firebase - SK Sri Aman Production Config
const firebaseConfig = {
  apiKey: "AIzaSyCjIjJfQTbCIY3QQtlF3zVfA2Ggn9Twnjs",
  authDomain: "buku-pengurusan-sksa.firebaseapp.com",
  databaseURL: "https://buku-pengurusan-sksa-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "buku-pengurusan-sksa",
  storageBucket: "buku-pengurusan-sksa.firebasestorage.app",
  messagingSenderId: "658418178889",
  appId: "1:658418178889:web:d8e93d3ab6cd0f8246d898",
  measurementId: "G-74FGKQNHLQ"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// Fetch page content
async function loadPage(pageId) {
  const snapshot = await db.ref(`pages/${pageId}`).once('value');
  const pageData = snapshot.val();
  renderPage(pageData);
}

// Save edited page
async function savePageContent(pageId, content) {
  await db.ref(`pages/${pageId}`).update({
    content: content,
    lastModified: new Date().toISOString(),
    modifiedBy: firebase.auth().currentUser.email
  });

  showNotification('Halaman berjaya disimpan!');
}

// Real-time listener
db.ref('pages').on('value', (snapshot) => {
  const pages = snapshot.val();
  updateTOC(pages);
});
```

### B. Page Rendering

```javascript
function renderPage(pageData) {
  const container = document.getElementById('page-content');

  switch(pageData.contentType) {
    case 'html':
      container.innerHTML = pageData.content;
      break;

    case 'table':
      renderTable(container, pageData.content);
      break;

    case 'image':
      container.innerHTML = `<img src="${pageData.content}" alt="${pageData.tajuk}">`;
      break;

    case 'mixed':
      renderMixedContent(container, pageData.sections);
      break;
  }

  // Update page indicator
  document.getElementById('page-indicator').textContent =
    `Muka surat ${pageData.pageNumber} / 87`;
}

function renderTable(container, tableData) {
  let html = '<table class="data-table"><thead><tr>';

  // Headers
  tableData.headers.forEach(header => {
    html += `<th>${header}</th>`;
  });
  html += '</tr></thead><tbody>';

  // Rows
  tableData.rows.forEach(row => {
    html += '<tr>';
    row.forEach(cell => {
      html += `<td>${cell}</td>`;
    });
    html += '</tr>';
  });
  html += '</tbody></table>';

  container.innerHTML = html;
}

function renderMixedContent(container, sections) {
  container.innerHTML = '';
  sections.forEach(section => {
    const div = document.createElement('div');
    div.className = `section-${section.type}`;

    if (section.type === 'text') {
      div.innerHTML = section.content;
    } else if (section.type === 'table') {
      renderTable(div, section.content);
    }

    container.appendChild(div);
  });
}
```

### C. Edit Mode

```javascript
let quillEditor;

function toggleEditMode() {
  const isAdmin = checkAdminRole();
  if (!isAdmin) {
    alert('Hanya admin boleh edit');
    return;
  }

  const viewMode = document.querySelector('.page-display');
  const editMode = document.getElementById('edit-mode');

  // Toggle display
  viewMode.style.display = 'none';
  editMode.style.display = 'block';

  // Initialize Quill editor
  if (!quillEditor) {
    quillEditor = new Quill('#quill-editor', {
      theme: 'snow',
      modules: {
        toolbar: [
          ['bold', 'italic', 'underline'],
          ['link', 'image'],
          [{ 'list': 'ordered'}, { 'list': 'bullet' }],
          [{ 'header': [1, 2, 3, false] }]
        ]
      }
    });
  }

  // Load current content
  const currentContent = viewMode.innerHTML;
  quillEditor.root.innerHTML = currentContent;
}

async function savePageContent() {
  const pageId = getCurrentPageId();
  const content = quillEditor.root.innerHTML;

  try {
    await db.ref(`pages/${pageId}`).update({
      content: content,
      lastModified: firebase.database.ServerValue.TIMESTAMP,
      modifiedBy: firebase.auth().currentUser.email
    });

    showNotification('✅ Halaman berjaya disimpan!', 'success');
    toggleEditMode(); // Exit edit mode
    loadPage(pageId); // Reload page
  } catch (error) {
    showNotification('❌ Ralat menyimpan: ' + error.message, 'error');
  }
}

function cancelEdit() {
  if (confirm('Batalkan perubahan?')) {
    toggleEditMode();
  }
}
```

### D. Search & Navigation

```javascript
function handleGlobalSearch(query) {
  const results = [];

  // Search in pages
  db.ref('pages').once('value', (snapshot) => {
    snapshot.forEach((child) => {
      const page = child.val();
      const content = page.content.toLowerCase();

      if (content.includes(query.toLowerCase())) {
        results.push({
          type: 'page',
          pageId: child.key,
          pageNumber: page.pageNumber,
          tajuk: page.tajuk,
          preview: extractPreview(content, query)
        });
      }
    });

    displaySearchResults(results);
  });
}

function goToPage(pageId) {
  // Switch to Buku tab
  switchTab('buku');

  // Load and scroll to page
  loadPage(pageId);

  // Update URL hash
  window.location.hash = pageId;
}

function nextPage() {
  const currentPageNum = getCurrentPageNumber();
  const nextPageId = `page_${String(currentPageNum + 1).padStart(3, '0')}`;
  goToPage(nextPageId);
}

function previousPage() {
  const currentPageNum = getCurrentPageNumber();
  if (currentPageNum > 1) {
    const prevPageId = `page_${String(currentPageNum - 1).padStart(3, '0')}`;
    goToPage(prevPageId);
  }
}
```

### E. Admin CRUD untuk Guru & Takwim

```javascript
// Tambah guru baru
async function tambahGuru(guruData) {
  const newGuruRef = db.ref('guru').push();
  await newGuruRef.set({
    ...guruData,
    editable: true,
    createdAt: firebase.database.ServerValue.TIMESTAMP
  });

  showNotification('✅ Guru berjaya ditambah!');
}

// Update guru
async function updateGuru(guruId, updates) {
  await db.ref(`guru/${guruId}`).update({
    ...updates,
    lastModified: firebase.database.ServerValue.TIMESTAMP
  });
}

// Tambah acara kalendar
async function tambahAcara(tarikh, acaraData) {
  await db.ref(`takwim/${tarikh}`).push(acaraData);
  renderCalendar();
}
```

---

## 6. CSS Styling

```css
:root {
  --primary: #6366f1;
  --primary-dark: #4f46e5;
  --pentadbiran: #f59e0b;
  --kurikulum: #10b981;
  --hem: #3b82f6;
  --kokurikulum: #ec4899;
  --highlight: #fef3c7;
  --text-primary: #1e293b;
  --text-secondary: #64748b;
  --bg-page: #ffffff;
  --border: #e2e8f0;
}

/* Page Display */
.page-display {
  max-width: 900px;
  margin: 0 auto;
  padding: 40px;
  background: var(--bg-page);
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
  min-height: 100vh;
}

.page-display h1 {
  color: var(--primary);
  border-bottom: 3px solid var(--primary);
  padding-bottom: 10px;
}

.page-display table {
  width: 100%;
  border-collapse: collapse;
  margin: 20px 0;
}

.page-display table th {
  background: var(--primary);
  color: white;
  padding: 12px;
}

.page-display table td {
  border: 1px solid var(--border);
  padding: 10px;
}

/* Edit Mode */
#quill-editor {
  min-height: 500px;
  background: white;
}

.edit-toolbar {
  background: #f1f5f9;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
}

.edit-toolbar button {
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
}

.edit-toolbar button:first-child {
  background: var(--kurikulum);
  color: white;
}

/* Admin-only elements */
.admin-only {
  display: none;
}

body.admin-mode .admin-only {
  display: inline-block;
}

/* Search highlight */
.search-highlight {
  background: var(--highlight);
  padding: 2px 4px;
  border-radius: 3px;
  font-weight: bold;
}
```

---

## 7. Authentication & Authorization

```javascript
// Admin login
async function loginAdmin(email, password) {
  try {
    await firebase.auth().signInWithEmailAndPassword(email, password);

    // Check if user is admin
    const user = firebase.auth().currentUser;
    const adminSnapshot = await db.ref(`admins/${user.uid}`).once('value');

    if (adminSnapshot.exists()) {
      document.body.classList.add('admin-mode');
      showNotification('✅ Login sebagai admin berjaya');
    } else {
      await firebase.auth().signOut();
      showNotification('❌ Anda bukan admin', 'error');
    }
  } catch (error) {
    showNotification('❌ Login gagal: ' + error.message, 'error');
  }
}

// Check admin role
function checkAdminRole() {
  const user = firebase.auth().currentUser;
  return user && document.body.classList.contains('admin-mode');
}

// Auth state listener
firebase.auth().onAuthStateChanged((user) => {
  if (user) {
    // Check admin role
    db.ref(`admins/${user.uid}`).once('value', (snapshot) => {
      if (snapshot.exists()) {
        document.body.classList.add('admin-mode');
      }
    });
  } else {
    document.body.classList.remove('admin-mode');
  }
});
```

---

## 8. Progressive Web App (PWA)

### manifest.json
```json
{
  "name": "Buku Pengurusan SK Sri Aman 2026",
  "short_name": "BPSKSA",
  "description": "Sistem Pengurusan Maklumat Sekolah - Editable Digital Document",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#6366f1",
  "icons": [
    {
      "src": "icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ]
}
```

### Service Worker (sw.js)
```javascript
const CACHE_NAME = 'bpsksa-v1';
const urlsToCache = [
  '/',
  '/index.html',
  '/styles.css',
  '/app.js',
  '/manifest.json'
];

// Cache Firebase data for offline
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      return response || fetch(event.request);
    })
  );
});
```

---

## 9. File Structure

```
bukupengurusansksa26-firebase/
├── index.html                    # Main SPA entry point
├── manifest.json                 # PWA manifest
├── sw.js                         # Service worker
│
├── css/
│   ├── styles.css               # Main styles
│   ├── page-display.css         # Page rendering styles
│   ├── edit-mode.css            # Editor styles
│   └── responsive.css           # Mobile responsive
│
├── js/
│   ├── firebase-config.js       # Firebase initialization
│   ├── auth.js                  # Authentication logic
│   ├── database.js              # CRUD operations
│   ├── page-renderer.js         # Page rendering engine
│   ├── editor.js                # Rich text editor
│   ├── search.js                # Search functionality
│   ├── navigation.js            # Page navigation
│   └── app.js                   # Main application
│
├── tools/
│   ├── pdf-extractor.js         # Script untuk extract PDF
│   └── firebase-uploader.js     # Bulk upload pages
│
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   └── favicon.ico
│
├── docs/
│   ├── MIGRATION_GUIDE.md       # Panduan migrate dari PDF
│   └── ADMIN_GUIDE.md           # Panduan untuk admin
│
└── README.md
```

---

## 10. Migration Checklist

### Phase 1: Data Extraction ✅
- [ ] Download PDF (buku-pengurusan-v2.pdf)
- [ ] Extract setiap page (87 pages total)
- [ ] Identify content type (text, table, image, mixed)
- [ ] Format content as HTML/JSON
- [ ] Extract & upload images to Firebase Storage
- [ ] Create page objects dengan proper structure

### Phase 2: Firebase Setup ✅
- [x] Create Firebase project (buku-pengurusan-sksa)
- [ ] Enable Realtime Database
- [ ] Enable Authentication (Email/Password provider)
- [ ] Enable Storage
- [ ] Setup security rules (read: public, write: admin only)
- [ ] Create admin users (tambah email admin di Authentication)
- [ ] Add admin UIDs to `/admins` node in database
- [ ] Upload page data (87 pages dari PDF)
- [ ] Verify data structure

### Phase 3: Application Development ✅
- [ ] Build page viewer
- [ ] Implement table of contents
- [ ] Add search functionality
- [ ] Implement edit mode dengan Quill.js
- [ ] Build guru directory
- [ ] Build calendar view
- [ ] Add admin authentication
- [ ] Implement CRUD operations
- [ ] Add real-time sync

### Phase 4: Testing ✅
- [ ] Test all 87 pages load correctly
- [ ] Test edit & save functionality
- [ ] Test search across all pages
- [ ] Test navigation (prev/next, TOC, links)
- [ ] Test admin auth & permissions
- [ ] Test on mobile devices
- [ ] Test offline mode
- [ ] Performance testing

### Phase 5: Deployment ✅
- [ ] Deploy to Firebase Hosting
- [ ] Configure custom domain (optional)
- [ ] Setup PWA install
- [ ] Train admin users
- [ ] Documentation
- [ ] Backup original PDF

---

## 11. Kelebihan Sistem Baru

| Feature | PDF Lama | Firebase Baru |
|---------|----------|---------------|
| Edit content | ❌ Perlu software PDF | ✅ Edit terus di browser |
| Update maklumat | ❌ Generate PDF baru | ✅ Update real-time |
| Search | ⚠️ Basic text search | ✅ Advanced search + filter |
| Multi-user | ❌ Conflict bila edit | ✅ Real-time sync |
| Mobile friendly | ⚠️ Zoom in/out susah | ✅ Responsive design |
| Offline access | ✅ Download PDF | ✅ PWA cache |
| File size | ⚠️ 3.6MB download | ✅ Load page by page |
| Version control | ❌ Manual backup | ✅ Firebase auto-backup |
| Collaboration | ❌ Satu-satu edit | ✅ Multiple admin edit |
| Accessibility | ⚠️ Screen reader limited | ✅ Semantic HTML |

---

## 12. Security Rules (Firebase)

```json
{
  "rules": {
    ".read": true,

    "pages": {
      ".write": "auth != null && root.child('admins').child(auth.uid).exists()"
    },

    "guru": {
      ".write": "auth != null && root.child('admins').child(auth.uid).exists()"
    },

    "takwim": {
      ".write": "auth != null && root.child('admins').child(auth.uid).exists()"
    },

    "admins": {
      ".read": "auth != null",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() === 'superadmin'"
    }
  }
}
```

---

## Output Akhir

Aplikasi yang:
1. ✅ Setiap page PDF ditukar kepada editable content
2. ✅ Admin boleh edit mana-mana page terus di browser
3. ✅ Changes saved real-time ke Firebase
4. ✅ Viewer boleh navigate seperti PDF viewer
5. ✅ Search berfungsi merentasi semua pages
6. ✅ Mobile-friendly & PWA
7. ✅ Backup automatic di Firebase
8. ✅ Tiada perlu generate PDF baru bila ada update

**Sistem ini mengubah static PDF kepada living document yang boleh berkembang dan dikemaskini dengan mudah!**
