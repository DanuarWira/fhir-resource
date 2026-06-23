# Tanda-Tanda Vital (TTV)

Dokumen ini menjelaskan struktur JSON yang digunakan untuk mencatat **tanda-tanda vital (TTV)** pasien rawat inap. JSON ini merupakan representasi sederhana (*simplified*) dari resource FHIR [`Observation`](https://hl7.org/fhir/observation.html) yang disesuaikan untuk kebutuhan integrasi sistem informasi rumah sakit.

---

## Struktur Lengkap

```json
{
  "component": [
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8480-6",
        "display": "Systolic blood pressure",
        "text": "Tekanan Darah Sistolik"
      },
      "valueQuantity": {
        "value": 120,
        "unit": "mmHg",
        "code": "mm[Hg]"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8462-4",
        "display": "Diastolic blood pressure",
        "text": "Tekanan Darah Diastolik"
      },
      "valueQuantity": {
        "value": 80,
        "unit": "mmHg",
        "code": "mm[Hg]"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8310-5",
        "display": "Body temperature",
        "text": "Suhu Tubuh"
      },
      "valueQuantity": {
        "value": 36.5,
        "unit": "°C",
        "code": "Cel"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8478-0",
        "display": "Mean arterial pressure",
        "text": "MAP"
      },
      "valueQuantity": {
        "value": 93.3,
        "unit": "mmHg",
        "code": "mm[Hg]"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8867-4",
        "display": "Heart rate",
        "text": "Nadi"
      },
      "valueQuantity": {
        "value": 80,
        "unit": "kali/menit",
        "code": "/min"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8884-9",
        "display": "Heart rate rhythm",
        "text": "Irama"
      },
      "valueCodeableConcept": {
        "system": "http://snomed.info/sct",
        "code": "271636001",
        "display": "Regular pulse",
        "text": "Teratur"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "44974-4",
        "display": "Pulse quality",
        "text": "Pulsasi"
      },
      "valueCodeableConcept": {
        "system": "http://snomed.info/sct",
        "code": "371632003",
        "display": "Normal pulse character",
        "text": "Kuat"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "9279-1",
        "display": "Respiratory rate",
        "text": "Pernapasan"
      },
      "valueQuantity": {
        "value": 18,
        "unit": "kali/menit",
        "code": "/min"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "2708-6",
        "display": "Oxygen saturation in Arterial blood",
        "text": "SpO2"
      },
      "valueQuantity": {
        "value": 98,
        "unit": "%",
        "code": "%"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "76010-8",
        "display": "Oxygen assistance type",
        "text": "Alat Bantu Oksigenasi"
      },
      "valueCodeableConcept": {
        "system": "http://snomed.info/sct",
        "code": "371825009",
        "display": "Patient on oxygen",
        "text": "Nasal kanul"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8302-2",
        "display": "Body height",
        "text": "Tinggi/Panjang Badan"
      },
      "valueQuantity": {
        "value": 170,
        "unit": "cm",
        "code": "cm"
      }
    },
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
        "code": "39156-5",
        "display": "Body mass index",
        "text": "BMI"
      },
      "valueQuantity": {
        "value": 24.2,
        "unit": "kg/m²",
        "code": "kg/m2"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8277-6",
        "display": "Body surface area",
        "text": "BSA"
      },
      "valueQuantity": {
        "value": 1.81,
        "unit": "m²",
        "code": "m2"
      }
    },
    {
      "code": {
        "system": "http://loinc.org",
        "code": "8335-2",
        "display": "Ideal body weight",
        "text": "BBI"
      },
      "valueQuantity": {
        "value": 63,
        "unit": "kg",
        "code": "kg"
      }
    }
  ]
}
```

> [!NOTE]
> Contoh di atas hanya menampilkan satu komponen. JSON lengkap berisi **15 komponen** TTV yang didokumentasikan di bawah.

---

## Penjelasan Struktur

### `component` — Daftar Komponen TTV

Array yang berisi seluruh pengukuran tanda-tanda vital. Setiap item merupakan satu observasi klinis.

- **Tipe**: `array` of `object`
- **Wajib**: Ya (minimal 1 item)

Setiap item memiliki field `code` (identitas pengukuran) dan salah satu dari `valueQuantity` atau `valueCodeableConcept` (hasil pengukuran).

---

### `code` — Identitas Pengukuran

Mengidentifikasi jenis observasi menggunakan sistem terminologi LOINC.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `system` | `string` | Ya | URI sistem terminologi (selalu `http://loinc.org`) |
| `code` | `string` | Ya | Kode LOINC |
| `display` | `string` | Ya | Nama tampilan resmi dalam bahasa Inggris |
| `text` | `string` | Tidak | Nama tampilan dalam bahasa Indonesia |

---

### `valueQuantity` — Hasil Pengukuran Numerik

Digunakan untuk komponen yang hasilnya berupa angka terukur.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `value` | `number` | Ya | Nilai numerik hasil pengukuran |
| `unit` | `string` | Ya | Satuan tampilan (contoh: `mmHg`, `°C`, `%`) |
| `code` | `string` | Ya | Kode satuan berdasarkan [UCUM](http://unitsofmeasure.org) |

```json
"valueQuantity": {
  "value": 120,
  "unit": "mmHg",
  "code": "mm[Hg]"
}
```

---

### `valueCodeableConcept` — Hasil Pengukuran Kategorikal

Digunakan untuk komponen yang hasilnya berupa kode/kategori, bukan angka. Dikodekan menggunakan SNOMED CT.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `system` | `string` | Ya | URI sistem terminologi (contoh: `http://snomed.info/sct`) |
| `code` | `string` | Ya | Kode SNOMED CT |
| `display` | `string` | Ya | Nama tampilan resmi dalam bahasa Inggris |
| `text` | `string` | Tidak | Nama tampilan dalam bahasa Indonesia |

```json
"valueCodeableConcept": {
  "system": "http://snomed.info/sct",
  "code": "271636001",
  "display": "Regular pulse",
  "text": "Teratur"
}
```

---

## Daftar Komponen TTV

### Tekanan Darah

| # | Komponen | LOINC | Tipe Nilai | Satuan |
|---|----------|-------|-----------|--------|
| 1 | Tekanan Darah Sistolik | `8480-6` | `valueQuantity` | mmHg (`mm[Hg]`) |
| 2 | Tekanan Darah Diastolik | `8462-4` | `valueQuantity` | mmHg (`mm[Hg]`) |
| 3 | MAP (*Mean Arterial Pressure*) | `8478-0` | `valueQuantity` | mmHg (`mm[Hg]`) |

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "8480-6",
    "display": "Systolic blood pressure",
    "text": "Tekanan Darah Sistolik"
  },
  "valueQuantity": {
    "value": 120,
    "unit": "mmHg",
    "code": "mm[Hg]"
  }
}
```

---

### Suhu Tubuh

| # | Komponen | LOINC | Tipe Nilai | Satuan |
|---|----------|-------|-----------|--------|
| 4 | Suhu Tubuh | `8310-5` | `valueQuantity` | °C (`Cel`) |

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "8310-5",
    "display": "Body temperature",
    "text": "Suhu Tubuh"
  },
  "valueQuantity": {
    "value": 36.5,
    "unit": "°C",
    "code": "Cel"
  }
}
```

