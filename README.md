# Tasbih Digital — PWA + Build APK/AAB via GitHub Actions

Struktur repo ini:

```
docs/                     → PWA (di-host lewat GitHub Pages)
android/twa-manifest.json → konfigurasi Bubblewrap (TWA)
android/android.keystore  → kunci signing (JANGAN di-commit — sudah di .gitignore)
.github/workflows/        → workflow yang build APK & AAB otomatis
```

## 1. Buat repo & upload

1. Buat repository baru di GitHub (boleh publik atau privat), misalnya `tasbih-digital`.
2. Upload/push semua isi folder ini ke repo tersebut (root repo = isi zip ini).

## 2. Aktifkan GitHub Pages

Settings → Pages → **Source: Deploy from a branch** → Branch: `main`, folder: `/docs` → Save.

Tunggu 1–2 menit, lalu catat URL yang muncul, biasanya:
`https://USERNAME.github.io/tasbih-digital/`

## 3. Ganti semua placeholder

Cari-ganti `GITHUB_USERNAME` dan `REPO_NAME` di `android/twa-manifest.json` dengan username GitHub dan nama repo kamu yang sebenarnya. Juga boleh ganti `packageId` (`id.tasbihdigital.app`) kalau mau id paket sendiri — formatnya kebalikan domain, unik, dan **tidak bisa diganti lagi setelah pertama kali upload ke Play Store**.

Commit & push perubahan ini.

## 4. Tambahkan GitHub Secrets

Repo Settings → Secrets and variables → Actions → **New repository secret**, buat 3 secret:

| Nama secret | Isi |
|---|---|
| `ANDROID_KEYSTORE_BASE64` | isi file `android.keystore.base64` yang saya sertakan terpisah |
| `ANDROID_KEYSTORE_PASSWORD` | password keystore yang saya berikan di chat |
| `ANDROID_KEY_PASSWORD` | sama dengan password di atas |
| `ANDROID_KEY_ALIAS` | `android` |

**Simpan `android.keystore` dan passwordnya di tempat aman di luar repo (password manager).** Kalau hilang, kamu tidak akan bisa merilis update untuk aplikasi yang sama di Play Store lagi — harus bikin aplikasi baru dari nol.

## 5. Jalankan build

Tab **Actions** di repo → pilih workflow "Build Android APK & AAB" → **Run workflow**.
Setelah selesai (±3–5 menit), scroll ke bagian **Artifacts** pada hasil run tersebut → unduh `tasbih-digital-android-*.zip`, isinya:
- `app-release-signed.apk` — untuk instal manual/testing di HP
- `app-release-bundle.aab` — untuk diupload ke Google Play Console

Workflow ini otomatis jalan lagi setiap kali ada push yang mengubah folder `android/` atau `docs/`.

## 6. Verifikasi Digital Asset Links

File `docs/.well-known/assetlinks.json` sudah saya isi dengan fingerprint SHA-256 dari keystore kamu, supaya setelah folder `docs/` live di GitHub Pages, Android bisa memverifikasi bahwa APK dan situs webnya satu pemilik (jadi TWA tampil tanpa address bar). Tidak perlu diubah, kecuali kamu generate keystore baru.

## 7. Upload ke Play Console

Ikuti langkah pendaftaran developer & pengisian listing seperti yang sudah dijelaskan sebelumnya, lalu upload `app-release-bundle.aab` di bagian rilis (Internal testing dulu disarankan).
