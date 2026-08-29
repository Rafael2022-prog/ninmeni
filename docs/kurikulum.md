# Kurikulum Data NINMENI — Panduan Ringkas Generasi 1

> **Batas istilah:** dokumen ini mempertahankan nama `pretraining` dan `SFT` karena
> keduanya adalah nama file, field, dan fungsi pada jalur publik Generasi 1. Dalam
> ontologi NINMENI yang berlaku, keduanya dibaca sebagai **pengalaman substrat** dan
> **pengalaman operasional**; SFT bukan mekanisme belajar canonical NINMENI.

Dua jalur data, dua peran yang berbeda dan tidak boleh dicampur:

## 1. Pengalaman substrat (field legacy: pretraining)
- Isi: teks aktual (prosa komunikasi nyata) berbahasa Indonesia — artikel, dokumentasi,
  narasi, kode dengan penjelasan. Format: JSONL `{"teks": "..."}` (lihat
  `examples/curriculum/pretraining_contoh.jsonl`).
- Prinsip: substrat belajar dari BAHASA HIDUP. Format meta-linguistik (tabel kamus,
  daftar lema, anotasi kelas kata) bukan corpus — kamus adalah alat kurasi, bukan data.
- Setiap sumber wajib berlisensi jelas dan tercatat (URL + lisensi + hash) di manifest
  Anda sendiri. Jangan gunakan data yang tidak boleh Anda gunakan.
- Konten pengetahuan dunia (fakta, statistik, budaya) masuk ke jalur INI — bukan ke SFT.

## 2. Pengalaman operasional (field legacy: SFT)
- Isi: contoh percakapan/tugas dalam format turns (lihat `examples/curriculum/sft_contoh.jsonl`).
- Peran: mengajarkan BENTUK berinteraksi (format jawaban, protokol tool, gaya) — bukan
  menjejalkan fakta. Fakta yang hanya ada di SFT cenderung rapuh.
- Penalaran bertahap diajarkan sebagai LINTASAN: enumerasi langkah searah penulisan,
  jawaban = kelanjutan alami dari langkahnya (lihat contoh `reason`). Penalaran juga
  bisa diberi kanal eksplisit lewat field `think` pada turn assistant (lihat contoh
  `reason_think`) — isinya dilatih di antara sentinel THINK/ENDTHINK.
- Protokol tool ditulis sebagai placeholder di content assistant:
  `<S8>{"tool": "...", "q": "..."}<S9>` untuk panggilan, lalu `<S10>{hasil}<S11>`
  untuk hasil (lihat contoh `tool`). Loader mensubstitusinya menjadi ID sentinel
  tunggal; span hasil (`<S10>..<S11>`) tidak dilatih-prediksi — model belajar
  memanggil dan menyandarkan jawaban pada hasil, bukan menghafal isi hasil.
  Rincian di `docs/antarmuka-model.md`.

## 3. Asas Prosedur-Terhitung (anti-hafalan)

Segala pengalaman operasional harus diuji pada varian held-out, bukan dinilai dari
kemampuannya mengulang contoh latih.
Cara mencapainya bukan slogan, melainkan disiplin builder:

- **Jawaban = fungsi dari input.** Setiap sample dihasilkan builder/oracle yang
  MENGHITUNG jawaban dari isi pertanyaan (aritmetika dieksekusi, hitungan
  diverifikasi kode), bukan daftar pasangan tanya-jawab yang ditulis tangan.
- **Guru tanpa kebiasaan.** Variasikan bentuk instruksi, urutan, distraktor,
  posisi jawaban, panjang, dan gaya — supaya model belajar PROSEDUR-nya, bukan
  templat permukaannya.
- **Kelulusan hanya di varian held-out.** Sisihkan varian/kombinasi yang 0×
  muncul di data latih; ukur transfer di sana. Skor tinggi pada bentuk yang
  pernah dilihat tidak membuktikan apa-apa — pantau selisih seen/unseen.
- **Jangan menjejalkan fakta lewat volume.** Mengulang-ulang jawaban yang sama
  memperbesar hafalan, bukan pemahaman; fakta dunia tetap milik jalur pretraining.

## Prinsip lintas-jalur
- 1 karakter = 1 ID: registry karakter statis (`registry/nmu_v1.json`) berlaku untuk
  SEMUA data. Teks yang memuat karakter di luar registry tidak dipaksa masuk — catat
  dan putuskan secara sadar (normalisasi terdokumentasi, atau buang utuh).
- Tanpa UNK: kegagalan encode = masalah validasi data, bukan sesuatu yang ditambal.
- Evaluasi model native dibaca dengan kacamata native: model karakter-level punya
  ritme belajar sendiri; jangan menilai dengan ekspektasi paradigma lain.
