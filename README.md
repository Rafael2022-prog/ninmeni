# NINMENI — Framework Model Bahasa Native Indonesia

**NINMENI** adalah kerangka riset dan rekayasa untuk membangun model bahasa yang
native terhadap bahasa Indonesia, dari lapisan paling dasar.

Hierarki: **framework NINMENI → paradigma NMU → model (contoh: Veyra)**.

- **NMU (ninmeni meaning unit)** — paradigma representasi: satu karakter = satu
  identitas tetap, di ruang karakter statis universal. Tanpa segmentasi sub-kata,
  tanpa UNK. Bahasa Indonesia hidup dari imbuhan; dengan membaca per karakter, akar
  kata selalu tampak utuh di setiap turunannya — model menemukan pola imbuhan sendiri.
- **Veyra** — model yang lahir dari kerangka ini. Generasi pertama: Veyra 75M.
  Generasi kedua: Veyra Nyra 98M, dimulai dari nol pada Agustus 2026 dan kini
  berada dalam evaluasi research-only dari checkpoint produksi immutable langkah 5.000.

> ### Repo ini adalah jalur GENERASI PERTAMA
>
> Kode dan ruang karakter di sini adalah jalur yang dipakai membangun **Veyra 75M**,
> dengan ruang karakter **±4.450** (`registry/nmu_v1.json`). Ia utuh dan berjalan
> untuk generasi itu.
>
> Generasi kedua memakai ruang karakter **10.240** dengan **format berkas registry
> yang berbeda**, sehingga codec di repo ini **tidak dapat memuatnya**. Perbedaannya
> bukan sekadar jumlah: 4.450 ID pertama tetap persis sama, tetapi berkas registry
> generasi kedua memakai skema `entries` — bukan `codepoints` seperti di sini.
>
> Perubahan inti lain pada generasi kedua: besaran yang menakar kerja tiap lapisan
> kini diturunkan sendiri di dalam paradigma NINMENI (**Ξ_EMYLTON**), menggantikan
> rumusan yang sebelumnya berasal dari luar NMU. Perubahan itu ada di arsitektur
> internal Veyra, yang memang tidak pernah dipublikasikan di repo ini (`model/`
> berisi decoder referensi polos — lihat bagian *Status & batas yang jujur*).
>
> Jalur generasi kedua akan diterbitkan setelah mekanisme, provenance, evaluasi, dan
> release gate-nya matang. Sampai saat itu, repo ini dipertahankan sebagai jalur
> Generasi 1 yang dapat digunakan dan direproduksi apa adanya. Keberadaan checkpoint
> internal Generasi 2 bukan izin untuk memindahkan klaim kapabilitas ke repo ini.

## Isi repo
```
nmu/         codec NMU (paket paradigma) (encode/decode 1 karakter = 1 ID, registry-driven)
registry/    ruang karakter statis (nmu_v1.json)
model/       model REFERENSI (decoder polos) — titik colok arsitektur Anda; BUKAN arsitektur Veyra
training/    loop historis Generasi 1: substrat + pengalaman operasional (field legacy SFT)
pipeline/    build_shards.py — teks JSONL -> shard ids (mmap-able)
configs/     contoh konfigurasi
docs/        panduan kurikulum data
examples/    contoh format pengalaman substrat dan operasional Generasi 1
```

## Mulai cepat
1. Siapkan korpus JSONL `{"teks": "..."}` (lihat `examples/curriculum/`).
2. Shardize: `python pipeline/build_shards.py --inputs korpus.jsonl --out data/shards`
3. Latih: `python -m training.train_unified_native --config configs/contoh_75m.yaml \
   --shards data/shards/train --sft data/sft_train.jsonl --registry registry/nmu_v1.json`

Nama argumen dan modul `sft` pada perintah di atas adalah **provenance implementasi
Generasi 1**, bukan nama mekanisme belajar canonical NINMENI. Di dalam ontologi
NINMENI, berkas tersebut adalah aset pengalaman operasional; Kristalisasi adalah
inti kausal belajar pada jalur Generasi 2. Nama legacy dipertahankan di repo ini agar
contoh dan checkpoint Generasi 1 tetap reproduktif.

## Mulai dengan NINMENI Hands-On

**NINMENI Hands-On** adalah program partisipasi dari Laboratorium Terbuka NINMENI.
Mulai dari eksperimen kecil yang benar-benar tersedia di jalur publik ini:

1. muat registry NMU Generasi 1;
2. buktikan satu karakter menghasilkan tepat satu ID;
3. buktikan `decode(encode(teks))` sama dengan hasil normalisasi canonical;
4. simpan revision, hash registry, environment, command, dan hasil;
5. kirim reproduction receipt melalui issue.

Panduan lengkap: [`HANDS_ON.md`](HANDS_ON.md). Aturan kontribusi dan batas bukti:
[`CONTRIBUTING.md`](CONTRIBUTING.md).

## Status & batas yang jujur
Proyek ini dibuka bertahap. Kode di sini adalah pipeline yang benar-benar dipakai
membangun **Veyra 75M — generasi pertama** — bukan kode demonstrasi. Yang TIDAK ada
di repo ini: bobot model (untuk generasi mana pun), arsitektur internal Veyra
(model/ berisi referensi polos sebagai titik colok — lihat docs/antarmuka-model.md),
korpus pelatihan, dan catatan riset internal. Evaluasi menyeluruh menunggu
dokumentasi teknis rilis penuh.

Batas yang perlu dinyatakan terus terang: repo ini **tidak** mengikuti generasi kedua.
Ia tidak usang untuk apa yang dikerjakannya, tetapi ia juga bukan versi lama dari
jalur yang sedang berjalan sekarang — keduanya sudah bercabang. Kalau Anda memakai
repo ini, Anda memakai jalur generasi pertama, dan itu memang tetap sah.

## Kontribusi & lisensi
Kode di repo ini dilisensikan di bawah **Apache License 2.0** (lihat `LICENSE`).
Kontribusi dipersilakan melalui issue / pull request. Temuan yang berbeda atau
falsifier yang membatalkan dugaan diperlakukan sebagai kontribusi, bukan kegagalan
komunitas.

## Kontak
Emylton Leunufna — pencipta framework NINMENI & paradigma NMU — `emylleons8@gmail.com`
