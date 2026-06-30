# Rekonsiliasi Obat

Dokumentasi ini menjelaskan struktur JSON untuk fitur Rekonsiliasi Obat pada modul Rawat Inap, yang mencakup regimen obat pra-admisi dan tindak lanjut saat perawatan di rumah sakit.

File JSON terdapat pada: `Implementation/Inpatient/RekonsiliasiObat/`

## 1. MedicationStatement (Pra-Admisi)

Digunakan untuk merekam regimen obat pasien sebelum masuk ke rumah sakit.
Sesuai dengan aturan maksimal 2 level kedalaman, struktur menggunakan array `component[]`.

### Struktur Data
- `resourceType`: `"MedicationStatement"`
- `category`: `"community"` (Menandakan obat dari luar/pra-admisi)
- `component[]`: Daftar obat yang sedang dikonsumsi.
  - `code`: Identifikasi obat. Wajib menyertakan `text` dalam Bahasa Indonesia.
  - `doseQuantity`: Nilai dosis numerik (tanpa `system`).
  - `timing`: Aturan frekuensi (frequency, period, periodUnit).
  - `route`: Rute pemberian obat (menggunakan kode SNOMED CT yang di-flatten).

## 2. MedicationRequest (Tindak Lanjut)

Digunakan untuk merekam keputusan klinis (tindak lanjut) terhadap regimen obat.
Struktur disederhanakan dengan memasukkan field langsung ke dalam item di `component[]`.

### Struktur Data
- `resourceType`: `"MedicationRequest"`
- `component[]`: Daftar keputusan tindak lanjut untuk setiap regimen.
  - `status`: Status tindak lanjut (`"active"` untuk Lanjut Aturan Sama / Lanjut Aturan Berubah, `"stopped"` untuk Stop).
  - `code`: Nama obat (referensi teks).
  - `dosageInstruction`: Penanda tindak lanjut ("Lanjut Aturan Berubah", "Lanjut Aturan Sama", "Stop").
  - `description`: Teks naratif berisi detail perubahan regimen (dosis, frekuensi, rute, sumber). Disesuaikan dari "note" FHIR.

---
*Dokumentasi JSON telah diperbarui dan divalidasi sesuai checklist standarisasi FHIR di repository ini.*
