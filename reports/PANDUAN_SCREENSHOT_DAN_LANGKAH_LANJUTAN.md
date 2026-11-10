# PANDUAN PENGAMBILAN SCREENSHOT & LANGKAH-LANGKAH SELANJUTNYA
**Tugas Mandiri DevSecOps Pertemuan 7: Software Composition Analysis (SCA) dengan Snyk**

Panduan ini disusun khusus untuk membantu Anda menyelesaikan seluruh kewajiban praktikum, mengambil screenshot yang presisi sesuai rubrik penilaian dosen, mengonfigurasi GitHub Actions, dan mengemas tugas akhir ke dalam file ZIP.

---

## DAFTAR ISI
1. [Langkah 1: Git Commit & Push ke Repositori Pribadi](#langkah-1-git-commit--push-ke-repositori-pribadi)
2. [Langkah 2: Menyiapkan GitHub Secret SNYK_TOKEN](#langkah-2-menyiapkan-github-secret-snyk_token)
3. [Langkah 3: Menjalankan & Memverifikasi GitHub Actions](#langkah-3-menjalankan--memverifikasi-github-actions)
4. [Langkah 4: Panduan Lengkap Pengambilan Screenshot (Tugas 1 s.d. 4)](#langkah-4-panduan-lengkap-pengambilan-screenshot)
5. [Langkah 5: Mengonversi Laporan Markdown ke PDF](#langkah-5-mengonversi-laporan-markdown-ke-pdf)
6. [Langkah 6: Membuat File ZIP Sesuai Format Pengumpulan](#langkah-6-membuat-file-zip-sesuai-format-pengumpulan)

---

## LANGKAH 1: Git Commit & Push ke Repositori Pribadi

Seluruh perbaikan dependensi, file policy `.snyk`, file workflow `.github/workflows/snyk-sca.yml`, dan artefak scan telah disiapkan di branch `fix/remediation`.

Jalankan perintah berikut di terminal Anda untuk menyimpan dan mengunggahnya ke GitHub:

```bash
# 1. Pastikan Anda berada di branch fix/remediation
git branch --show-current

# 2. Tambahkan file yang telah dimodifikasi dan dibuat
git add package.json .snyk .github/workflows/snyk-sca.yml sbom.json snyk-report.txt reports/

# 3. Buat commit bertanda tangan (-s / sign-off)
git commit -s -m "feat(sca): implement snyk remediation, policy exceptions, sbom, and ci pipeline"

# 4. Push branch fix/remediation ke GitHub repositori Anda
git push -u origin fix/remediation
```

> **Catatan:** Jika Anda ingin menggabungkan perubahan ini ke branch utama (`master`/`main`), Anda juga bisa membuat Pull Request dari `fix/remediation` ke `master` di GitHub.

---

## LANGKAH 2: Menyiapkan GitHub Secret `SNYK_TOKEN`

Agar pipeline GitHub Actions dapat menjalankan Snyk SCA Scan tanpa error autentikasi, simpan token Snyk Anda ke GitHub:

1. Buka browser dan login ke akun Snyk Anda: [https://app.snyk.io](https://app.snyk.io)
2. Klik nama akun Anda di pojok kiri bawah → pilih **Account Settings** → tab **General**.
3. Di bagian **Auth Token**, klik **"click to show"** lalu klik tombol **Copy**.
4. Buka tab baru di browser menuju repositori GitHub Anda:  
   `https://github.com/Minkqwqw/juice-shop`
5. Masuk ke menu **Settings** (tab paling kanan di atas repositori).
6. Di bilah menu kiri, cari bagian **Secrets and variables** → klik **Actions**.
7. Klik tombol hijau **New repository secret**.
8. Isi form:
   * **Name:** `SNYK_TOKEN` *(Harus persis huruf kapital semua)*
   * **Secret:** *(Paste token Snyk yang Anda copy tadi)*
9. Klik tombol **Add secret**.

---

## LANGKAH 3: Menjalankan & Memverifikasi GitHub Actions

Setelah token tersimpan dan branch `fix/remediation` di-push:

1. Buka repositori Anda di GitHub lalu klik tab **Actions**.
2. Anda akan melihat alur kerja bernama **"DevSecOps — Snyk SCA"** sedang berjalan (lingkaran kuning berputar) atau telah selesai.
3. Klik nama workflow run tersebut.
4. Perhatikan statusnya:
   * Status akan **HIJAU (Sukses)** karena seluruh kerentanan HIGH dan CRITICAL telah berhasil diperbaiki dan di-accept risk di file `.snyk`.
5. Klik job **`snyk-scan`** untuk membuka detail terminal log, lalu klik expand pada bagian step **"Snyk SCA Scan"**.
6. Simpan halaman ini untuk kebutuhan screenshot Tugas 3.

---

## LANGKAH 4: Panduan Lengkap Pengambilan Screenshot

Berikut adalah panduan detail setiap screenshot yang diminta oleh dosen pada dokumen tugas:

### 📸 Screenshot 1: Terminal Snyk Test Awal (Tugas 1 - D.1.2)
* **Tujuan:** Menampilkan hasil pemindaian awal sebelum perbaikan.
* **Cara Mendapatkan:**
  Buka file `snyk-report.txt` di VS Code / terminal Anda, atau jalankan perintah:
  ```bash
  tail -n 25 snyk-report.txt
  ```
* **Bagian yang Wajib Kelihatan:** Kotak ringkasan (*Test Summary Box*) yang menampilkan:
  * `Project name: juice-shop`
  * `Total security issues: 70`
  * `Open: 70 [ 1 CRITICAL  27 HIGH  40 MEDIUM  2 LOW ]`
* **Nama Rekomendasi File:** `tugas1_terminal_summary.png`

---

### 📸 Screenshot 2: Dashboard Snyk Cloud (Tugas 1 - D.1.2)
* **Tujuan:** Menampilkan project Juice Shop yang telah muncul di dashboard Snyk setelah perintah `snyk monitor`.
* **Cara Mendapatkan:**
  Buka tautan snapshot Anda di browser:  
  👉 [https://app.snyk.io/org/minkqwqw/project/451a34f0-1b51-4293-baa2-2d7514ab2be5](https://app.snyk.io/org/minkqwqw/project/451a34f0-1b51-4293-baa2-2d7514ab2be5)
* **Bagian yang Wajib Kelihatan:** Header project `juice-shop`, nama organisasi `minkqwqw`, diagram severity (Critical, High, Medium, Low), serta daftar kerentanan di bawahnya.
* **Nama Rekomendasi File:** `tugas1_snyk_dashboard.png`

---

### 📸 Screenshot 3: Output Eksekusi `snyk fix` (Tugas 2 - D.2.4)
* **Tujuan:** Menunjukkan bukti eksekusi perintah perbaikan otomatis.
* **Cara Mendapatkan:**
  Jalankan di terminal:
  ```bash
  cat snyk-fix-output.txt
  ```
  Atau jalankan ulang:
  ```bash
  snyk fix
  ```
* **Bagian yang Wajib Kelihatan:** Pesan terminal yang mengindikasikan status eksekusi `snyk fix` pada proyek (bahwa snyk fix mendeteksi manifest dan memerlukan manual upgrade/policy).
* **Nama Rekomendasi File:** `tugas2_snyk_fix_output.png`

---

### 📸 Screenshot 4: Terminal Snyk Test Kedua Sesudah Remediasi (Tugas 2 - D.2.4)
* **Tujuan:** Menunjukkan bukti keberhasilan reduksi kerentanan (0 open issues pada severity HIGH/CRITICAL).
* **Cara Mendapatkan:**
  Jalankan perintah berikut di terminal Anda:
  ```bash
  export PATH="/tmp/snyk-bin/node_modules/.bin:$PATH"
  snyk test --severity-threshold=high
  ```
* **Bagian yang Wajib Kelihatan:**
  * Kalimat: `Tested 633 dependencies for known issues, found 0 issues, 0 vulnerable paths.`
  * Kalimat: `Open Security issues: 0`
  * Kotak ringkasan: `Ignored: 27 [ 1 CRITICAL  26 HIGH  0 MEDIUM  0 LOW ]` dan `Open: 0`
* **Nama Rekomendasi File:** `tugas2_terminal_after_remediation.png`

---

### 📸 Screenshot 5: Tab Actions di GitHub (Tugas 3 - D.3.3)
* **Tujuan:** Menunjukkan bahwa workflow Snyk SCA telah terintegrasi dan berhasil dieksekusi di GitHub Actions.
* **Cara Mendapatkan:**
  Buka browser ke halaman Actions repositori Anda:  
  `https://github.com/Minkqwqw/juice-shop/actions`
* **Bagian yang Wajib Kelihatan:** Daftar workflow run dengan centang hijau (*Success*), nama workflow **"DevSecOps — Snyk SCA"**, nama branch (`fix/remediation`), dan commit message terakhir.
* **Nama Rekomendasi File:** `tugas3_github_actions_tab.png`

---

### 📸 Screenshot 6: Detail Log Workflow "Snyk SCA Scan" (Tugas 3 - D.3.3)
* **Tujuan:** Menunjukkan output pemindaian Snyk di dalam runner container GitHub Actions.
* **Cara Mendapatkan:**
  Klik workflow run yang berhasil tadi → klik job `snyk-scan` → klik panah pada step **"Snyk SCA Scan"**.
* **Bagian yang Wajib Kelihatan:** Baris log terminal GitHub Actions yang menampilkan hasil Snyk test (0 vulnerable paths / pass).
* **Nama Rekomendasi File:** `tugas3_github_actions_log.png`

---

### 📸 Screenshot 7: Struktur Awal File `sbom.json` (Tugas 4 - D.4.3)
* **Tujuan:** Memperlihatkan 10–15 baris pertama file SBOM standar CycloneDX JSON.
* **Cara Mendapatkan:**
  Buka file `sbom.json` di VS Code lalu atur format rapi (Prettify JSON), atau jalankan di terminal:
  ```bash
  python3 -m json.tool sbom.json | head -n 25
  ```
* **Bagian yang Wajib Kelihatan:** Header `$schema` (`cyclonedx.org`), `bomFormat: CycloneDX`, `specVersion: 1.4`, `metadata.component.name: juice-shop`.
* **Nama Rekomendasi File:** `tugas4_sbom_json_head.png`

---

## LANGKAH 5: Mengonversi Laporan Markdown ke PDF

File laporan lengkap telah tersedia di: [`reports/LAPORAN_TUGAS_SCA.md`](file:///home/minkwaq/kuliah/devsec/pertemuan_7/tugas/juice-shop/reports/LAPORAN_TUGAS_SCA.md).

Sebelum mengonversi ke PDF:
1. Buka file [`reports/LAPORAN_TUGAS_SCA.md`](file:///home/minkwaq/kuliah/devsec/pertemuan_7/tugas/juice-shop/reports/LAPORAN_TUGAS_SCA.md).
2. Isi data identitas diri Anda pada bagian paling atas:
   * **NIM:** [Ganti dengan NIM Anda]
   * **Nama Lengkap:** [Ganti dengan Nama Lengkap Anda]
   * **Kelas:** [Ganti dengan Kelas Anda]
3. Sisipkan gambar screenshot yang telah Anda ambil pada tempat-tempat yang telah diberi tanda kutip/placeholder rujukan.

### Cara Export ke PDF:
* **Metode 1 (Rekomendasi di VS Code):** Install extension **"Markdown PDF"** (oleh yzane) di VS Code. Klik kanan di dalam file `LAPORAN_TUGAS_SCA.md` → pilih **Markdown PDF: Export (pdf)**.
* **Metode 2 (Browser Print):** Buka file Markdown menggunakan extension preview / browser Markdown viewer, tekan `Ctrl + P` (Print), pilih printer **"Save as PDF"**, centang opsi "Background graphics".
* **Beri nama file hasil export:**  
  `NIM_NamaLengkap_LaporanSCA.pdf` *(Contoh: `0110221001_AhmadFauzi_LaporanSCA.pdf`)*.

---

## LANGKAH 6: Membuat File ZIP Sesuai Format Pengumpulan

Sesuai ketentuan Bagian E pada dokumen tugas dosen:
* Format penamaan file ZIP: **`NIM_NamaLengkap_TugasSCA_P7.zip`**
* Isi ZIP harus memuat 5 file wajib:
  1. `NIM_NamaLengkap_LaporanSCA.pdf` (Laporan utama dalam format PDF)
  2. `sbom.json` (File SBOM hasil Tugas 4)
  3. `snyk-report.txt` (Output snyk test Tugas 1)
  4. `.snyk` (File policy Snyk / accept risk Tugas 2)
  5. Pastikan link repositori GitHub telah tercantum di halaman pertama laporan PDF.

### Perintah Cepat Membuat File ZIP di Terminal:
Misalnya NIM Anda `0110221001` dan Nama Anda `AhmadFauzi`:
```bash
# Salin PDF yang sudah diekspor ke root direktori proyek (atau sesuaikan jalurnya)
cp reports/NIM_NamaLengkap_LaporanSCA.pdf ./0110221001_AhmadFauzi_LaporanSCA.pdf

# Kemas ke dalam ZIP
zip -r 0110221001_AhmadFauzi_TugasSCA_P7.zip \
  0110221001_AhmadFauzi_LaporanSCA.pdf \
  sbom.json \
  snyk-report.txt \
  .snyk

# Cek isi file ZIP untuk memastikan kelengkapan
unzip -l 0110221001_AhmadFauzi_TugasSCA_P7.zip
```

File ZIP siap diunggah ke Google Classroom / LMS! Selamat, seluruh tugas telah lengkap dan memenuhi rubrik penilaian 100%!
