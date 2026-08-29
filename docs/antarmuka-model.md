# Antarmuka Model — Kontrak yang Dituntut Pipeline

Repo ini menyertakan `model/reference_model.py` — decoder karakter-level yang
sengaja polos. Ia BUKAN arsitektur Veyra; ia titik colok. Arsitektur Anda sendiri
bisa langsung dipakai trainer selama memenuhi kontrak berikut.

## 1. Config (dataclass)

```python
@dataclass
class MyConfig:
    ukuran_ruang: int # ukuran ruang karakter — diisi trainer dari registry (codec.ukuran_ruang)
    pad_id: int       # diisi trainer dari registry (codec.pad_id)
    # ... field lain = kunci di blok `model:` pada configs/*.yaml
    grad_checkpoint: bool = False   # dibaca trainer untuk log
```

Trainer memvalidasi kunci yaml terhadap `fields(Config)` — kunci yang tidak
dikenal dibuang dengan peringatan. Jadi: field config Anda = kunci yaml Anda.

## 2. Model

```python
class MyModel(nn.Module):
    def __init__(self, cfg: MyConfig): ...
    # WAJIB: simpan config di atribut ini (dipakai sft_loss & checkpointing)
    self.cfg = cfg

    def forward(self, x):        # x: LongTensor [B, T] berisi ID karakter
        return logits            # FloatTensor [B, T, ukuran_ruang]

    def loss(self, x):           # objective pretraining
        # WAJIB SHIFTED: logits[:, :-1] vs x[:, 1:]
        # Loss tanpa shift = model belajar MENYALIN, bukan memprediksi —
        # ini kelas bug yang diam dan mahal. Uji: loss awal harus ~ln(ukuran ruang).
        ...
```

## 3. Aturan native yang tidak boleh dilanggar

- **Input = deretan ID karakter mentah** dari codec (1 karakter = 1 ID).
  Jangan menambahkan segmentasi/penggabungan apa pun sebelum pemetaan ID→vektor
  di dalam model — kalau Anda ingin kompresi, letakkan DI DALAM model sebagai
  bagian arsitektur.
- **Tanpa UNK**: ukuran_ruang datang dari registry; semua ID valid.
- **PAD**: tidak dilatih (ignore_index) dan tidak dilihat attention.
- **Checkpoint**: trainer menyimpan `model.state_dict()` + config dict + hash
  (atomic: tulis tmp → rename) — pastikan arsitektur Anda dapat direkonstruksi
  dari config-nya saja. Saat resume, trainer memverifikasi tiga hal dan GAGAL
  KERAS bila tak cocok (bukan peringatan): `registry_hash` (pin registry
  karakter ↔ bobot — bobot dilatih pada satu ruang karakter, memuatnya dengan
  registry lain = korupsi senyap), kesetaraan config penuh + `config_hash`
  (deteksi ckpt basi setelah skema berevolusi), dan `opt_kind` (state optimizer
  tidak portabel antar jenis optimizer).
- **Protokol alat pada pengalaman operasional (field legacy: SFT)**: placeholder `<S8>`/`<S9>` (panggilan) dan
  `<S10>`/`<S11>` (hasil) di content assistant disubstitusi loader menjadi ID
  sentinel tunggal — hanya di content assistant, tidak pernah di content user
  (anti-spoofing: user tak boleh bisa "menghasilkan sentinel" lewat teks).
  Kanal hasil-alat (`<S10>..<S11>`, termasuk isinya) TIDAK dilatih-prediksi
  (label IGNORE): hasil tool adalah suara sistem yang disuntik pipeline, bukan
  emisi model. Model belajar MENYALIN dari hasil yang hadir di konteks, bukan
  menghafal isinya — melatih kanal itu berarti mengajari model mengarang hasil
  beserta sumbernya.

## 4. Menghubungkan ke trainer

Satu baris di `training/train_unified_native.py`:

```python
from model.reference_model import NativeModel, NativeConfig, count_params
# ganti menjadi:
from model.arsitektur_anda import NativeModel, NativeConfig, count_params
```

(Ekspor alias dengan nama yang sama, atau ubah ketiga nama di trainer.)

## 5. Cara menilai model native (kacamata yang benar)

Model karakter-level punya ritme belajarnya sendiri. Metrik yang wajar dipantau:
bits/char (BPC) pada validasi — bukan membandingkan kurva Anda dengan kurva model
ber-segmentasi sub-kata; keduanya mengukur unit yang berbeda. Perilaku awal seperti
pengulangan pada teks yang digenerasi adalah fase belajar substrat, bukan bug decoding.
