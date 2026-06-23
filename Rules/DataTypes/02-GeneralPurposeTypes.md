# FHIR General-Purpose Data Types

Tipe data *General-Purpose* adalah struktur data kompleks yang paling sering muncul di berbagai FHIR resource. Tipe ini terdiri atas kumpulan *fields* (biasanya gabungan primitif dan tipe kompleks lainnya) yang mendefinisikan suatu entitas kecil yang utuh.

Berikut adalah tipe-tipe data yang paling umum dijumpai (termasuk yang diperkenalkan di FHIR R5):

## 1. Identifier
Nomor unik yang biasa diberikan kepada seseorang, tempat, atau hal tertentu oleh sebuah entitas (seperti Rumah Sakit, KTP, dll).

```json
"identifier": [
  {
    "use": "official",
    "system": "urn:oid:2.16.840.1.113883.4.1",
    "value": "123-45-678"
  }
]
```

## 2. HumanName
Nama manusia yang dapat dipecah menjadi bagian-bagian (nama keluarga, nama depan, gelar).

```json
"name": [
  {
    "use": "official",
    "text": "Dr. Budi Santoso, Sp.PD",
    "family": "Santoso",
    "given": ["Budi"],
    "prefix": ["Dr."],
    "suffix": ["Sp.PD"]
  }
]
```

## 3. Address
Alamat fisik atau pos dari seseorang / instansi.

```json
"address": [
  {
    "use": "home",
    "type": "physical",
    "line": ["Jl. Merdeka No. 10"],
    "city": "Jakarta Selatan",
    "postalCode": "12345"
  }
]
```

## 4. ContactPoint
Kontak komunikasi, seperti nomor telepon, faks, email, atau pager.

```json
"telecom": [
  {
    "system": "phone",
    "value": "081234567890",
    "use": "mobile"
  }
]
```

## 5. Coding & CodeableConcept
- **Coding**: Representasi referensi kode menggunakan sebuah sistem terminologi standar.
- **CodeableConcept**: Gabungan dari beberapa `Coding` beserta teks aslinya.

```json
"code": {
  "coding": [
    {
      "system": "http://loinc.org",
      "code": "8480-6",
      "display": "Systolic blood pressure"
    }
  ],
  "text": "Tekanan Darah Sistolik"
}
```

## 6. Quantity (termasuk Age, Distance, Duration, Count, MoneyQuantity, SimpleQuantity)
Nilai kuantitatif terukur beserta satuannya.

```json
"valueQuantity": {
  "value": 38.5,
  "unit": "Cel",
  "system": "http://unitsofmeasure.org",
  "code": "Cel"
}
```

## 7. Period
Rentang waktu spesifik (waktu mulai `start` dan selesai `end`).

```json
"effectivePeriod": {
  "start": "2023-11-01T08:00:00Z",
  "end": "2023-11-05T12:00:00Z"
}
```

## 8. Range
Rentang batas nilai numerik tertentu (memiliki batas bawah `low` dan atas `high`).

```json
"valueRange": {
  "low": { "value": 1.5, "unit": "mg" },
  "high": { "value": 3.0, "unit": "mg" }
}
```

## 9. Ratio & RatioRange
- **Ratio**: Perbandingan antara dua nilai (numerator dan denominator).
- **RatioRange** (Baru/R5): Menggabungkan perbandingan dengan rentang nilai.

```json
"valueRatio": {
  "numerator": { "value": 10, "unit": "mg" },
  "denominator": { "value": 1, "unit": "mL" }
}
```

## 10. Annotation
Penjelasan tambahan berupa teks dengan penulis/waktu.

```json
"note": [
  {
    "authorString": "Perawat",
    "text": "Pasien mengeluh gatal."
  }
]
```

## 11. Timing
Tipe data mengatur perulangan jadwal/event (contoh: diminum 3 kali sehari).

```json
"timing": {
  "repeat": {
    "frequency": 3,
    "period": 1,
    "periodUnit": "d"
  }
}
```

## 12. Attachment
Konten *embedded* atau tautan file eksternal (PDF, Gambar).

```json
"content": {
  "attachment": {
    "contentType": "application/pdf",
    "url": "http://example.org/documents/123.pdf"
  }
}
```

## 13. Money
Representasi mata uang, terdiri dari nominal dan jenis mata uang.

```json
"cost": {
  "value": 150000.00,
  "currency": "IDR"
}
```

## 14. SampledData
Serangkaian pengukuran atau nilai yang disampel berulang pada interval waktu tetap (misalnya hasil EKG).

```json
"valueSampledData": {
  "origin": {
    "value": 0,
    "unit": "mV"
  },
  "period": 2.0,
  "dimensions": 1,
  "data": "1.1 1.2 1.3 1.5 1.6"
}
```

## 15. Signature
Tanda tangan digital (XML DSig atau JWS) yang dilampirkan ke dalam dokumen untuk membuktikan otentisitas dari sebuah catatan klinis.

```json
"signature": [
  {
    "type": [
      {
        "system": "urn:iso-astm:E1762-95:2013",
        "code": "1.2.840.10065.1.12.1.1"
      }
    ],
    "when": "2023-11-20T10:00:00Z",
    "who": { "reference": "Practitioner/123" },
    "sigFormat": "application/jose",
    "data": "eyJhbGciOiJSUzI1NiIsInR5cCI..."
  }
]
```
