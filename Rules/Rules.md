# Rules Pembuatan JSON

Panduan dan aturan yang harus diikuti saat membuat file JSON di dalam proyek ini. Struktur JSON mengacu pada standar FHIR namun **disederhanakan** agar lebih ringkas dan mudah dikonsumsi oleh aplikasi.

> [!NOTE]
> Semua file JSON di folder `Implementation/` harus mengikuti aturan ini. Lihat juga [`README.md`](../README.md) untuk konteks dan cara penggunaan repository.

---

## 1. Array `component[]` Sebagai Root Utama

Semua data item di dalam file JSON **wajib dibungkus dalam array `component[]`** sebagai struktur root utama. Array ini menjadi satu-satunya wadah untuk menampung daftar item data (pengukuran, diagnosis, obat, zat alergi, dll.).

> [!IMPORTANT]
> Tidak boleh ada item data yang ditempatkan langsung di root object tanpa dibungkus `component[]`. Field di luar `component[]` hanya boleh berupa metadata pendukung seperti `resourceType`, `category`, `criticality`, atau field deskripsi.

**Struktur dasar setiap file JSON:**
```json
{
  "component": [
    { /* item 1 */ },
    { /* item 2 */ }
  ]
}
```

**Field yang boleh berada di luar `component[]` (level root):**

| Field                  | Keterangan                                   |
|------------------------|----------------------------------------------|
| `resourceType`         | Identifikasi resource (jika multi-resource)  |
| `category`             | Kategori data (misal: `"food"`)              |
| `criticality`          | Tingkat kritis (misal: `"high"`)             |
| `componentDescription` | Deskripsi naratif untuk `component[]`        |
| `reaction[]`           | Array terpisah (khusus AllergyIntolerance)   |
| `reactionDescription`  | Deskripsi naratif untuk `reaction[]`         |

---

## 2. Maksimal 2 Level Kedalaman (Nesting)

Struktur JSON yang dibuat **tidak boleh melebihi 2 level kedalaman** dari root object.

### Penghitungan Level

| Level | Keterangan                              | Contoh Path                              |
|-------|-----------------------------------------|------------------------------------------|
| 0     | Root object `{}`                        | `{}`                                     |
| 1     | Properti langsung dari root             | `component[]`, `category`, `reaction[]`  |
| 2     | Properti di dalam item `component[]`    | `code`, `valueQuantity`, `system`        |

> [!IMPORTANT]
> Level 2 adalah batas maksimal. Tidak boleh ada objek atau array bersarang yang membuat level 3 ke atas.

### Implikasi: Flatten `coding[]`

Pada FHIR standar, `CodeableConcept` memiliki array `coding[]` yang membuat **3 level kedalaman**. Array ini harus di-**flatten** langsung ke objek parent-nya.

**❌ Tidak boleh (3 level):**
```json
{
  "code": {
    "coding": [
      {
        "system": "http://loinc.org",
        "code": "29463-7",
        "display": "Body weight"
      }
    ],
    "text": "Berat Badan"
  }
}
```

**✅ Benar (2 level):**
```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "29463-7",
    "display": "Body weight",
    "text": "Berat Badan"
  }
}
```

---

## 3. Hanya Simpan Field Data Inti

File JSON **hanya berisi data yang benar-benar diperlukan**. Field metadata resource FHIR berikut **tidak perlu disertakan** karena secara default disimpan sebagai dedicated column pada masing-masing tabel:

| Field yang Dihapus       | Alasan                                          |
|--------------------------|--------------------------------------------------|
| `id`                     | ID dikelola oleh sistem/database                 |
| `text` (Narrative)       | Representasi HTML tidak diperlukan               |
| `identifier`             | Identifier dikelola oleh sistem                  |
| `status`                 | Status dikelola oleh workflow aplikasi            |
| `subject`                | Relasi pasien dikelola oleh aplikasi             |
| `encounter`              | Relasi encounter dikelola oleh aplikasi          |
| `effectiveDateTime`      | Timestamp dikelola oleh sistem                   |
| `issued`                 | Timestamp dikelola oleh sistem                   |
| `performer`              | Relasi performer dikelola oleh aplikasi          |
| `note`                   | Catatan tambahan bersifat opsional               |
| `interpretation`         | Interpretasi bersifat opsional                   |
| `referenceRange`         | Rentang referensi bersifat opsional              |
| `bodySite`               | Lokasi tubuh bersifat opsional                   |

### Field yang Tetap Disertakan (Kondisional)

