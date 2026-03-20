# RH
# 📁 Sistem Data Personil - Kodim 1609

Sistem ini menggunakan **Google Apps Script** dengan arsitektur universal yang memungkinkan penambahan sheet baru secara otomatis tanpa perlu mengubah kode inti.

---

## 🚀 Fitur Utama

| Fitur | Keterangan |
|-------|------------|
| ✅ **Universal Delete** | Menghapus semua data terkait ID dari semua sheet secara otomatis |
| ✅ **Universal Insert** | Menambahkan data ke sheet mana pun dengan format yang sudah ditentukan |
| ✅ **Auto Reset** | Reset semua sheet yang terdaftar dalam satu perintah |
| ✅ **Mudah Dikembangkan** | Cukup tambah 3-4 baris kode untuk sheet baru |
| ✅ **Tidak Ada Duplikasi** | Setiap NRP hanya memiliki 1 baris di setiap sheet |

---

## 📂 Struktur Sheet Saat Ini

| Nama Sheet | Kolom yang Digunakan |
|------------|---------------------|
| **DATA_PERSONEL** | ID, Nama, Pangkat/NRP, Jabatan, TTL, Umur, Alamat, Nama Istri, TTL Istri, Umur Istri, Akta Nikah, NRP_Asli, Timestamp |
| **DATA_ANAK** | ID_PERSONEL, No, Nama Anak, TTL Anak, Umur Anak, Timestamp |
| **RIWAYAT_PANGKAT** | ID_PERSONEL, No, Pangkat, No SK, Tanggal, TMT, Timestamp |
| **RIWAYAT_JABATAN** | ID_PERSONEL, No, Jabatan, No SK, Tanggal, TMT, Timestamp |

---

## 🔧 Cara Menambah Sheet Baru

Jika Anda ingin menambah sheet baru di masa depan (misal: `DATA_PENDIDIKAN`), ikuti langkah berikut:

### 1. Daftarkan Sheet Baru di `SHEET_NAMES`

