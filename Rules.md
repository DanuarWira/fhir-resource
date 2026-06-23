# Rules Pembuatan JSON

Panduan dan aturan yang harus diikuti saat membuat file JSON di dalam proyek ini. Struktur JSON mengacu pada standar FHIR namun **disederhanakan** agar lebih ringkas dan mudah dikonsumsi oleh aplikasi.

---

## 1. Maksimal 2 Level Kedalaman (Nesting)

Struktur JSON yang dibuat **tidak boleh melebihi 2 level kedalaman** dari root object.

### Penghitungan Level

| Level | Keterangan                       | Contoh Path                    |
|-------|----------------------------------|--------------------------------|
| 0     | Root object `{}`                 | `{}`                           |
| 1     | Properti langsung dari root      | `component[]`, `code`          |
| 2     | Properti di dalam level 1        | `code.system`, `code.display`  |

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

## 2. Hanya Simpan Field Data Inti

File JSON **hanya berisi data yang benar-benar diperlukan**. Field metadata resource FHIR berikut **tidak perlu disertakan** kecuali memang dibutuhkan secara eksplisit:

| Field yang Dihapus       | Alasan                                          |
|--------------------------|--------------------------------------------------|
| `resourceType`           | Sudah teridentifikasi dari nama file/folder      |
| `id`                     | ID dikelola oleh sistem/database                 |
| `text` (Narrative)       | Representasi HTML tidak diperlukan               |
| `identifier`             | Identifier dikelola oleh sistem                  |
| `status`                 | Status dikelola oleh workflow aplikasi            |
| `category`               | Sudah teridentifikasi dari konteks               |
| `subject`                | Relasi pasien dikelola oleh aplikasi             |
| `encounter`              | Relasi encounter dikelola oleh aplikasi          |
| `effectiveDateTime`      | Timestamp dikelola oleh sistem                   |
| `issued`                 | Timestamp dikelola oleh sistem                   |
| `performer`              | Relasi performer dikelola oleh aplikasi          |
| `note`                   | Catatan tambahan bersifat opsional               |
| `interpretation`         | Interpretasi bersifat opsional                   |
| `referenceRange`         | Rentang referensi bersifat opsional              |
| `bodySite`               | Lokasi tubuh bersifat opsional                   |

---

## 3. Hapus Field Redundan di Dalam Value

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

## 4. Struktur Component

Setiap item pengukuran menggunakan pola `component[]` yang berisi:

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

---

## 5. Contoh Struktur yang Benar

Berikut contoh JSON yang mengikuti semua rules di atas:

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
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8884-9",
        "display": "Heart rate rhythm",
        "text": "Irama Jantung"
      },
      "valueCodeableConcept": {
        "code": "271636001",
        "display": "Regular pulse",
        "text": "Teratur"
      }
    }
  ]
}
```

---

## Checklist Sebelum Submit

- [ ] Tidak ada nesting lebih dari 2 level
- [ ] Tidak ada array `coding[]` — sudah di-flatten
- [ ] Field metadata resource (resourceType, id, status, dll.) tidak disertakan
- [ ] `system` pada `valueQuantity` tidak disertakan
- [ ] Setiap `code` memiliki `text` dalam Bahasa Indonesia
- [ ] Nilai kuantitatif menggunakan `valueQuantity`, nilai kualitatif menggunakan `valueCodeableConcept`