Beberapa field metadata **tetap digunakan** pada kasus tertentu:

| Field              | Kapan Disertakan                                         | Contoh                                  |
|--------------------|----------------------------------------------------------|-----------------------------------------|
| `resourceType`     | Wajib pada folder multi-resource (lebih dari 1 file JSON) | `Condition.json`, `FamilyMemberHistory.json` |
| `category`         | Jika diperlukan untuk membedakan jenis data              | `"category": "food"` di AllergyIntolerance  |
| `criticality`      | Jika relevan secara klinis                               | `"criticality": "high"` di AllergyIntolerance |
| `clinicalStatus`   | Jika diperlukan untuk konteks klinis                     | `"clinicalStatus": "active"` di Condition   |

> [!TIP]
> Aturan praktis: jika sebuah field **berpengaruh pada cara data ditampilkan atau diproses** oleh aplikasi, field tersebut boleh disertakan.

---

## 4. Hapus Field Redundan di Dalam Value

Pada tipe data `Quantity`, field `system` (URI terminologi satuan) **tidak perlu disertakan** jika `code` satuan sudah cukup jelas.

**❌ Tidak perlu:**
```json
{
  "valueQuantity": {
    "value": 70,
    "unit": "kg",
    "system": "http://unitsofmeasure.org",
    "code": "kg"
  }
}
```

**✅ Cukup:**
```json
{
  "valueQuantity": {
    "value": 70,
    "unit": "kg",
    "code": "kg"
  }
}
```

---

## 5. Konvensi Penamaan

### Nama File

- Nama file menggunakan format: **`{ResourceType}.json`**
  - Contoh: `Observation.json`, `Condition.json`, `AllergyIntolerance.json`
- Satu folder dapat berisi **lebih dari satu file JSON** jika modul menggunakan beberapa resource FHIR
  - Contoh: folder `RiwayatPenyakit/` berisi `Condition.json`, `FamilyMemberHistory.json`, `MedicationStatement.json`

### Bahasa Indonesia

- Setiap `code` wajib memiliki field **`text`** dalam **Bahasa Indonesia**
- Field `display` menggunakan **bahasa Inggris** sesuai standar terminologi asli

### Suffix `Description`

- Field deskripsi naratif menggunakan suffix **`Description`** pada level root
  - `componentDescription` — deskripsi ringkas untuk array `component[]`
  - `reactionDescription` — deskripsi ringkas untuk array `reaction[]`
- Field `description` juga bisa digunakan **di dalam item** `component[]` untuk deskripsi per-item
  - Contoh: pada `MedicationStatement` → `"description": "isi deskripsi riwayat pengobatan"`

---

## 6. Pola Struktur Data

### 6a. Pola Component dengan Code Object (Observation)

Untuk resource **Observation** dengan banyak item pengukuran, setiap item menggunakan pola `component[]` dengan objek `code` tersarang:

- **`code`** — Identifikasi jenis pengukuran (wajib):
  - `system` — URI terminologi (misal: `http://loinc.org`)
  - `code` — Kode standar
  - `display` — Nama tampilan dalam bahasa Inggris
  - `text` — Nama tampilan dalam Bahasa Indonesia

- **`valueQuantity`** — Untuk nilai numerik dengan satuan:
  - `value` — Nilai angka
  - `unit` — Label satuan yang ditampilkan
  - `code` — Kode satuan standar

- **`valueCodeableConcept`** — Untuk nilai berupa kode/konsep kualitatif:
  - `system` — URI terminologi (jika ada, misal: `http://snomed.info/sct`)
  - `code` — Kode standar
  - `display` — Nama tampilan dalam bahasa Inggris
  - `text` — Nama tampilan dalam Bahasa Indonesia

**Contoh:**
```json
{
  "component": [
    {
      "code": {
        "system": "http://loinc.org",
        "code": "29463-7",
        "display": "Body weight",
        "text": "Berat Badan"
      },
      "valueQuantity": {
        "value": 70,
        "unit": "kg",
        "code": "kg"
      }
    }
  ]
}
```

### 6b. Pola Component Flat (Condition, FamilyMemberHistory, MedicationStatement)

Untuk resource yang item-nya berupa **daftar kode** tanpa nilai ukur, `component[]` menggunakan pola flat — properti kode langsung di dalam item tanpa objek `code` tersarang:

- `system` — URI terminologi
- `code` — Kode standar
- `display` — Nama tampilan (Inggris)
- `text` — Nama tampilan (Indonesia)
- Field tambahan per konteks: `clinicalStatus`, `description`