---

### Nadi / Denyut Jantung

| # | Komponen | LOINC | Tipe Nilai | Satuan / Terminologi |
|---|----------|-------|-----------|---------------------|
| 5 | Nadi (*Heart rate*) | `8867-4` | `valueQuantity` | kali/menit (`/min`) |
| 6 | Irama (*Heart rate rhythm*) | `8884-9` | `valueCodeableConcept` | SNOMED CT |
| 7 | Pulsasi (*Pulse quality*) | `44974-4` | `valueCodeableConcept` | SNOMED CT |

**Contoh — Nadi (numerik):**

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "8867-4",
    "display": "Heart rate",
    "text": "Nadi"
  },
  "valueQuantity": {
    "value": 80,
    "unit": "kali/menit",
    "code": "/min"
  }
}
```

**Contoh — Irama (kategorikal):**

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "8884-9",
    "display": "Heart rate rhythm",
    "text": "Irama"
  },
  "valueCodeableConcept": {
    "system": "http://snomed.info/sct",
    "code": "271636001",
    "display": "Regular pulse",
    "text": "Teratur"
  }
}
```

**Nilai yang umum untuk Irama:**

| Kode SNOMED CT | Display | Teks |
|----------------|---------|------|
| `271636001` | Regular pulse | Teratur |
| `61086009` | Irregular pulse | Tidak teratur |

