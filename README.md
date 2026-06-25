# 🏥 FHIR Resource - Struktur Data Integrasi SIMRS

Repository ini berisi **struktur JSON berbasis FHIR (Fast Healthcare Interoperability Resources)** yang dirancang sebagai panduan integrasi data antara **Sistem Informasi Manajemen Rumah Sakit (SIMRS)** dengan platform **Satu Sehat** milik Kementerian Kesehatan RI.

Struktur JSON dalam repository ini **disederhanakan** dari standar FHIR R5 agar lebih mudah dikonsumsi oleh tim pengembang aplikasi, dengan tetap mempertahankan kompatibilitas terhadap terminologi dan kode standar internasional (LOINC, SNOMED-CT, KFA, WHO ATC).

---

## 📁 Struktur Repository

```
fhir-resource/
│
├── 📂 Rules/                          # Panduan & aturan pembuatan JSON
│   ├── Rules.md                       # Rules utama pembuatan struktur JSON
│   └── DataTypes/                     # Referensi tipe data FHIR
│       ├── 01-PrimitiveTypes.md
│       ├── 02-GeneralPurposeTypes.md
│       ├── 03-MetadataTypes.md
│       └── 04-SpecialPurposeTypes.md
│
├── 📂 FHIRResources/                  # Referensi resource FHIR standar (lengkap)
│   ├── OrdersAndObservations/         # Observation, DiagnosticReport, dll.
│   ├── PatientAdministration/         # Patient, Encounter, Practitioner, dll.
│   └── PatientCare/                   # Condition, AllergyIntolerance, Procedure, dll.
│
├── 📂 Implementation/                 # Implementasi JSON per modul SIMRS
│   ├── Inpatient/                     # Rawat Inap
│   │   ├── TTV/                       # Tanda-Tanda Vital
│   │   ├── RiwayatAlergi/             # Riwayat Alergi Pasien
│   │   ├── RiwayatPenyakit/           # Riwayat Penyakit Pasien
│   │   └── AssesmentAwalKeperawatan/  # Asesmen Awal Keperawatan
│   └── Emergency/                     # Instalasi Gawat Darurat (IGD)
│
└── 📂 Documentation/                  # Dokumentasi per modul
    ├── Inpatient/                     # Dokumen rawat inap
    │   ├── TTV.md
    │   ├── RiwayatAlergi.md
    │   └── RiwayatPenyakit.md
    ├── Ambulatory/                    # Dokumen rawat jalan
    └── Emergency/                     # Dokumen IGD
```

---

## 📐 Rules Pembuatan JSON

Semua file JSON di folder `Implementation/` mengikuti aturan yang didefinisikan di [`Rules/Rules.md`](Rules/Rules.md). Berikut ringkasan rules utamanya:

### 1. Maksimal 2 Level Kedalaman

Struktur JSON tidak boleh melebihi **2 level nesting** dari root object. Array `coding[]` pada FHIR standar harus di-**flatten** langsung ke parent object.

```
Level 0: { }              ← root
Level 1: component[], code ← properti langsung
Level 2: code.system       ← batas maksimal
```

### 2. Hanya Field Data Inti

Field metadata FHIR seperti `id`, `status`, `identifier`, `subject`, `encounter`, `performer`, `issued`, dll. **tidak disertakan** karena secara default disimpan sebagai dedicated column pada masing-masing tabel

### 3. Konvensi Penamaan

- Setiap `code` wajib memiliki field `text` dalam **Bahasa Indonesia**
- Nama file menggunakan format: `{ResourceType}.json` (contoh: `Observation.json`, `Condition.json`)
- Data berulang menggunakan array `component[]`
- Field deskripsi singular menggunakan suffix `Description` (contoh: `componentDescription`, `reactionDescription`)

---

## 📋 Modul yang Tersedia

### Rawat Inap (Inpatient)

| Modul | Resource FHIR | File | Deskripsi |
|-------|---------------|------|-----------|
| **TTV** | Observation | [`Observation.json`](Implementation/Inpatient/TTV/Observation.json) | Tanda-tanda vital: tekanan darah, suhu, nadi, GCS, SpO2, BMI, dll. |
| **Riwayat Alergi** | AllergyIntolerance | [`AllergyIntolerance.json`](Implementation/Inpatient/RiwayatAlergi/AllergyIntolerance.json) | Kategori, kritikalitas, kode penyebab, dan reaksi alergi |
| **Riwayat Penyakit** | Condition, MedicationStatement, FamilyMemberHistory | [Folder](Implementation/Inpatient/RiwayatPenyakit/) | Penyakit sekarang/terdahulu, riwayat pengobatan, penyakit keluarga |

---

## 🏷️ Sistem Terminologi

| Terminologi | URI Sistem | Digunakan Untuk |
|-------------|-----------|-----------------|
| **LOINC** | `http://loinc.org` | Kode observasi & pengukuran (TTV, lab) |
| **SNOMED CT** | `http://snomed.info/sct` | Kode klinis (diagnosis, penyakit, reaksi, pulsasi) |
| **KFA** | `http://sys-ids.kemkes.go.id/kfa` | Kode Farmasi Indonesia (obat/pengobatan) |
| **HL7 FHIR** | `http://hl7.org/fhir/...` | Value set FHIR (kategori alergi, kritikalitas) |

---

## 📚 Referensi FHIR Standar

Folder `FHIRResources/` berisi contoh JSON **lengkap** dari resource FHIR standar (sebelum disederhanakan). Berguna sebagai referensi ketika perlu memahami field apa saja yang tersedia di standar aslinya.

| Kategori | Jumlah Resource | Contoh |
|----------|-----------------|--------|
| **Orders & Observations** | 26 resource | Observation, DiagnosticReport, ServiceRequest, NutritionOrder |
| **Patient Administration** | 22 resource | Patient, Encounter, Practitioner, Organization, Appointment |
| **Patient Care** | 14 resource | Condition, AllergyIntolerance, Procedure, CarePlan, CareTeam |

---

## 📖 Referensi Tipe Data

Folder `Rules/DataTypes/` berisi panduan lengkap tipe data FHIR dalam Bahasa Indonesia:

| File | Isi |
|------|-----|
| `01-PrimitiveTypes.md` | boolean, string, integer, decimal, dateTime, code, id, dll. |
| `02-GeneralPurposeTypes.md` | Identifier, HumanName, CodeableConcept, Quantity, Period, dll. |
| `03-MetadataTypes.md` | ContactDetail, DataRequirement, Expression, TriggerDefinition, dll. |
| `04-SpecialPurposeTypes.md` | Reference, Extension, Narrative, Dosage, Meta, dll. |

---

## 🚀 Cara Menggunakan

1. **Lihat rules** di [`Rules/Rules.md`](Rules/Rules.md) untuk memahami aturan pembuatan JSON
2. **Pilih modul** di `Implementation/` sesuai kebutuhan (Inpatient/Emergency/Ambulatory)
3. **Gunakan JSON** sebagai kontrak data antara frontend ↔ backend ↔ Satu Sehat
4. **Baca dokumentasi** di `Documentation/` untuk penjelasan detail setiap field
5. **Referensi FHIR standar** di `FHIRResources/` jika perlu field tambahan dari standar asli

---

### Checklist Sebelum Commit

- [ ] Tidak ada nesting lebih dari 2 level
- [ ] Tidak ada array `coding[]` — sudah di-flatten
- [ ] Field metadata resource tidak disertakan (kecuali `resourceType` jika multi-resource)
- [ ] Setiap `code` memiliki `text` dalam Bahasa Indonesia
- [ ] Dokumentasi `.md` sudah dibuat/diperbarui