**Contoh (Condition):**
```json
{
  "resourceType": "Condition",
  "component": [
    {
      "clinicalStatus": "active",
      "system": "http://snomed.info/sct",
      "code": "38341003",
      "display": "Hypertensive disorder",
      "text": "Hipertensi"
    }
  ]
}
```

**Contoh (MedicationStatement):**
```json
{
  "resourceType": "MedicationStatement",
  "component": [
    {
      "system": "http://sys-ids.kemkes.go.id/kfa",
      "code": "93001295",
      "display": "Metformin 500 mg tablet",
      "text": "Metformin 500 mg tablet",
      "description": "isi deskripsi riwayat pengobatan"
    }
  ]
}
```

### 6c. Pola Reaction (AllergyIntolerance)

Resource `AllergyIntolerance` menggunakan dua array terpisah:

- **`component[]`** — Daftar zat penyebab alergi
- **`reaction[]`** — Daftar manifestasi/reaksi alergi

Keduanya menggunakan pola flat (tanpa objek `code` tersarang) dan dilengkapi dengan field deskripsi naratif di level root.

**Contoh:**
```json
{
  "category": "food",
  "criticality": "high",
  "component": [
    {
      "system": "http://snomed.info/sct",
      "code": "430747003",
      "display": "Acetylcarnitine",
      "text": "Asetilkarinitin"
    }
  ],
  "componentDescription": "Alergi terhadap asetilkarinitin",
  "reaction": [
    {
      "system": "http://snomed.info/sct",
      "code": "31996006",
      "display": "Vasculitis",
      "text": "Vaskulitis"
    }
  ],
  "reactionDescription": "Pasien mengalami reaksi berupa vaskulitis"
}
```

### 6d. Pola Khusus: GCS Sub-Component

Untuk **GCS (Glasgow Coma Scale)**, sub-skor ditampilkan sebagai properti langsung di dalam item component (bukan array bersarang), mempertahankan batas 2 level:

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "9269-2",
    "display": "Glasgow coma score total",
    "text": "GCS"
  },
  "valueQuantity": {
    "value": 15,
    "unit": "Skor",
    "code": "Representasi total skor"
  },
  "eye": {
    "code": "9267-6",
    "value": 2,
    "display": "Mata terbuka"
  },
  "verbal": {
    "code": "9268-4",
    "value": 5,
    "display": "Respon verbal"
  },
  "motor": {
    "code": "9266-8",
    "value": 6,
    "display": "Respon motorik"
  }
}
```

### 6e. Pola Device Reference

Untuk item yang merujuk perangkat medis (bukan nilai ukur atau kode), gunakan objek `device`:

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "76010-8",
    "display": "Oxygen assistance type",
    "text": "Alat Bantu Oksigenasi"
  },
  "device": {
    "display": "Nasal kanul"
  }
}
```

---

## 7. Sistem Terminologi

Gunakan sistem terminologi standar sesuai konteks:

| Terminologi | URI Sistem                              | Digunakan Untuk                              |
|-------------|-----------------------------------------|----------------------------------------------|
| **LOINC**   | `http://loinc.org`                      | Kode observasi & pengukuran (TTV, lab)       |
| **SNOMED CT** | `http://snomed.info/sct`              | Kode klinis (diagnosis, penyakit, reaksi, alergi) |
| **KFA**     | `http://sys-ids.kemkes.go.id/kfa`       | Kode Farmasi Indonesia (obat/pengobatan)     |
| **HL7 FHIR** | `http://hl7.org/fhir/...`             | Value set FHIR (kategori alergi, kritikalitas) |

---

## Checklist Sebelum Submit

- [ ] Semua item data dibungkus dalam array `component[]`
- [ ] Tidak ada nesting lebih dari 2 level
- [ ] Tidak ada array `coding[]` — sudah di-flatten
- [ ] Field metadata resource tidak disertakan (kecuali `resourceType` jika multi-resource)
- [ ] `system` pada `valueQuantity` tidak disertakan
- [ ] Setiap `code` memiliki `text` dalam Bahasa Indonesia
- [ ] Nilai kuantitatif menggunakan `valueQuantity`, nilai kualitatif menggunakan `valueCodeableConcept`
- [ ] Nama file menggunakan format `{ResourceType}.json`
- [ ] Dokumentasi `.md` sudah dibuat/diperbarui di `Documentation/`
- [ ] Deskripsi naratif menggunakan suffix `Description` (jika ada)
