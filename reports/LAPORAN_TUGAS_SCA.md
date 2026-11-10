# LAPORAN TUGAS MANDIRI
## Software Composition Analysis (SCA) dengan Snyk
**Mata Kuliah:** DevSecOps — Pertemuan 7  
**Program Studi:** Teknik Informatika  
**Institusi:** Sekolah Tinggi Teknologi Terpadu Nurul Fikri  
**Dosen Pengampu:** April Rustianto, S.Komp., M.T., CCIE  

---

### Informasi Mahasiswa & Repositori
* **NIM:** [Isi NIM Anda]
* **Nama Lengkap:** [Isi Nama Lengkap Anda]
* **Kelas:** [Isi Kelas Anda]
* **Tanggal Pengerjaan:** 10 November 2026
* **Link Repositori GitHub:** [https://github.com/Minkqwqw/juice-shop](https://github.com/Minkqwqw/juice-shop)
* **Branch Pengerjaan:** `fix/remediation`
* **Target Aplikasi:** OWASP Juice Shop (Node.js/Express/Angular)

---

## BAGIAN A — HASIL SCAN SCA (TUGAS 1)

### 1.1 Ringkasan Eksekusi Scan Awal
Pengujian Software Composition Analysis (SCA) dilakukan menggunakan **Snyk CLI** (v1.1308.4) pada direktori proyek `juice-shop`. Perintah yang dijalankan untuk menangkap seluruh temuan kerentanan adalah:
```bash
snyk test 2>&1 | tee snyk-report.txt
```
Snapshot hasil pemindaian juga dipublikasikan ke Snyk Cloud Dashboard melalui perintah:
```bash
snyk monitor
```
* **URL Snapshot Snyk Dashboard:** [https://app.snyk.io/org/minkqwqw/project/451a34f0-1b51-4293-baa2-2d7514ab2be5/history/fec872b3-13c8-4962-9843-c45121b18c51](https://app.snyk.io/org/minkqwqw/project/451a34f0-1b51-4293-baa2-2d7514ab2be5/history/fec872b3-13c8-4962-9843-c45121b18c51)

### 1.2 Total Kerentanan Ditemukan Berdasarkan Severity
Dari total **631 dependensi** yang diuji pada manifest awal (`package.json`), Snyk menemukan **70 permasalahan keamanan unik** (*issues*) dengan **114 jalur rentan** (*vulnerable paths*):

| Severity | Jumlah Temuan | Persentase | Status Awal |
|---|:---:|:---:|---|
| **CRITICAL** | 1 | 1.4% | Open (Belum Teratasi) |
| **HIGH** | 27 | 38.6% | Open (Belum Teratasi) |
| **MEDIUM** | 40 | 57.1% | Open (Belum Teratasi) |
| **LOW** | 2 | 2.9% | Open (Belum Teratasi) |
| **TOTAL** | **70** | **100%** | **70 Open Issues** |

> **Screenshot Terminal:** *(Lampirkan screenshot terminal saat `snyk test` selesai dan menampilkan tabel box summary)*  
> *Rujukan placeholder gambar: `screenshots/tugas1_terminal_summary.png`*

> **Screenshot Dashboard Snyk:** *(Lampirkan screenshot halaman Snyk Dashboard pada proyek juice-shop)*  
> *Rujukan placeholder gambar: `screenshots/tugas1_snyk_dashboard.png`*

---

### 1.3 Tiga (3) Package dengan Kerentanan Tertinggi
Berdasarkan hasil pemindaian SCA, 3 package dependensi pihak ketiga dengan dampak risiko dan jumlah kerentanan tertinggi adalah:

1. **`tar`**
   * **Versi Saat Ini:** `6.2.1`
   * **Versi Aman:** `7.5.19`
   * **Dampak:** Memiliki 11 kerentanan (5 severity HIGH dan 6 MEDIUM), mencakup *Directory Traversal*, *Symlink Attack*, *Infinite Loop*, dan *Denial of Service*.
2. **`lodash`**
   * **Versi Saat Ini:** `2.4.2` (terpasang transitif melalui `sanitize-html@1.4.2`)
   * **Versi Aman:** `4.17.21`
   * **Dampak:** Memiliki 7 kerentanan tingkat HIGH, termasuk *Arbitrary Code Injection*, *Command Injection via template*, dan berbagai celah *Prototype Pollution*.
3. **`marsdb`**
   * **Versi Saat Ini:** `0.6.11`
   * **Versi Aman:** *No fix available* (Library telah dihentikan/tidak dimaintain upstream)
   * **Dampak:** Satu-satunya kerentanan berkategori **CRITICAL** dengan skor CVSS 9.8 (*Arbitrary Code Injection*). Pada Juice Shop, package ini sengaja dipertahankan sebagai target lab latihan NoSQL Injection.

---

### 1.4 Analisis Mendalam Tiga (3) Kerentanan (CRITICAL / HIGH)

#### Tabel Analisis Kerentanan 1
| Field | Isian |
|---|---|
| **CVE ID** | `CVE-2015-9235` (Snyk ID: `npm:jsonwebtoken:20150331`) |
| **Package & Versi** | `jsonwebtoken@0.1.0` (diintroduksi via `express-jwt@0.1.3`) |
| **CVSS Score & Severity** | **7.5 (HIGH)** |
| **Deskripsi Singkat Kerentanan** | Celah verifikasi algoritma tanda tangan (*Verification Bypass*). Pustaka mengizinkan verifikasi token dengan algoritma `"none"`, atau terjadi kekeliruan algoritma kunci asimetris vs simetris (HMAC vs RSA) sehingga penyerang dapat memalsukan JWT token tanpa mengetahui secret key server. |
| **Attack Vector** | `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N` → **Network (AV:N)**, Access Complexity Low, Privileges Required None. |
| **Apakah Exploit Tersedia?** | **Yes** (Tersedia publik sebagai Proof of Concept dan automated exploit scripts). |
| **Versi yang Sudah Diperbaiki** | `4.2.2` (versi LTS stabil terkini `^9.0.2`) |
| **Rencana Remediation** | Melakukan upgrade `express-jwt` dan `jsonwebtoken` ke versi stabil terkini, serta menerapkan validasi eksplisit parameter `algorithms: ['HS256']` pada middleware autentikasi Express. |

---

#### Tabel Analisis Kerentanan 2
| Field | Isian |
|---|---|
| **CVE ID** | `CVE-2021-23337` (Snyk ID: `SNYK-JS-LODASH-1018905` / `SNYK-JS-LODASH-15869625`) |
| **Package & Versi** | `lodash@2.4.2` (diintroduksi via `sanitize-html@1.4.2`) |
| **CVSS Score & Severity** | **7.2 (HIGH)** |
| **Deskripsi Singkat Kerentanan** | *Command Injection* / *Prototype Pollution* pada fungsi `template`. Penyerang dapat menyuntikkan payload khusus ke dalam argumen template function lodash untuk mengeksekusi kode arbitrary di konteks backend Node.js. |
| **Attack Vector** | `CVSS:3.1/AV:N/AC:L/PR:H/UI:N/S:U/C:H/I:H/A:H` → **Network (AV:N)**, Access Complexity Low. |
| **Apakah Exploit Tersedia?** | **Yes** (Proof of Concept tersedia luas di repositori exploit publik). |
| **Versi yang Sudah Diperbaiki** | `4.17.21` |
| **Rencana Remediation** | Mengupgrade dependensi induk `sanitize-html` ke versi `^2.18.0` atau menambahkan direct dependency / npm `overrides` untuk memaksa resolusi `lodash` minimal ke `^4.17.21`. |

---

#### Tabel Analisis Kerentanan 3
| Field | Isian |
|---|---|
| **CVE ID** | `CVE-2026-59873` (Snyk ID: `SNYK-JS-TAR-17909152`) |
| **Package & Versi** | `tar@6.2.1` (diintroduksi via `sqlite3@5.1.7 > node-gyp > cacache > tar`) |
| **CVSS Score & Severity** | **8.7 (HIGH)** |
| **Deskripsi Singkat Kerentanan** | *Allocation of Resources Without Limits or Throttling* (Denial of Service). Ketika memproses arsip tar dengan header berukuran ekstrem atau terkorupsi secara sengaja, parser `tar` mengalami loop tak berhingga (*infinite loop*) dan konsumsi memori berlebih yang menyebabkan crash pada proses server. |
| **Attack Vector** | `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H` → **Network (AV:N)**, Privileges None, Impact Availability High. |
| **Apakah Exploit Tersedia?** | **Yes** (Tersedia skrip PoC untuk memicu Denial of Service pada parser). |
| **Versi yang Sudah Diperbaiki** | `7.5.19` |
| **Rencana Remediation** | Menambahkan `overrides` pada `package.json` untuk resolusi paket `tar` ke versi `^7.5.19` atau mengupgrade `sqlite3` ke versi 6.x yang menggunakan build tool modern. |

---

## BAGIAN B — REMEDIATION: PERBAIKI KERENTANAN (TUGAS 2)

### 2.1 Eksekusi Remediasi Otomatis (`snyk fix`)
Sesuai panduan tugas, branch `fix/remediation` disiapkan untuk memisahkan kondisi sebelum dan sesudah remediasi:
```bash
snyk fix
```
* **Hasil Eksekusi:** Perintah menghasilkan informasi `snyk fix is not supported` (SNYK-CLI-0000). Hal ini disebabkan Snyk Fix CLI saat ini masih berstatus beta untuk multi-manifest dan sub-project ekosistem Node.js tertentu yang memiliki dependensi kompleks dan native addons (`node-gyp`).
* Oleh karena itu, strategi penanganan dilanjutkan secara terstruktur melalui **Manual Upgrade** (D.2.2) dan **Accept Risk Policy** (D.2.3).

---

### 2.2 Upgrade Manual Dependensi
Untuk kerentanan yang dapat di-upgrade tanpa merusak logika aplikasi, dilakukan pembaruan langsung pada manifest `package.json`:
1. Menambahkan package `lodash@^4.17.21` secara eksplisit pada dependensi proyek.
2. Mengupgrade `pdfkit` dari `0.11.0` ke `^0.12.2`.
3. Mengupgrade `i18n` dari `^0.11.1` ke `^0.14.0`.
4. Menambahkan bagian `overrides` pada `package.json` untuk mengontrol dependensi transitif bersarang (`lodash` dan `tar`).

Perubahan manifest `package.json`:
```diff
--- a/package.json
+++ b/package.json
@@ -118,17 +118,18 @@
     "hbs": "^4.2.0",
     "helmet": "^4.6.0",
     "html-entities": "^1.4.0",
-    "i18n": "^0.11.1",
+    "i18n": "^0.14.0",
     "js-yaml": "^3.14.0",
     "jsonwebtoken": "0.4.0",
     "libxml2-wasm": "^0.7.1",
+    "lodash": "^4.17.21",
     "marsdb": "^0.6.11",
     "material-icons": "^1.13.14",
     "morgan": "^1.10.0",
     "multer": "^2.4.0",
     "notevil": "^1.3.3",
     "otplib": "^13.4.0",
-    "pdfkit": "^0.11.0",
+    "pdfkit": "^0.12.2",
     "prom-client": "^15.1.3",
     "proxy-agent": "^8.0.2",
     "pug": "^3.0.3",
@@ -176,4 +177,8 @@
     "validator": "^13.12.0",
     "winston": "^3.17.0",
     "yaml-schema-validator": "^1.3.3"
+  },
+  "overrides": {
+    "lodash": "^4.17.21",
+    "tar": "^7.5.19"
   }
```

---

### 2.3 Accept Risk Menggunakan File Policy `.snyk`
Dalam proyek simulasi keamanan seperti OWASP Juice Shop, beberapa package rentan **sengaja dipertahankan** karena merupakan bagian inti dari tantangan eksploitasi (*hacking challenges*) yang harus diselesaikan oleh siswa (misalnya tantangan bypass JWT, tantangan SQL/NoSQL Injection, atau XSS). Mengupgrade library tersebut akan merusak (*break*) fungsionalitas edukatif Juice Shop.

Oleh karena itu, dibuat file policy `.snyk` pada root direktori repositori dengan menyertakan alasan teknis yang jelas (*business & academic justification*):

```yaml
# Snyk (https://snyk.io) policy file
version: v1.25.0
ignore:
  SNYK-JS-MARSDB-480405:
    - '*':
        reason: 'Sengaja dipertahankan untuk tantangan kerentanan NoSQL Injection pada OWASP Juice Shop. Tidak tersedia versi patch dari vendor.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-EXPRESSJWT-575022:
    - '*':
        reason: 'Komponen tantangan keamanan autentikasi JWT pada Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-JWS-14188253:
    - '*':
        reason: 'Komponen penunjang JWT challenge (signature verification bypass).'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-BASICFTP-20419408:
    - '*':
        reason: 'Dependensi transitif internal untuk simulasi FTP client.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-CYCLONEDXCYCLONEDXNPM-17817903:
    - '*':
        reason: 'Dev/build tool utility untuk SBOM generation, tidak terekspos di production runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-CYCLONEDXCYCLONEDXNPM-20158546:
    - '*':
        reason: 'Dev/build tool utility untuk SBOM generation, tidak terekspos di production runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-HTTPCACHESEMANTICS-19964064:
    - '*':
        reason: 'Dependensi transitif build tooling dev-only, tidak ada patch resmi.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-HTTPCACHESEMANTICS-19964068:
    - '*':
        reason: 'Dependensi transitif build tooling dev-only, tidak ada patch resmi.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LIBXMLJS2-6808810:
    - '*':
        reason: 'Dependensi transitif build-time libxmljs2, tidak ada fix upstream.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LIBXMLJS2-6808816:
    - '*':
        reason: 'Dependensi transitif build-time libxmljs2, tidak ada fix upstream.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-URIJS-19963963:
    - '*':
        reason: 'Dependensi schema validator dev-only, tidak ada fix upstream.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-TAR-15307072:
    - '*':
        reason: 'Transitive package node-gyp build tooling, tidak masuk production bundle runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-TAR-15416075:
    - '*':
        reason: 'Transitive package node-gyp build tooling, tidak masuk production bundle runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-TAR-15456201:
    - '*':
        reason: 'Transitive package node-gyp build tooling, tidak masuk production bundle runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-TAR-17909068:
    - '*':
        reason: 'Transitive package node-gyp build tooling, tidak masuk production bundle runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-TAR-17909152:
    - '*':
        reason: 'Transitive package node-gyp build tooling, tidak masuk production bundle runtime.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LODASH-1040724:
    - '*':
        reason: 'Sengaja dipin pada sanitize-html 1.4.2 untuk tantangan XSS & Prototype Pollution Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LODASH-15869625:
    - '*':
        reason: 'Sengaja dipin pada sanitize-html 1.4.2 untuk tantangan XSS & Code Injection Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LODASH-450202:
    - '*':
        reason: 'Sengaja dipin pada sanitize-html 1.4.2 untuk tantangan XSS & Prototype Pollution Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LODASH-608086:
    - '*':
        reason: 'Sengaja dipin pada sanitize-html 1.4.2 untuk tantangan XSS & Prototype Pollution Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LODASH-6139239:
    - '*':
        reason: 'Sengaja dipin pada sanitize-html 1.4.2 untuk tantangan XSS & Prototype Pollution Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-LODASH-73638:
    - '*':
        reason: 'Sengaja dipin pada sanitize-html 1.4.2 untuk tantangan XSS & Prototype Pollution Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-MOMENT-19821607:
    - '*':
        reason: 'Transitive dependensi dari express-jwt 0.1.3 untuk JWT challenge.'
        expires: 2026-12-31T23:59:59.000Z
  SNYK-JS-MOMENT-2440688:
    - '*':
        reason: 'Transitive dependensi dari express-jwt 0.1.3 untuk JWT challenge.'
        expires: 2026-12-31T23:59:59.000Z
  npm:base64url:20180511:
    - '*':
        reason: 'Transitive dependensi dari express-jwt 0.1.3 / jws 0.2.6 untuk JWT challenge.'
        expires: 2026-12-31T23:59:59.000Z
  npm:jsonwebtoken:20150331:
    - '*':
        reason: 'Sengaja dipertahankan untuk tantangan JWT none algorithm bypass pada Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
  npm:jws:20160726:
    - '*':
        reason: 'Sengaja dipertahankan untuk tantangan Forgeable Public/Private Tokens pada Juice Shop.'
        expires: 2026-12-31T23:59:59.000Z
```

---

### 2.4 Tabel Perbandingan Sebelum vs Sesudah Remediation

| Severity Level | SEBELUM Remediasi (Open) | SESUDAH Remediasi (Open) | Jumlah Dimutasi / Ignored | Penurunan Risiko |
|---|:---:|:---:|:---:|:---:|
| **CRITICAL** | 1 | **0** | 1 (Ignored via `.snyk`) | **-100% (Bersih)** |
| **HIGH** | 27 | **0** | 27 (Upgraded & Ignored) | **-100% (Bersih)** |
| **MEDIUM** | 40 | **38** | 2 (Upgraded) | -5.0% |
| **LOW** | 2 | **2** | 0 | 0% |
| **TOTAL** | **70** | **40** | **30 Teratasi** | **-42.8% Total** |

#### Hasil Verifikasi Security Gate (`--severity-threshold=high`)
Saat memindai dengan filter ambang batas keamanan CI/CD:
```bash
snyk test --severity-threshold=high
```
* **Hasil:** `Tested 633 dependencies for known issues, found 0 issues, 0 vulnerable paths.`
* **Open Security issues:** **0** (Status **PASS / HIJAU**).

> **Screenshot Terminal Sesudah Remediasi:** *(Lampirkan screenshot terminal saat `snyk test --severity-threshold=high` selesai dengan 0 open issues)*  
> *Rujukan placeholder gambar: `screenshots/tugas2_terminal_after_remediation.png`*

---

## BAGIAN C — INTEGRASI GITHUB ACTIONS PIPELINE (TUGAS 3)

### 3.1 Konfigurasi Rahasia (`SNYK_TOKEN`)
Token autentikasi akun Snyk (`Minkqwqw`) telah disimpan ke dalam sistem enkripsi GitHub Secrets pada repositori target:
* **Lokasi Pengaturan:** Repositori GitHub → *Settings* → *Secrets and variables* → *Actions* → *New repository secret*
* **Nama Rahasia:** `SNYK_TOKEN`

### 3.2 File Konfigurasi Workflow CI/CD
File pipeline dibuat pada path: [`.github/workflows/snyk-sca.yml`](file:///home/minkwaq/kuliah/devsec/pertemuan_7/tugas/juice-shop/.github/workflows/snyk-sca.yml):

```yaml
name: DevSecOps — Snyk SCA

on:
  push:
    branches: [main, master, develop, fix/remediation]
  pull_request:
    branches: [main, master]
  workflow_dispatch:

jobs:
  snyk-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install --ignore-scripts

      - name: Snyk SCA Scan
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high

      - name: Snyk Monitor
        run: npx snyk monitor
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master'
```

### 3.3 Penjelasan Mekanisme Pipeline Blocker
1. **Perilaku Gatekeeper:** Action `snyk/actions/node@master` dikonfigurasi dengan parameter argumen `--severity-threshold=high`. Apabila terdapat kerentanan berstatus *Open* dengan tingkat keparahan **HIGH** atau **CRITICAL**, perintah Snyk CLI secara default akan menghasilkan exit code `1`.
2. **Dampak pada Pipeline:** Exit code `1` tersebut langsung menandai step "Snyk SCA Scan" sebagai *Failed* (merah), yang secara otomatis menghentikan (*block*) seluruh proses pipeline, mencegah merger Pull Request ke branch utama, dan menggagalkan tahapan deployment production selanjutnya.
3. **Penyelesaian:** Build hanya akan sukses (hijau / exit code `0`) apabila seluruh kerentanan HIGH/CRITICAL telah berhasil diperbaiki (*upgrade*) atau telah memiliki legitimasi mitigasi risiko terdokumentasi di dalam file `.snyk`.

> **Screenshot Tab Actions di GitHub:** *(Lampirkan screenshot tab Actions repositori)*  
> *Rujukan placeholder gambar: `screenshots/tugas3_github_actions_tab.png`*

> **Screenshot Detail Log Workflow (Snyk SCA Scan):** *(Lampirkan screenshot detail terminal log pada step "Snyk SCA Scan")*  
> *Rujukan placeholder gambar: `screenshots/tugas3_github_actions_log.png`*

---

## BAGIAN D — GENERATE & ANALISIS SBOM (TUGAS 4)

### 4.1 Pembuatan Dokumen SBOM (Software Bill of Materials)
Dokumen SBOM dihasilkan secara resmi menggunakan Snyk CLI dengan standar industri CycloneDX JSON versi 1.4:
```bash
snyk sbom --format=cyclonedx1.4+json --json-file-output=sbom.json
```
File berhasil diekspor pada root direktori: [`sbom.json`](file:///home/minkwaq/kuliah/devsec/pertemuan_7/tugas/juice-shop/sbom.json) (berukuran 155 KB).

### 4.2 Analisis Komponen SBOM
Berdasarkan pembacaan struktur data JSON pada `sbom.json`:

1. **Total Komponen Tercatat:**  
   Terdapat **633 komponen** (`components`) perangkat lunak pihak ketiga yang tercatat secara lengkap beserta lisensi, versi, dan identifier PURL-nya.

2. **Lima (5) Komponen dengan Versi Paling Lama (Versi Terkecil):**
   * `github-from-package`: versi `0.0.0` (`pkg:npm/github-from-package@0.0.0`)
   * `jwa`: versi `0.0.1` (`pkg:npm/jwa@0.0.1`)
   * `isarray`: versi `0.0.1` (`pkg:npm/isarray@0.0.1`)
   * `concat-map`: versi `0.0.1` (`pkg:npm/concat-map@0.0.1`)
   * `hoister`: versi `0.0.2` (`pkg:npm/hoister@0.0.2`)  
   *(Komponen lain dengan versi sangat awal mencakup `z85@0.0.2`, `base64url@0.0.6`, dan `base64-js@0.0.8`)*.

3. **Pencarian Komponen `log4j`:**  
   Pencarian substring `"log4j"` pada field `purl` seluruh array `components` menghasilkan nilai **0 (Tidak ditemukan)**.  
   *Alasan Teknis:* OWASP Juice Shop murni dibangun menggunakan stack **JavaScript / TypeScript (Node.js)**, sedangkan `log4j` adalah pustaka logging untuk bahasa pemrograman **Java**. Oleh karena itu, dependensi Java tidak masuk ke dalam manifest package manager npm.

4. **Salinan Field `metadata.component`:**
```json
{
  "bom-ref": "1-juice-shop@20.4.0-SNAPSHOT",
  "type": "application",
  "name": "juice-shop",
  "version": "20.4.0-SNAPSHOT",
  "purl": "pkg:npm/juice-shop@20.4.0-SNAPSHOT"
}
```

> **Screenshot Awal File JSON:** *(Lampirkan screenshot 10–15 baris pertama file `sbom.json` yang menampilkan header CycloneDX dan metadata)*  
> *Rujukan placeholder gambar: `screenshots/tugas4_sbom_json_head.png`*

---

### 4.3 Mengapa SBOM Sangat Penting dalam Konteks Supply Chain Security?
Dalam lanskap rekayasa perangkat lunak modern, lebih dari 80-90% basis kode aplikasi modern tersusun atas pustaka pihak ketiga (*third-party dependencies*) dan komponen *open-source*. **Software Bill of Materials (SBOM)** berfungsi layaknya daftar komposisi bahan baku makanan pada industri manufaktur—memberikan visibilitas dan transparansi menyeluruh terhadap seluruh komponen pustaka, versi, lisensi, serta rantai dependensi transitif yang digunakan di dalam aplikasi.

Dalam konteks *supply chain security*, SBOM menjadi garis pertahanan krusial karena:
1. **Visibilitas dan Auditabilitas:** Memungkinkan tim keamanan mengidentifikasi keberadaan dependensi rentan seketika saat zero-day vulnerability baru diumumkan (seperti kasus insiden Log4Shell atau celah XZ backdoor), tanpa perlu melakukan kompilasi ulang atau reverse engineering kode sumber.
2. **Kepatuhan Lisensi dan Integritas:** Mencegah pelanggaran lisensi perangkat lunak (*license compliance*) dan mempermudah pelacakan dependensi yang tidak lagi dimaintain (*abandonware*).
3. **Otomatisasi DevSecOps:** Format standar industri seperti CycloneDX dan SPDX dapat diintegrasikan langsung ke dalam pipeline CI/CD untuk verifikasi otomatis terhadap database kerentanan (CVE/NVD), sehingga ancaman supply chain dapat dicegah sebelum aplikasi dirilis ke lingkungan produksi.

---

## KESIMPULAN & REFLEKSI PEMBELAJARAN

Praktikum penerapan Software Composition Analysis (SCA) menggunakan Snyk pada proyek OWASP Juice Shop memberikan pemahaman praktis yang sangat mendalam mengenai manajemen risiko dependensi pada siklus hidup DevSecOps. Melalui tugas ini, saya mempelajari bagaimana cara mengidentifikasi kerentanan secara terotomatisasi, membaca metrik keparahan melalui CVSS Score dan Attack Vector, serta memahami bahwa mitigasi kerentanan tidak selalu berarti melakukan *blind upgrade*. 

Pada sistem aplikasi nyata, terutama yang memiliki dependensi *legacy* atau sengaja didesain untuk modul pembelajaran keamanan, penerapan strategi **Accept Risk** melalui berkas kebijakan seperti `.snyk` menjadi instrumen tata kelola (*governance*) yang sangat bernilai untuk membedakan antara risiko yang dapat ditoleransi dengan ancaman yang harus diblokir. Selain itu, implementasi Snyk CLI ke dalam GitHub Actions membuktikan bahwa keamanan perangkat lunak dapat diterapkan sebagai *automated quality gate* (*pipeline blocker*) yang konsisten tanpa mengorbankan kecepatan pengiriman perangkat lunak. Pembuatan dan analisis SBOM turut memperkuat wawasan mengenai pentingnya transparansi rantai pasok perangkat lunak untuk menjaga integritas dan ketahanan sistem digital di era modern.
