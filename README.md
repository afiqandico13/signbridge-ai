# SignBridge AI 🤟

> Sistem penerjemah bahasa isyarat (BISINDO) secara real-time menggunakan MediaPipe + LSTM + Claude AI

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?logo=tensorflow)
![MediaPipe](https://img.shields.io/badge/MediaPipe-0.10-green)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

## Tentang Proyek

SignBridge AI mendeteksi gerakan tangan dari kamera secara real-time, mengenali gesture bahasa isyarat Indonesia (BISINDO), lalu menerjemahkannya menjadi kalimat Bahasa Indonesia yang natural menggunakan Claude AI.

```
Kamera → MediaPipe (keypoint) → LSTM (kenali gesture) → Claude AI (terjemahkan) → Teks + Suara
```

### Gesture yang Didukung (v1)

| | | | |
|---|---|---|---|
| halo | terima kasih | tolong | maaf |
| ya | tidak | nama saya | apa kabar |
| baik | selamat pagi | selamat siang | selamat malam |
| sampai jumpa | saya tidak mengerti | bisa ulangi | |

---

## Struktur Proyek

```
signbridge-ai/
├── README.md
├── requirements.txt
├── .gitignore
├── models/
│   ├── hand_lstm_model.h5      ← model LSTM terlatih
│   └── label_encoder.pkl       ← encoder kelas gesture
└── src/
    ├── signbridge_v4.py        ← aplikasi utama (real-time)
    ├── collect_data.py         ← kumpul data gesture via webcam
    ├── train_model.py          ← latih model LSTM
    └── extract_keypoints.py    ← ekstrak keypoint dari video
```

---

## Cara Install

### 1. Clone repo

```bash
git clone https://github.com/<username>/signbridge-ai.git
cd signbridge-ai
```

### 2. Buat virtual environment (disarankan)

```bash
# Anaconda
conda create -n signbridge python=3.10
conda activate signbridge

# atau venv biasa
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # Linux/Mac
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

## Cara Pakai

### Jalankan Aplikasi Utama

```bash
cd src
python signbridge_v4.py
```

Saat pertama kali jalan, program akan meminta API key (opsional untuk terjemahan):

```
Masukkan API key (atau Enter untuk skip):
```

> Tanpa API key, aplikasi tetap bisa mendeteksi gesture tapi tidak akan menerjemahkan ke kalimat natural.

### Kontrol Keyboard

| Tombol | Fungsi |
|--------|--------|
| `SPACE` | — |
| `C` | Hapus buffer gesture |
| `T` | Terjemahkan manual |
| `S` | Ucapkan teks (TTS) |
| `P` | Pause / Resume |
| `E` | Mode edit label |
| `W` | Simpan transkrip |
| `Q` | Keluar |

---

## Setup API Key (Opsional)

Untuk fitur terjemahan ke kalimat natural, diperlukan API key. Pilih salah satu:

**Opsi A — OpenRouter (gratis tersedia):**
```
https://openrouter.ai
```

**Opsi B — Anthropic langsung:**
```
https://console.anthropic.com
```

Setelah dapat key, buat file `.env` di folder `src/`:

```
# .env
OPENROUTER_API_KEY=sk-or-v1-xxxx
# atau
ANTHROPIC_API_KEY=sk-ant-xxxx
```

---

## Latih Model Sendiri

Jika ingin melatih model dengan gesture baru:

### Langkah 1 — Kumpulkan data

```bash
cd src
python collect_data.py
```

Kontrol saat rekam:
- `SPACE` — mulai rekam
- `N` — gesture berikutnya
- `R` — ulangi sesi
- `D` — hapus semua data gesture ini
- `Q` — keluar & simpan

Target: **120 sampel per gesture**

### Langkah 2 — Training

```bash
python train_model.py
```

Output:
```
models/hand_lstm_model.h5
models/label_encoder.pkl
models/training_report.txt
```

### Langkah 3 — Jalankan

```bash
python signbridge_v4.py
```

---

## Arsitektur Model

```
Input: sekuens 30 frame × 252 fitur
       (126 keypoint tangan kiri+kanan + 126 delta antar frame)
         ↓
LSTM(128) → BatchNorm → LSTM(64) → BatchNorm
         ↓
    Attention layer
         ↓
  Dense(128) → Dense(64) → Softmax(n_kelas)
```

| Komponen | Detail |
|----------|--------|
| Deteksi tangan | MediaPipe Hands (21 landmark × 3 koordinat × 2 tangan) |
| Model | LSTM 2 lapis + Attention |
| Optimizer | Adam (lr=0.001) |
| Augmentasi | Noise + time-shift (3×) |
| Terjemahan | Claude claude-sonnet via OpenRouter / Anthropic API |

---

## Requirements

```
tensorflow>=2.10
mediapipe>=0.10
opencv-python
numpy
scikit-learn
joblib
pyttsx3
```

Install semua:

```bash
pip install -r requirements.txt
```

---

## Dataset

Proyek ini juga menyertakan data preprocessing dari dataset **WLASL100** (Word-Level American Sign Language) sebagai referensi pengembangan.

> Data mentah dan video tidak di-include dalam repo. Jalankan `collect_data.py` untuk merekam gesture BISINDO sendiri.

---

## Kontribusi

Pull request dan issue sangat disambut! Beberapa area yang bisa dikembangkan:

- Tambah gesture BISINDO baru
- Peningkatan akurasi model (Transformer, dll)
- UI berbasis web / mobile
- Dukungan multi-bahasa

---

## Lisensi

MIT License — bebas digunakan untuk keperluan edukasi dan penelitian.

---

<p align="center">
  Dibuat dengan ❤️ untuk komunitas Tuli Indonesia
</p>