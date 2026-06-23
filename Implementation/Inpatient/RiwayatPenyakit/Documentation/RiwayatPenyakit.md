# Riwayat Penyakit

Dokumen ini menjelaskan struktur JSON yang digunakan untuk mencatat **riwayat penyakit** pasien rawat inap. Form Riwayat Penyakit melibatkan **3 resource FHIR** yang berbeda, masing-masing mewakili aspek klinis yang berbeda.

---

## Mappingan Resource FHIR

| No | Field Formulir | Resource FHIR | Element Path FHIR | Terminologi |
|----|---------------|---------------|-------------------|-------------|
| 1 | Riwayat Penyakit Sekarang | [`Condition`](https://hl7.org/fhir/R5/condition.html) | `Condition.code.coding.code` | SNOMED CT |
| 2 | Riwayat Penyakit Terdahulu | [`Condition`](https://hl7.org/fhir/R5/condition.html) | `Condition.code.coding.code` | SNOMED CT |
| 3 | Riwayat Pengobatan | [`MedicationStatement`](https://hl7.org/fhir/R5/medicationstatement.html) | `MedicationStatement.medicationCodeableConcept` | KFA |
| 4 | Riwayat Penyakit dalam Keluarga | [`FamilyMemberHistory`](https://hl7.org/fhir/R5/familymemberhistory.html) | `FamilyMemberHistory.condition.code.coding` | SNOMED CT |

> [!NOTE]
> Field **Riwayat Perawatan Sebelumnya** pada form menggunakan input teks bebas dan tidak memiliki resource JSON terpisah.

---

## 1. Condition — Riwayat Penyakit Sekarang & Terdahulu

Resource `Condition` digunakan untuk mencatat **riwayat penyakit pribadi** pasien, baik yang masih aktif (sekarang) maupun yang sudah tidak aktif (terdahulu). Perbedaan antara keduanya ditentukan oleh field `clinicalStatus`.

### Struktur Lengkap

```json
{
  "component": [
    {
      "clinicalStatus": "active",
      "system": "http://snomed.info/sct",
      "code": "38341003",
      "display": "Hypertensive disorder",
      "text": "Hipertensi"
    },
    {
      "clinicalStatus": "active",
      "system": "http://snomed.info/sct",
      "code": "73211009",
      "display": "Diabetes mellitus",
      "text": "Diabetes melitus"
    },
    {
      "clinicalStatus": "inactive",
      "system": "http://snomed.info/sct",
      "code": "195967001",
      "display": "Asthma",
      "text": "Asma"
    },
    {
      "clinicalStatus": "inactive",
      "system": "http://snomed.info/sct",
      "code": "36971009",
      "display": "Sinusitis",
      "text": "Sinusitis"
    }
  ]
}
```

### Penjelasan Field

#### `component` — Daftar Kondisi/Penyakit

Array berisi satu atau lebih kondisi klinis pasien.

- **Tipe**: `array` of `object`
- **Wajib**: Ya (minimal 1 item)

Setiap item dalam array memiliki field berikut:

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `clinicalStatus` | `string` | Ya | Status klinis kondisi (`active` atau `inactive`) |
| `system` | `string` | Ya | URI sistem terminologi (selalu `http://snomed.info/sct`) |
| `code` | `string` | Ya | Kode SNOMED CT untuk penyakit |
| `display` | `string` | Ya | Nama tampilan resmi dalam bahasa Inggris |
| `text` | `string` | Tidak | Nama tampilan dalam bahasa Indonesia |

#### `clinicalStatus` — Status Klinis

Menentukan apakah penyakit saat ini masih aktif atau sudah tidak aktif. Field ini juga menentukan kategori penyakit pada formulir:

| Nilai | Keterangan | Kategori Formulir |
|-------|-----------|-------------------|
| `active` | Penyakit yang masih diderita saat ini | **Riwayat Penyakit Sekarang** |
| `inactive` | Penyakit yang pernah diderita sebelumnya | **Riwayat Penyakit Terdahulu** |

> [!IMPORTANT]
> Field `clinicalStatus` menentukan di kolom mana penyakit akan ditampilkan pada tabel riwayat. Penyakit dengan status `active` masuk ke kolom *Riwayat Penyakit Sekarang*, sedangkan `inactive` masuk ke kolom *Riwayat Penyakit Terdahulu*.

---

## 2. MedicationStatement — Riwayat Pengobatan

Resource `MedicationStatement` digunakan untuk mencatat **riwayat pengobatan** yang sedang atau pernah dikonsumsi pasien. Kode obat menggunakan terminologi **KFA** (*Kode Farmasi dan Alat Kesehatan*) dari Kemenkes RI.

### Struktur Lengkap

```json
{
  "medication": [
    {
      "system": "http://sys-ids.kemkes.go.id/kfa",
      "code": "93001295",
      "display": "Metformin 500 mg tablet",
      "text": "Metformin 500 mg tablet"
    },
    {
      "system": "http://sys-ids.kemkes.go.id/kfa",
      "code": "93001019",
      "display": "Amlodipine 5 mg tablet",
      "text": "Amlodipine 5 mg tablet"
    }
  ]
}
```

### Penjelasan Field

#### `medication` — Daftar Obat

Array berisi satu atau lebih obat yang dikonsumsi pasien.

- **Tipe**: `array` of `object`
- **Wajib**: Ya (minimal 1 item)

Setiap item dalam array memiliki field berikut:

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `system` | `string` | Ya | URI sistem terminologi (selalu `http://sys-ids.kemkes.go.id/kfa`) |
| `code` | `string` | Ya | Kode KFA untuk obat |
| `display` | `string` | Ya | Nama obat lengkap beserta sediaan |
| `text` | `string` | Tidak | Nama tampilan dalam bahasa Indonesia |

> [!NOTE]
> Berbeda dengan resource lainnya yang menggunakan **SNOMED CT**, resource ini menggunakan terminologi **KFA** (Katalog Farmasi dan Alat Kesehatan) yang dikelola oleh Kementerian Kesehatan RI melalui sistem `http://sys-ids.kemkes.go.id/kfa`.

---

## 3. FamilyMemberHistory — Riwayat Penyakit dalam Keluarga

Resource `FamilyMemberHistory` digunakan untuk mencatat **riwayat penyakit** yang dimiliki oleh anggota keluarga pasien. Informasi ini penting untuk evaluasi risiko genetik dan predisposisi penyakit.

### Struktur Lengkap

```json
{
  "condition": [
    {
      "system": "http://snomed.info/sct",
      "code": "73211009",
      "display": "Diabetes mellitus",
      "text": "Diabetes melitus"
    },
    {
      "system": "http://snomed.info/sct",
      "code": "56265001",
      "display": "Heart disease",
      "text": "Penyakit jantung"
    }
  ]
}
```

### Penjelasan Field

#### `condition` — Daftar Penyakit Keluarga

Array berisi satu atau lebih penyakit yang dimiliki anggota keluarga pasien.

- **Tipe**: `array` of `object`
- **Wajib**: Ya (minimal 1 item)

Setiap item dalam array memiliki field berikut:

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `system` | `string` | Ya | URI sistem terminologi (selalu `http://snomed.info/sct`) |
| `code` | `string` | Ya | Kode SNOMED CT untuk penyakit |
| `display` | `string` | Ya | Nama tampilan resmi dalam bahasa Inggris |
| `text` | `string` | Tidak | Nama tampilan dalam bahasa Indonesia |

---

## Ringkasan Seluruh Resource

| Resource | Field Formulir | Root Array | Terminologi | Input |
|----------|---------------|-----------|-------------|-------|
| `Condition` | Riwayat Penyakit Sekarang | `component` (clinicalStatus: `active`) | SNOMED CT | Dropdown |
| `Condition` | Riwayat Penyakit Terdahulu | `component` (clinicalStatus: `inactive`) | SNOMED CT | Dropdown |
| `MedicationStatement` | Riwayat Pengobatan | `medication` | KFA | Dropdown |
| `FamilyMemberHistory` | Riwayat Penyakit dalam Keluarga | `condition` | SNOMED CT | Dropdown |
| — | Riwayat Perawatan Sebelumnya | — | — | Teks bebas |

---

## Contoh Penggunaan

### Condition — Hanya Riwayat Penyakit Sekarang

```json
{
  "component": [
    {
      "clinicalStatus": "active",
      "system": "http://snomed.info/sct",
      "code": "84114007",
      "display": "Heart failure",
      "text": "Gagal jantung"
    }
  ]
}
```

### Condition — Kombinasi Penyakit Sekarang dan Terdahulu

```json
{
  "component": [
    {
      "clinicalStatus": "active",
      "system": "http://snomed.info/sct",
      "code": "38341003",
      "display": "Hypertensive disorder",
      "text": "Hipertensi"
    },
    {
      "clinicalStatus": "inactive",
      "system": "http://snomed.info/sct",
      "code": "40055000",
      "display": "Chronic sinusitis",
      "text": "Sinusitis kronis"
    }
  ]
}
```

### MedicationStatement — Satu Obat

```json
{
  "medication": [
    {
      "system": "http://sys-ids.kemkes.go.id/kfa",
      "code": "93001295",
      "display": "Metformin 500 mg tablet",
      "text": "Metformin 500 mg tablet"
    }
  ]
}
```

### FamilyMemberHistory — Satu Penyakit Keluarga

```json
{
  "condition": [
    {
      "system": "http://snomed.info/sct",
      "code": "22298006",
      "display": "Myocardial infarction",
      "text": "Infark miokard"
    }
  ]
}
```

---

## Referensi

- [FHIR R5 — Condition](https://hl7.org/fhir/R5/condition.html)
- [FHIR R5 — MedicationStatement](https://hl7.org/fhir/R5/medicationstatement.html)
- [FHIR R5 — FamilyMemberHistory](https://hl7.org/fhir/R5/familymemberhistory.html)
- [SNOMED CT Browser](https://browser.ihtsdotools.org/)
- [KFA Kemenkes](https://dto.kemkes.go.id/kfa)
