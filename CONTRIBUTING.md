# Berkontribusi ke NINMENI

Kontribusi NINMENI mengikuti urutan: **bukti → kontrak → falsifier → perubahan →
regression**. Popularitas suatu gagasan, lamanya kode dipakai, atau training yang
sudah berjalan bukan bukti kebenaran mekanis.

## Sebelum mengirim kontribusi

- Pertahankan aksioma NMU: **satu karakter = satu ID**.
- Jangan menambahkan segmentasi sub-kata, UNK, atau fallback mapping.
- Bedakan jalur publik Generasi 1 dari arsitektur internal Generasi 2.
- Jangan menyatakan dashboard atau perubahan angka sebagai kapabilitas.
- Sertakan revision, command, environment, input, raw output, dan batas klaim.
- Jangan mengubah kontrak setelah melihat hasil.

## Bentuk kontribusi

- reproduction receipt;
- falsifier yang dapat dijalankan;
- laporan bug dengan reproduksi minimum;
- perbaikan dokumentasi yang dapat diverifikasi;
- benchmark dengan kelas runtime dan environment lengkap;
- pull request dengan regression yang relevan.

Temuan negatif diterima. Bila hasil berbeda dari dokumentasi, jangan memilih output
yang paling sesuai; laporkan seluruh kondisi yang dijalankan.

## Status bukti

Gunakan salah satu status berikut dalam issue atau pull request:

- `FACT` — langsung ditopang kode atau artefak terverifikasi;
- `DESIGN` — kontrak yang ditetapkan, belum otomatis terbukti;
- `HYPOTHESIS` — dugaan yang mempunyai falsifier;
- `REPRODUCED` — hasil telah direproduksi dengan provenance lengkap;
- `FALSIFIED` — klaim gagal pada falsifier yang sah;
- `UNKNOWN` — bukti belum cukup;
- `INSTRUMENT_INVALID` — instrumen tidak dapat menjawab pertanyaan.

## Pull request

Pull request harus menjelaskan:

1. masalah yang diselesaikan;
2. kontrak yang harus tetap benar;
3. file yang diubah;
4. falsifier atau regression yang dijalankan;
5. output lengkap yang relevan;
6. dampak terhadap kompatibilitas Generasi 1.

Perubahan yang menghapus atau mengubah perilaku publik harus menyertakan alasan
mekanis dan jalur migrasi. Jangan menambahkan compatibility branch tersembunyi.

## Batas istilah

Nama `sft` pada file dan argumen repo ini adalah provenance implementasi Generasi 1.
Ia tidak diratifikasi sebagai mekanisme belajar canonical NINMENI. Untuk pembahasan
baru, gunakan **pengalaman substrat** dan **pengalaman operasional**; jangan mengganti
nama fungsi legacy secara parsial bila itu merusak reproduktibilitas.
