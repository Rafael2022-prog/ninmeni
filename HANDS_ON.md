# NINMENI Hands-On

NINMENI Hands-On adalah program partisipasi dari **Laboratorium Terbuka NINMENI**.
Program ini memberi jalur untuk mempelajari, mereproduksi, memfalsifikasi, dan
mengembangkan bukti publik NINMENI. Ia bukan mekanisme model dan bukan nama lain
untuk Matrix Eco.

## Eksperimen pertama — invariant registry NMU

Eksperimen ini hanya menggunakan jalur Generasi 1 yang memang tersedia di repo.
Ia tidak mengklaim menguji arsitektur internal Veyra Nyra 98M.

### Kontrak

Untuk teks sah di ruang registry:

1. setiap karakter menghasilkan tepat satu ID;
2. tidak ada UNK atau fallback ID;
3. `decode(encode(teks)) == NFC(normalize_newlines(teks))`;
4. hash registry dicatat bersama hasil.

### Persiapan

```bash
python -m pip install -e .
```

### Jalankan

Buat berkas sementara `hands_on_roundtrip.py` di luar repo atau salin potongan ini
ke interpreter Python:

```python
from pathlib import Path
from nmu import NMUCodec

registry = Path("registry/nmu_v1.json")
codec = NMUCodec.load(registry)
text = "Saya belajar NINMENI.\nSatu karakter = satu ID."
ids = codec.encode(text)
decoded = codec.decode(ids)

assert len(ids) == len(codec.normalize(text))
assert decoded == codec.normalize(text)
assert codec.round_trip_ok(text)

print("status=PASS")
print(f"registry_sha256={codec.registry_hash}")
print(f"registry_version={codec.version}")
print(f"character_count={len(codec.normalize(text))}")
print(f"id_count={len(ids)}")
print(f"ids={ids}")
```

### Falsifier minimum

Eksperimen **FAIL** bila salah satu kondisi berikut terjadi:

- jumlah ID tidak sama dengan jumlah karakter hasil normalisasi;
- decode tidak menghasilkan teks canonical yang sama;
- karakter yang tidak terdaftar diam-diam dipetakan ke sebuah UNK;
- hash registry tidak dapat ditentukan;
- hasil hanya diperoleh setelah registry atau kode diubah tanpa dicatat.

Error terklasifikasi untuk karakter di luar ruang terkurasi bukan kegagalan
invariant. Itu adalah perilaku fail-closed yang memang dikontrak codec.

## Reproduction receipt

Salin format berikut ke issue **Reproduction receipt**:

```text
experiment_id: NMU-G1-REGISTRY-ROUNDTRIP-V1
repository_revision:
registry_sha256:
python_version:
operating_system:
command_or_script:
input_text:
expected_result:
observed_result:
status: PASS | FAIL | INSTRUMENT_INVALID
deviation:
raw_artifact_link:
submitted_by:
```

Jangan mengubah status menjadi PASS hanya karena output terlihat masuk akal. Bila
instrumen, environment, atau provenance tidak cukup untuk mengambil keputusan,
gunakan `INSTRUMENT_INVALID` dan sertakan blocker-nya.

## Tangga partisipasi

1. **Pembaca** — pelajari kontrak dan batas repo.
2. **Reproducer** — jalankan eksperimen tanpa mengubah kontrak.
3. **Falsifier** — ajukan kondisi yang dapat membatalkan klaim mekanis.
4. **Kontributor** — kirim perbaikan, benchmark, dokumentasi, atau artefak.
5. **Penjaga bidang** — rawat satu wilayah secara berulang dengan provenance.

Mulai dari pekerjaan kecil. Satu eksperimen, satu artefak, satu batas klaim.
