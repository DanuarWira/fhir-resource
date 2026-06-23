# AllergyIntolerance — Riwayat Alergi

Dokumen ini menjelaskan struktur JSON yang digunakan untuk mencatat **riwayat alergi** pasien rawat inap. JSON ini merupakan representasi sederhana (*simplified*) dari resource FHIR [`AllergyIntolerance`](https://hl7.org/fhir/allergyintolerance.html) yang disesuaikan untuk kebutuhan integrasi sistem informasi rumah sakit.

---

## Struktur Lengkap

```json
{
  "category": "food",
  "criticality": "high",
  "code": [
    {
      "system": "http://snomed.info/sct",
      "code": "430747003",
      "display": "Acetylcarnitine",
      "text": "Asetilkarinitin"
    },
    {
      "system": "http://snomed.info/sct",
      "code": "79778003",
      "display": "Acetylene",
      "text": "Asetilen"
    }
  ],
  "codeText": "Alergi terhadap asetilkarinitin dan asetilen",
  "reaction": [
    {
      "system": "http://snomed.info/sct",
      "code": "31996006",
      "display": "Vasculitis",
      "text": "Vaskulitis"
    },
    {
      "system": "http://snomed.info/sct",
      "code": "51599000",
      "display": "Edema of larynx",
      "text": "Edema laring"
    }
  ],
  "reactionDescription": "Pasien mengalami reaksi berupa vaskulitis dan edema laring"
}
```

---

## Penjelasan Field

### `category` — Kategori Alergi

| Nilai | Keterangan |
|-------|-----------|
| `food` | Alergi terhadap makanan |
| `medication` | Alergi terhadap obat-obatan |
| `environment` | Alergi terhadap lingkungan (debu, serbuk sari, dll.) |
| `biologic` | Alergi terhadap produk biologis |

- **Tipe**: `string`
- **Wajib**: Ya

```json
"category": "food"
```

---

### `criticality` — Tingkat Kekritisan

Menunjukkan potensi dampak klinis terburuk jika pasien terpapar zat alergen.

| Nilai | Keterangan |
|-------|-----------|
| `low` | Reaksi ringan, tidak mengancam jiwa |
| `high` | Reaksi berat, berpotensi mengancam jiwa |
| `unable-to-assess` | Tidak dapat dinilai |

- **Tipe**: `string`
- **Wajib**: Ya

```json
"criticality": "high"
```

---

### `code` — Zat Penyebab Alergi

Array berisi satu atau lebih zat alergen yang dikodekan menggunakan sistem terminologi standar (SNOMED CT).

- **Tipe**: `array` of `object`
- **Wajib**: Ya (minimal 1 item)

Setiap item dalam array memiliki field berikut:

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `system` | `string` | Ya | URI sistem terminologi (contoh: `http://snomed.info/sct`) |
| `code` | `string` | Ya | Kode dari sistem terminologi |
| `display` | `string` | Ya | Nama tampilan resmi dalam bahasa Inggris |
| `text` | `string` | Tidak | Terjemahan atau nama tampilan dalam bahasa Indonesia |

```json
"code": [
  {
    "system": "http://snomed.info/sct",
    "code": "430747003",
    "display": "Acetylcarnitine",
    "text": "Asetilkarinitin"
  }
]
```

---

### `codeText` — Deskripsi Alergen (Teks Bebas)

Ringkasan naratif dari seluruh zat penyebab alergi dalam satu kalimat, biasanya dalam bahasa Indonesia.

- **Tipe**: `string`
- **Wajib**: Tidak (direkomendasikan)

```json
"codeText": "Alergi terhadap asetilkarinitin dan asetilen"
```

---

### `reaction` — Manifestasi / Reaksi Klinis

Array berisi satu atau lebih manifestasi klinis yang terjadi saat pasien terpapar zat alergen. Sama seperti `code`, setiap item dikodekan menggunakan SNOMED CT.

- **Tipe**: `array` of `object`
- **Wajib**: Tidak (direkomendasikan)

Setiap item dalam array memiliki field berikut:

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `system` | `string` | Ya | URI sistem terminologi (contoh: `http://snomed.info/sct`) |
| `code` | `string` | Ya | Kode manifestasi klinis |
| `display` | `string` | Ya | Nama tampilan resmi dalam bahasa Inggris |
| `text` | `string` | Tidak | Terjemahan atau nama tampilan dalam bahasa Indonesia |

```json
"reaction": [
  {
    "system": "http://snomed.info/sct",
    "code": "31996006",
    "display": "Vasculitis",
    "text": "Vaskulitis"
  }
]
```

---

### `reactionDescription` — Deskripsi Reaksi (Teks Bebas)

Ringkasan naratif dari seluruh reaksi klinis yang dialami pasien dalam satu kalimat.

- **Tipe**: `string`
- **Wajib**: Tidak (direkomendasikan)

```json
"reactionDescription": "Pasien mengalami reaksi berupa vaskulitis dan edema laring"
```

---

## Ringkasan Seluruh Field

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `category` | `string` | Ya | Kategori alergi (`food`, `medication`, `environment`, `biologic`) |
| `criticality` | `string` | Ya | Tingkat kekritisan (`low`, `high`, `unable-to-assess`) |
| `code` | `array` | Ya | Daftar zat penyebab alergi (SNOMED CT) |
| `codeText` | `string` | Tidak | Deskripsi naratif zat penyebab alergi |
| `reaction` | `array` | Tidak | Daftar manifestasi/reaksi klinis (SNOMED CT) |
| `reactionDescription` | `string` | Tidak | Deskripsi naratif reaksi klinis |

---

## Contoh Penggunaan

### Alergi Obat dengan Reaksi Ringan

```json
{
  "category": "medication",
  "criticality": "low",
  "code": [
    {
      "system": "http://snomed.info/sct",
      "code": "387458008",
      "display": "Aspirin",
      "text": "Aspirin"
    }
  ],
  "codeText": "Alergi terhadap aspirin",
  "reaction": [
    {
      "system": "http://snomed.info/sct",
      "code": "126485001",
      "display": "Urticaria",
      "text": "Urtikaria (biduran)"
    }
  ],
  "reactionDescription": "Pasien mengalami urtikaria setelah mengonsumsi aspirin"
}
```

### Alergi Lingkungan Tanpa Reaksi Spesifik

```json
{
  "category": "environment",
  "criticality": "low",
  "code": [
    {
      "system": "http://snomed.info/sct",
      "code": "256259004",
      "display": "Pollen",
      "text": "Serbuk sari"
    }
  ],
  "codeText": "Alergi terhadap serbuk sari"
}
```

---

## Referensi

- [FHIR R4 — AllergyIntolerance](https://hl7.org/fhir/R4/allergyintolerance.html)
- [SNOMED CT Browser](https://browser.ihtsdotools.org/)