```javascript
const SHEET_NAMES = {
  PERSONEL: "DATA_PERSONEL",
  ANAK: "DATA_ANAK",
  PANGKAT: "RIWAYAT_PANGKAT",
  JABATAN: "RIWAYAT_JABATAN",
  // ===== TAMBAH SHEET BARU DI SINI =====
  PENDIDIKAN: "DATA_PENDIDIKAN",
};

const SHEET_HEADERS = {
  DATA_PERSONEL: ["ID", "Nama", "Pangkat/NRP", "Jabatan", "TTL", "Umur", "Alamat", "Nama Istri", "TTL Istri", "Umur Istri", "Akta Nikah", "NRP_Asli", "Timestamp"],
  DATA_ANAK: ["ID_PERSONEL", "No", "Nama Anak", "TTL Anak", "Umur Anak", "Timestamp"],
  RIWAYAT_PANGKAT: ["ID_PERSONEL", "No", "Pangkat", "No SK", "Tanggal", "TMT", "Timestamp"],
  RIWAYAT_JABATAN: ["ID_PERSONEL", "No", "Jabatan", "No SK", "Tanggal", "TMT", "Timestamp"],
  // ===== TAMBAH HEADER SHEET BARU DI SINI =====
  DATA_PENDIDIKAN: ["ID_PERSONEL", "No", "Jenjang", "Tahun", "Keterangan", "Timestamp"],
};

// Data PENDIDIKAN
addDataToSheet(ss, "PENDIDIKAN", id, data.pendidikan_list, (item, idx) => {
  if (!item || item.length < 4) return null;
  return [id, idx + 1, item[0] || "", item[1] || "", item[2] || "", item[3] || "", new Date()];
});

// Ambil data pendidikan
const shPendidikan = ss.getSheetByName(SHEET_NAMES.PENDIDIKAN);
if (shPendidikan) {
  const dataPendidikan = shPendidikan.getDataRange().getValues();
  for (let i = 1; i < dataPendidikan.length; i++) {
    if (dataPendidikan[i][0] == id) {
      result.pendidikan_list.push([
        dataPendidikan[i][2] || "",
        dataPendidikan[i][3] || "",
        dataPendidikan[i][4] || ""
      ]);
    }
  }
}

// Di bagian data yang dikirim ke server
pendidikan_list: [["SMA", "1998", "Negeri 1"], ["D3", "2002", "Universitas X"]]

{
  nama: "Made Saputra",
  pangkat: "Kopda/31000305130488",
  jabatan: "Babinsa",
  ttl: "Singaraja, 21-04-1979",
  umur: "46 tahun",
  alamat: "Desa Pengastulan",
  istri: "Komang dika ariani",
  ttl_istri: "Buleleng, 21-04-1988",
  umur_istri: "38 tahun",
  nikah: "sik/298/V/2020",
  anak: [["Budi", "Denpasar, 10-05-2015"], ["Sari", "Denpasar, 20-08-2018"]],
  pangkat_list: [["Letda", "SK-001", "01-01-2020", "01-02-2020"]],
  jabatan_list: [["Danramil", "SK-100", "01-03-2020", "01-04-2020"]]
}

1. HTML mengirim data ke doPost()
                ↓
2. Ekstrak NRP dari field pangkat
                ↓
3. UPDATE atau CREATE data di DATA_PERSONEL
                ↓
4. HAPUS semua data terkait ID dari sheet lain
                ↓
5. TAMBAH data baru ke semua sheet
                ↓
6. Buat folder Drive (jika belum ada)
                ↓
7. Kirim response ke HTML

⚙️ Cara Deploy Ulang Setelah Menambah Sheet
Buka Google Apps Script

Copy kode universal yang sudah ditambahkan sheet baru

Simpan (Ctrl+S)

Jalankan fungsi runSetup() (untuk reset semua sheet)

Klik Deploy → New deployment

Pilih type: Web App

Execute as: Me

Who has access: Anyone

Klik Deploy

Copy URL baru

Update URL di file HTML

Upload HTML ke GitHub

📝 Catatan Penting
Hal	Keterangan
HTML tidak perlu diubah	Selama format data yang dikirim sama, HTML tetap berfungsi
ID Personel	Menggunakan NRP yang diekstrak dari field pangkat
Kolom ID_PERSONEL	Harus selalu berada di kolom pertama setiap sheet (kecuali DATA_PERSONEL)
Timestamp	Otomatis ditambahkan setiap kali simpan
🆘 Troubleshooting
Masalah	Solusi
Data masih bertumpuk	Pastikan sudah deploy ulang dengan kode universal dan jalankan runSetup()
Sheet baru tidak muncul	Cek SHEET_NAMES dan SHEET_HEADERS sudah ditambahkan
HTML error	Pastikan URL Web App sudah diupdate dengan URL hasil deploy terbaru
Data tidak tersimpan	Cek log eksekusi di Apps Script (View → Logs)

// ================= KONFIGURASI =================
const SHEET_ID = "1Sajjok-cjpxhv9sgwUtPbUipQxUoJkvT2_T-Fk79YIQ";
const FOLDER_ID = "1kNM6LeZJLNkOEr5LbT2gCwWgxRsr9rZv";

// ================= DAFTAR SHEET =================
const SHEET_NAMES = {
  PERSONEL: "DATA_PERSONEL",
  ANAK: "DATA_ANAK",
  PANGKAT: "RIWAYAT_PANGKAT",
  JABATAN: "RIWAYAT_JABATAN"
};

// ================= KONFIGURASI HEADER PER SHEET =================
const SHEET_HEADERS = {
  DATA_PERSONEL: ["ID", "Nama", "Pangkat/NRP", "Jabatan", "TTL", "Umur", "Alamat", "Nama Istri", "TTL Istri", "Umur Istri", "Akta Nikah", "NRP_Asli", "Timestamp"],
  DATA_ANAK: ["ID_PERSONEL", "No", "Nama Anak", "TTL Anak", "Umur Anak", "Timestamp"],
  RIWAYAT_PANGKAT: ["ID_PERSONEL", "No", "Pangkat", "No SK", "Tanggal", "TMT", "Timestamp"],
  RIWAYAT_JABATAN: ["ID_PERSONEL", "No", "Jabatan", "No SK", "Tanggal", "TMT", "Timestamp"]
};

// ================= FUNGSI EKSTRAK NRP =================
function extractNRP(pangkatText) {
  if (!pangkatText) return null;
  let cleanText = pangkatText.trim();
  
  if (cleanText.includes("/")) {
    let lastPart = cleanText.split("/").pop().trim();
    let match = lastPart.match(/\d+/);
    if (match) return match[0];
  }
  
  let match = cleanText.match(/\d+/);
  return match ? match[0] : null;
}

// ================= HAPUS SEMUA DATA LAMA =================
function deleteAllRelatedData(ss, id) {
  for (let sheetName in SHEET_NAMES) {
    if (sheetName === "PERSONEL") continue;
    
    const sheet = ss.getSheetByName(SHEET_NAMES[sheetName]);
    if (!sheet) continue;
    
    const data = sheet.getDataRange().getValues();
    for (let i = data.length - 1; i >= 1; i--) {
      if (data[i][0] == id) {
        sheet.deleteRow(i + 1);
      }
    }
  }
}

// ================= UPDATE ATAU CREATE DATA PERSONEL =================
function updateOrCreatePersonel(ss, id, data) {
  const shPersonel = ss.getSheetByName(SHEET_NAMES.PERSONEL);
  const dataPersonel = shPersonel.getDataRange().getValues();
  let existingRow = -1;
  
  for (let i = 1; i < dataPersonel.length; i++) {
    if (dataPersonel[i][0] == id) {
      existingRow = i + 1;
      break;
    }
  }
  
  if (existingRow > 0) {
    shPersonel.getRange(existingRow, 1, 1, 13).setValues([[
      id,
      data.nama || "",
      data.pangkat || "",
      data.jabatan || "",
      data.ttl || "",
      data.umur || "",
      data.alamat || "",
      data.istri || "",
      data.ttl_istri || "",
      data.umur_istri || "",
      data.nikah || "",
      data.nrp || "",
      new Date()
    ]]);
    return "UPDATE";
  } else {
    shPersonel.appendRow([
      id,
      data.nama || "",
      data.pangkat || "",
      data.jabatan || "",
      data.ttl || "",
      data.umur || "",
      data.alamat || "",
      data.istri || "",
      data.ttl_istri || "",
      data.umur_istri || "",
      data.nikah || "",
      data.nrp || "",
      new Date()
    ]);
    return "CREATE";
  }
}

// ================= TAMBAH DATA KE SHEET =================
function addDataToSheet(ss, sheetKey, id, dataList, dataMapper) {
  if (!dataList || dataList.length === 0) return;
  
  const sheetName = SHEET_NAMES[sheetKey];
  const headers = SHEET_HEADERS[sheetName];
  if (!headers) return;
  
  const sheet = getOrCreateSheet(sheetName, headers);
  
  dataList.forEach((item, index) => {
    const rowData = dataMapper(item, index, id);
    if (rowData) {
      sheet.appendRow(rowData);
    }
  });
}

// ================= FUNGSI UTAMA =================
function doPost(e) {
  try {
    const ss = SpreadsheetApp.openById(SHEET_ID);
    const data = JSON.parse(e.postData.contents);
    
    let nrp = extractNRP(data.pangkat);
    let id = nrp ? nrp : new Date().getTime().toString();
    
    const action = updateOrCreatePersonel(ss, id, {
      nama: data.nama,
      pangkat: data.pangkat,
      jabatan: data.jabatan,
      ttl: data.ttl,
      umur: data.umur,
      alamat: data.alamat,
      istri: data.istri,
      ttl_istri: data.ttl_istri,
      umur_istri: data.umur_istri,
      nikah: data.nikah,
      nrp: nrp
    });
    
    deleteAllRelatedData(ss, id);
    
    addDataToSheet(ss, "ANAK", id, data.anak, (item, idx) => {
      if (!item || item.length < 2) return null;
      return [id, idx + 1, item[0] || "", item[1] || "", hitungUmurDariString(item[1] || ""), new Date()];
    });
    
    addDataToSheet(ss, "PANGKAT", id, data.pangkat_list, (item, idx) => {
      if (!item || item.length < 4) return null;
      return [id, idx + 1, item[0] || "", item[1] || "", item[2] || "", item[3] || "", new Date()];
    });
    
    addDataToSheet(ss, "JABATAN", id, data.jabatan_list, (item, idx) => {
      if (!item || item.length < 4) return null;
      return [id, idx + 1, item[0] || "", item[1] || "", item[2] || "", item[3] || "", new Date()];
    });
    
    buatFolderPersonil(id, data.nama || "Personil");
    
    return ContentService
      .createTextOutput(JSON.stringify({ 
        success: true, 
        message: "Data berhasil " + (action === "UPDATE" ? "diganti/diperbarui" : "disimpan"), 
        id: id,
        action: action
      }))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ 
        success: false, 
        error: error.toString() 
      }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// ================= FUNGSI GET =================
function doGet(e) {
  try {
    const ss = SpreadsheetApp.openById(SHEET_ID);
    const params = e ? e.parameter : {};
    let requestId = params.id || "latest";
    
    if (requestId !== "latest") {
      requestId = requestId.trim();
    }
    
    if (requestId === "latest") {
      return getLatestData(ss);
    } else {
      return getDataById(ss, requestId);
    }
    
  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ 
        success: false, 
        error: error.toString() 
      }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// ================= FUNGSI FOLDER =================
function getFolderUtama() {
  try {
    return DriveApp.getFolderById(FOLDER_ID);
  } catch (error) {
    const rootFolder = DriveApp.getRootFolder();
    return rootFolder.createFolder("DATA_PERSONIL_KODIM_1609");
  }
}

function buatFolderPersonil(id, namaPersonil) {
  try {
    const folderUtama = getFolderUtama();
    const namaBersih = namaPersonil.replace(/[^a-zA-Z0-9]/g, "_");
    const namaFolder = `${id}_${namaBersih}`;
    
    const folders = folderUtama.getFoldersByName(namaFolder);
    if (folders.hasNext()) {
      return folders.next();
    }
    
    const folderBaru = folderUtama.createFolder(namaFolder);
    const readmeContent = `DATA PERSONIL\nID/NRP: ${id}\nNama: ${namaPersonil}\nDibuat: ${new Date().toLocaleString('id-ID')}`;
    folderBaru.createFile(`README_${id}.txt`, readmeContent);
    return folderBaru;
  } catch (error) {
    return null;
  }
}

// ================= FUNGSI AMBIL DATA =================
function getLatestData(ss) {
  const shPersonel = ss.getSheetByName(SHEET_NAMES.PERSONEL);
  if (!shPersonel) {
    return ContentService.createTextOutput(JSON.stringify({})).setMimeType(ContentService.MimeType.JSON);
  }
  
  const dataPersonel = shPersonel.getDataRange().getValues();
  if (dataPersonel.length <= 1) {
    return ContentService.createTextOutput(JSON.stringify({})).setMimeType(ContentService.MimeType.JSON);
  }
  
  const lastRow = dataPersonel[dataPersonel.length - 1];
  const id = lastRow[0];
  
  return getCompleteData(ss, id, lastRow);
}

function getDataById(ss, id) {
  const shPersonel = ss.getSheetByName(SHEET_NAMES.PERSONEL);
  if (!shPersonel) {
    return ContentService.createTextOutput(JSON.stringify({ error: "Sheet PERSONEL tidak ditemukan" })).setMimeType(ContentService.MimeType.JSON);
  }
  
  const dataPersonel = shPersonel.getDataRange().getValues();
  let personelRow = null;
  let foundId = null;
  
  for (let i = 1; i < dataPersonel.length; i++) {
    if (dataPersonel[i][0] == id || dataPersonel[i][11] == id) {
      personelRow = dataPersonel[i];
      foundId = dataPersonel[i][0];
      break;
    }
  }
  
  if (!personelRow) {
    return ContentService.createTextOutput(JSON.stringify({ error: "ID/NRP tidak ditemukan" })).setMimeType(ContentService.MimeType.JSON);
  }
  
  return getCompleteData(ss, foundId, personelRow);
}

function getCompleteData(ss, id, personelRow) {
  const result = {
    nama: personelRow[1] || "",
    pangkat: personelRow[2] || "",
    jabatan: personelRow[3] || "",
    ttl: personelRow[4] || "",
    umur: personelRow[5] || "",
    alamat: personelRow[6] || "",
    istri: personelRow[7] || "",
    ttl_istri: personelRow[8] || "",
    umur_istri: personelRow[9] || "",
    nikah: personelRow[10] || "",
    anak: [],
    pangkat_list: [],
    jabatan_list: []
  };
  
  const shAnak = ss.getSheetByName(SHEET_NAMES.ANAK);
  if (shAnak) {
    const dataAnak = shAnak.getDataRange().getValues();
    for (let i = 1; i < dataAnak.length; i++) {
      if (dataAnak[i][0] == id) {
        result.anak.push([dataAnak[i][2] || "", dataAnak[i][3] || ""]);
      }
    }
  }
  
  const shPangkat = ss.getSheetByName(SHEET_NAMES.PANGKAT);
  if (shPangkat) {
    const dataPangkat = shPangkat.getDataRange().getValues();
    for (let i = 1; i < dataPangkat.length; i++) {
      if (dataPangkat[i][0] == id) {
        result.pangkat_list.push([dataPangkat[i][2] || "", dataPangkat[i][3] || "", dataPangkat[i][4] || "", dataPangkat[i][5] || ""]);
      }
    }
  }
  
  const shJabatan = ss.getSheetByName(SHEET_NAMES.JABATAN);
  if (shJabatan) {
    const dataJabatan = shJabatan.getDataRange().getValues();
    for (let i = 1; i < dataJabatan.length; i++) {
      if (dataJabatan[i][0] == id) {
        result.jabatan_list.push([dataJabatan[i][2] || "", dataJabatan[i][3] || "", dataJabatan[i][4] || "", dataJabatan[i][5] || ""]);
      }
    }
  }
  
  return ContentService.createTextOutput(JSON.stringify(result)).setMimeType(ContentService.MimeType.JSON);
}

// ================= FUNGSI BANTUAN =================
function getOrCreateSheet(sheetName, headers) {
  const ss = SpreadsheetApp.openById(SHEET_ID);
  let sheet = ss.getSheetByName(sheetName);
  
  if (!sheet) {
    sheet = ss.insertSheet(sheetName);
    if (headers && headers.length) {
      sheet.getRange(1, 1, 1, headers.length).setValues([headers]);
      sheet.getRange(1, 1, 1, headers.length).setFontWeight("bold").setBackground("#f0f0f0");
      sheet.setFrozenRows(1);
    }
  }
  return sheet;
}

function hitungUmurDariString(ttlString) {
  if (!ttlString) return "";
  const match = ttlString.match(/\b(19|20)\d{2}\b/);
  return match ? (new Date().getFullYear() - parseInt(match[0]) + " tahun") : "";
}

// ================= FUNGSI RESET SHEET =================
function resetAllSheets() {
  try {
    const ss = SpreadsheetApp.openById(SHEET_ID);
    
    for (let key in SHEET_NAMES) {
      const sheet = ss.getSheetByName(SHEET_NAMES[key]);
      if (sheet) {
        ss.deleteSheet(sheet);
      }
    }
    
    for (let key in SHEET_NAMES) {
      const sheetName = SHEET_NAMES[key];
      const headers = SHEET_HEADERS[sheetName];
      if (headers) {
        getOrCreateSheet(sheetName, headers);
      }
    }
    
    return "✅ Semua sheet berhasil direset!";
  } catch (error) {
    return "❌ Error: " + error.toString();
  }
}

function runSetup() {
  Logger.log("=== MEMULAI SETUP ===");
  Logger.log(resetAllSheets());
  Logger.log("=== SETUP SELESAI ===");
}
