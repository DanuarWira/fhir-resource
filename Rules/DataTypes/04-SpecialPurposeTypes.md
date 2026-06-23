# FHIR Special-Purpose Data Types

Tipe data *Special-Purpose* di FHIR didefinisikan secara unik untuk menangani mekanisme fungsional khusus sistem FHIR, yang secara konsep tidak tergolong General-Purpose maupun Metadata.

Berikut adalah tipe-tipe spesial utama:

## 1. Reference
Ini adalah salah satu tipe data paling penting di dalam struktur FHIR. Tipe ini digunakan untuk mengikat (membuat relasi antar) resource satu dengan yang lainnya.

* **reference**: Tautan mutlak (*absolute URL*) atau relatif (*relative URL*) yang menunjuk ke lokasi Resource lain.
* **type**: Jenis dari resource yang ditunjuk.
* **display**: Nama teks representasi dari rujukan tersebut.

```json
"patient": {
  "reference": "Patient/pat-12345",
  "type": "Patient",
  "display": "Budi Santoso"
}
```

## 2. CodeableReference
(Tipe data baru yang diperkenalkan di R5). Menggabungkan fleksibilitas antara memberikan kode statis (`CodeableConcept`) ATAU memberikan referensi ke resource definisi (`Reference`). Berguna saat sebuah konsep bisa direpresentasikan sebagai sekadar teks/kode standar, atau merujuk pada resource spesifik (seperti *Observation* atau *Condition*).

```json
"reason": [
  {
    "concept": {
      "coding": [
        { "system": "http://snomed.info/sct", "code": "386661006", "display": "Fever" }
      ]
    },
    "reference": {
      "reference": "Condition/fever-123"
    }
  }
]
```

## 3. Meta
Setiap resource di FHIR selalu memiliki atribut bawaan `.meta` yang bertipe data `Meta`. Meta digunakan untuk melacak riwayat pembaruan sistem dan penanda keamanan (security tags).

```json
"meta": {
  "versionId": "2",
  "lastUpdated": "2023-11-20T10:00:00Z",
  "profile": [
    "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
  ]
}
```

## 4. Narrative & xhtml
Resource FHIR selalu disarankan memiliki elemen *human-readable* (bisa dibaca langsung oleh manusia tanpa perlu *parsing* khusus). Ini disimpan di elemen `text` bertipe `Narrative`. `Narrative` tersusun atas status dan `div` yang bertipe `xhtml` murni.

```json
"text": {
  "status": "generated",
  "div": "<div xmlns=\"http://www.w3.org/1999/xhtml\"><b>Budi Santoso</b>, Laki-laki, Lahir 1990</div>"
}
```

## 5. Extension
Ekstensi adalah mekanisme FHIR untuk menambahkan field-field *custom* yang tidak diakomodir secara standar baku (contoh: "Agama" pada resource Patient di luar standar base FHIR).

```json
"extension": [
  {
    "url": "http://hl7.org/fhir/StructureDefinition/patient-religion",
    "valueCodeableConcept": {
      "text": "Islam"
    }
  }
]
```

## 6. Dosage
Tipe data kompleks dan spesifik yang membungkus seluruh set instruksi medis penakaran dosis (untuk `MedicationRequest` dan `MedicationDispense`). Meliputi instruksi rute, batas dosis, hingga waktu konsumsi.

```json
"dosageInstruction": [
  {
    "text": "Minum 1 tablet 3 kali sehari setelah makan.",
    "timing": {
      "repeat": { "frequency": 3, "period": 1, "periodUnit": "d" }
    },
    "route": {
      "coding": [ { "system": "http://snomed.info/sct", "code": "26643006" } ]
    },
    "doseAndRate": [
      {
        "doseQuantity": { "value": 1, "unit": "tablet" }
      }
    ]
  }
]
```

## 7. ElementDefinition
Digunakan untuk mendefinisikan *blue-print* dari properti sebuah field dalam sistem profil FHIR (`StructureDefinition`). Sangat jarang ditulis langsung di luar pembuatan Implementation Guide.