**Nilai yang umum untuk Pulsasi:**

| Kode SNOMED CT | Display | Teks |
|----------------|---------|------|
| `371632003` | Normal pulse character | Kuat |
| `271637005` | Weak pulse | Lemah |

---

### Pernapasan & Oksigenasi

| # | Komponen | LOINC | Tipe Nilai | Satuan / Terminologi |
|---|----------|-------|-----------|---------------------|
| 8 | Pernapasan (*Respiratory rate*) | `9279-1` | `valueQuantity` | kali/menit (`/min`) |
| 9 | SpO2 (*Oxygen saturation*) | `2708-6` | `valueQuantity` | % (`%`) |
| 10 | Alat Bantu Oksigenasi | `76010-8` | `valueCodeableConcept` | SNOMED CT |

**Contoh — SpO2:**

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "2708-6",
    "display": "Oxygen saturation in Arterial blood",
    "text": "SpO2"
  },
  "valueQuantity": {
    "value": 98,
    "unit": "%",
    "code": "%"
  }
}
```

**Contoh — Alat Bantu Oksigenasi:**

```json
{
  "code": {
    "system": "http://loinc.org",
    "code": "76010-8",
    "display": "Oxygen assistance type",
    "text": "Alat Bantu Oksigenasi"
  },
  "valueCodeableConcept": {
    "system": "http://snomed.info/sct",
    "code": "371825009",
    "display": "Patient on oxygen",
    "text": "Nasal kanul"
  }
}
```

---

### Antropometri

| # | Komponen | LOINC | Tipe Nilai | Satuan |
|---|----------|-------|-----------|--------|
| 11 | Tinggi/Panjang Badan | `8302-2` | `valueQuantity` | cm (`cm`) |
| 12 | Berat Badan | `29463-7` | `valueQuantity` | kg (`kg`) |
| 13 | BMI (*Body Mass Index*) | `39156-5` | `valueQuantity` | kg/m² (`kg/m2`) |
| 14 | BSA (*Body Surface Area*) | `8277-6` | `valueQuantity` | m² (`m2`) |
| 15 | BBI (*Ideal Body Weight*) | `8335-2` | `valueQuantity` | kg (`kg`) |

```json
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
```

---

## Ringkasan Seluruh Komponen

| # | Komponen | LOINC | Tipe Nilai | Satuan / Terminologi |
|---|----------|-------|-----------|---------------------|
| 1 | Tekanan Darah Sistolik | `8480-6` | `valueQuantity` | mmHg |
| 2 | Tekanan Darah Diastolik | `8462-4` | `valueQuantity` | mmHg |
| 3 | Suhu Tubuh | `8310-5` | `valueQuantity` | °C |
| 4 | MAP | `8478-0` | `valueQuantity` | mmHg |
| 5 | Nadi | `8867-4` | `valueQuantity` | kali/menit |
| 6 | Irama | `8884-9` | `valueCodeableConcept` | SNOMED CT |
| 7 | Pulsasi | `44974-4` | `valueCodeableConcept` | SNOMED CT |
| 8 | Pernapasan | `9279-1` | `valueQuantity` | kali/menit |
| 9 | SpO2 | `2708-6` | `valueQuantity` | % |
| 10 | Alat Bantu Oksigenasi | `76010-8` | `valueCodeableConcept` | SNOMED CT |
| 11 | Tinggi/Panjang Badan | `8302-2` | `valueQuantity` | cm |
| 12 | Berat Badan | `29463-7` | `valueQuantity` | kg |
| 13 | BMI | `39156-5` | `valueQuantity` | kg/m² |
| 14 | BSA | `8277-6` | `valueQuantity` | m² |
| 15 | BBI | `8335-2` | `valueQuantity` | kg |

---

## Referensi

- [FHIR R5 — Observation](https://hl7.org/fhir/R5/observation.html)
- [LOINC Search](https://loinc.org/search/)
- [SNOMED CT Browser](https://browser.ihtsdotools.org/)
- [UCUM — Unified Code for Units of Measure](https://ucum.org)
