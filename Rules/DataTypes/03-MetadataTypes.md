# FHIR Metadata Types

Tipe data *Metadata* umumnya tidak sering ditemui di dalam resource transaksional klinis biasa (seperti *Patient* atau *Observation*), melainkan sangat sering ditemui pada tipe resource untuk standar *Knowledge Artifacts*, Aturan Klinis (*Clinical Quality Language*), dan Spesifikasi *Implementation Guide*.

Berikut adalah tipe-tipe data yang merepresentasikan metadata (termasuk dari pembaruan FHIR R5):

## 1. ContactDetail
Menyimpan informasi spesifik tentang individu atau organisasi beserta detail kontaknya.

```json
"contact": [
  {
    "name": "Acme Standard Committee",
    "telecom": [
      { "system": "email", "value": "standards@acme.org" }
    ]
  }
]
```

## 2. ExtendedContactDetail
Versi lebih komprehensif dari `ContactDetail` yang diperkenalkan di R5. Tipe ini mampu mencakup struktur `Address` dan rujukan organisasi yang lebih rinci (ContactPoint, Address, Organization).

```json
"contact": [
  {
    "purpose": {
      "coding": [
        { "system": "http://terminology.hl7.org/CodeSystem/contactentity-type", "code": "BILL" }
      ]
    },
    "name": [ { "text": "Billing Dept" } ],
    "telecom": [ { "system": "phone", "value": "555-1234" } ],
    "address": { "city": "Jakarta", "country": "ID" },
    "organization": { "reference": "Organization/billing-inc" }
  }
]
```

## 3. Contributor
Informasi mengenai partisipan yang memberikan sumbangsih pembuatan pada sebuah spesifikasi data.

```json
"contributor": [
  {
    "type": "author",
    "name": "Dr. Spesialis Anak"
  }
]
```

## 4. DataRequirement
Mendefinisikan kriteria spesifik pengambilan atau filter suatu resource dari sistem (sering dipakai di *Clinical Decision Support*).

```json
"dataRequirement": [
  {
    "type": "Observation",
    "profile": [ "http://hl7.org/fhir/StructureDefinition/vitalsigns" ]
  }
]
```

## 5. RelatedArtifact
Mendeskripsikan referensi ke bahan referensi atau rujukan luar (publikasi, dokumen pedoman).

```json
"relatedArtifact": [
  {
    "type": "citation",
    "display": "WHO Guidelines on Nutrition"
  }
]
```

## 6. UsageContext
Menjelaskan konteks pengguna target untuk sebuah resource definisional. Bisa berbasis usia, lokasi geografis, ras, dll.

```json
"useContext": [
  {
    "code": { "system": "http://terminology.hl7.org/CodeSystem/usage-context-type", "code": "focus" },
    "valueCodeableConcept": { "text": "Pediatric Patients" }
  }
]
```

## 7. ParameterDefinition
Mendeskripsikan parameter (input/output) untuk mendeskripsikan *Operations* di dalam FHIR (*OperationDefinition*).

```json
"parameter": [
  {
    "name": "patientId",
    "use": "in",
    "min": 1,
    "max": "1",
    "type": "id"
  }
]
```

## 8. Expression
Menampung sintaks bahasa pemrograman spesifik yang bisa dieksekusi mesin, seperti FHIRPath atau CQL.

```json
"expression": {
  "description": "Menghitung umur berdasarkan birthDate",
  "language": "text/fhirpath",
  "expression": "Patient.birthDate.ageInYears()"
}
```

## 9. TriggerDefinition
Mendefinisikan kondisi yang memicu (*trigger*) sebuah urutan langkah (workflow) dieksekusi.

```json
"trigger": [
  {
    "type": "named-event",
    "name": "Admission-Event"
  }
]
```

## 10. Availability
(Tipe data baru di R5) Mendeskripsikan slot waktu ketersediaan seseorang, lokasi, atau layanan kesehatan untuk membuat janji temu.

```json
"availability": {
  "availableTime": [
    {
      "daysOfWeek": ["mon", "tue", "wed"],
      "availableStartTime": "08:00:00",
      "availableEndTime": "16:00:00"
    }
  ]
}
```

## 11. MonetaryComponent
(Tipe data baru di R5) Komponen keuangan yang mendeskripsikan rincian harga, pajak, diskon, atau faktor moneter lainnya pada layanan medis.

```json
"component": [
  {
    "type": "base",
    "amount": {
      "value": 150000.00,
      "currency": "IDR"
    }
  },
  {
    "type": "tax",
    "factor": 0.11
  }
]
```

## 12. VirtualServiceDetail
(Tipe data baru di R5) Menyediakan detail mengenai koneksi dan alamat untuk layanan virtual seperti *telemedicine* atau pertemuan konferensi video.

```json
"virtualService": [
  {
    "channelType": {
      "coding": [
        { "system": "http://terminology.hl7.org/CodeSystem/virtual-service-type", "code": "zoom" }
      ]
    },
    "addressUrl": "https://zoom.us/j/123456789",
    "additionalInfo": [ "Password: 123" ]
  }
]
```
