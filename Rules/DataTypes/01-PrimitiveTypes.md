# FHIR Primitive Data Types

Tipe data primitif adalah tipe data dasar di FHIR yang biasanya hanya berisi satu buah nilai (value) tunggal (misal: string, angka, boolean). Dalam struktur JSON FHIR, tipe primitif direpresentasikan langsung sebagai pasangan kunci-nilai (key-value pair) standar JSON. 

FHIR memiliki ekstensi khusus untuk tipe data primitif, sehingga tipe primitif juga bisa memiliki atribut internal yang diawali dengan `_` (underscore). Misalnya elemen `name` (string) bisa memiliki ekstensi berupa `_name`.

Berikut adalah daftar tipe data primitif utama beserta contohnya di dalam JSON FHIR:

## 1. boolean
Nilai kebenaran logika (hanya `true` atau `false`, tanpa tanda kutip).

```json
{
  "active": true
}
```

## 2. string
Teks biasa berupa karakter Unicode dengan panjang hingga 1MB.

```json
{
  "name": "Budi Santoso"
}
```

## 3. integer, integer64, unsignedInt & positiveInt
- `integer`: Bilangan bulat 32-bit bertanda (rentang -2,147,483,648 hingga 2,147,483,647).
- `integer64`: Bilangan bulat 64-bit bertanda (sangat besar, diperkenalkan di FHIR R5). Dalam JSON, `integer64` direpresentasikan sebagai string (dengan tanda kutip) untuk menghindari masalah presisi di JavaScript/JSON parser.
- `unsignedInt`: Bilangan bulat non-negatif (>= 0).
- `positiveInt`: Bilangan bulat positif (>= 1).

```json
{
  "sequence": 1,
  "count": 5,
  "veryLargeNumber": "9223372036854775807" 
}
```

## 4. decimal
Bilangan pecahan rasional. Penting diperhatikan bahwa FHIR sangat peduli pada "presisi" yang ditulis (contoh: `2.0` secara klinis berbeda dengan `2.00`). Ditulis tanpa tanda kutip di JSON.

```json
{
  "value": 36.5
}
```

## 5. uri, url, canonical, uuid, oid
Tipe string khusus yang formatnya harus divalidasi.
- `uri`: Uniform Resource Identifier.
- `url`: Uniform Resource Locator (sub-tipe dari uri).
- `canonical`: URI merujuk ke suatu resource FHIR berdasarkan canonical URL.
- `uuid`: Universally Unique Identifier, contoh: `urn:uuid:53fefa32-fcbb-4ff8-8a92-55ee120877b7`.
- `oid`: Object Identifier, contoh: `urn:oid:1.2.36.146.595.217.0.1`.

```json
{
  "system": "http://loinc.org",
  "url": "http://hl7.org/fhir/StructureDefinition/Patient"
}
```

## 6. base64Binary
Deretan byte biner (seperti gambar, PDF) yang di-*encode* menjadi format Base64 (berupa string).

```json
{
  "data": "JVBERi0xLjQKJcOkw7zDtsOUCjEgMCBvYmoKPDwv..."
}
```

## 7. instant, date, dateTime, time
Format waktu dan tanggal (di-encode sebagai string berstandar ISO 8601).
- `instant`: Waktu spesifik yang absolut, wajib mencantumkan detik dan zona waktu (contoh: `2015-02-07T13:28:17.239+02:00`).
- `date`: Tanggal, bisa parsial: tahun (`YYYY`), tahun-bulan (`YYYY-MM`), atau lengkap (`YYYY-MM-DD`).
- `dateTime`: Gabungan tanggal dan waktu. Bisa parsial atau lengkap. Jika ada waktu, biasanya zona waktu dicantumkan.
- `time`: Waktu dalam satu hari (`hh:mm:ss`).

```json
{
  "birthDate": "1990-10-25",
  "effectiveDateTime": "2023-11-05T08:30:00Z",
  "valueTime": "14:30:00"
}
```

## 8. code
Sama seperti `string`, tetapi nilai di dalamnya hanya boleh terdiri dari huruf, angka, tanda hubung (`-`), dan titik (`.`). Tipe ini biasanya merujuk pada suatu item dalam struktur kamus tertentu (contoh: kode jenis kelamin `male`, status `active`).

```json
{
  "gender": "male",
  "status": "completed"
}
```

## 9. id
Sama seperti `string` dengan batasan panjang 64 karakter. Memiliki aturan karakter: huruf besar/kecil, angka, tanda hubung, dan titik. Biasanya digunakan sebagai *Identifier/ID* internal untuk suatu resource (misal: ID untuk `Patient/123`).

```json
{
  "id": "pat-12345"
}
```

## 10. markdown
Tipe string khusus yang kontennya mengikuti konvensi penulisan *Markdown* (mendukung pemformatan teks tebal, miring, link, dsb).

```json
{
  "description": "**Pasien** memiliki riwayat asma yang *persisten*."
}
```

---
> [!TIP]
> **Ekstensi Primitif**: Jika suatu tipe primitif tidak memiliki nilai nyata (misalnya karena datanya disembunyikan untuk kerahasiaan), FHIR mengizinkan tipe primitif dikirim melalui elemen "saudaranya" dengan garis bawah (`_`).

```json
{
  "birthDate": null,
  "_birthDate": {
    "extension": [
      {
        "url": "http://hl7.org/fhir/StructureDefinition/data-absent-reason",
        "valueCode": "masked"
      }
    ]
  }
}
```
