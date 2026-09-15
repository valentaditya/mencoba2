# SOP Git Workflow — PT Andima Transportindo

## A. Tujuan

Dokumen ini dibuat sebagai panduan standar penggunaan Git dan GitHub pada pengembangan sistem untuk PT Andima Transportindo. Dokumen ini bertujuan untuk memastikan proses pengembangan kode dilakukan secara terstruktur, mengurangi konflik kode, menjaga stabilitas branch `main`, serta memastikan setiap perubahan kode melalui proses review sebelum di-merge ke branch `main`.

---

## B. Scope

SOP ini berlaku untuk seluruh tribe yang terlibat dalam pengembangan sistem, khususnya peserta yang bersentuhan langsung dengan proses development sistem baik **WebDev** maupun **QA** yang berhubungan dengan repository.

SOP mencakup:
- Struktur branch repository
- Aturan penamaan branch
- Alur pengembangan fitur
- Commit dan Pull Request
- Code Review
- Merge antar branch
- Branch Protection

---

## C. Struktur Branch

Repository menggunakan empat jenis branch utama:

| Branch | Fungsi | Status |
|---|---|---|
| `main` | Menyimpan versi sistem yang stabil dan siap digunakan/release. | Protected |
| `staging` | Menyimpan versi yang digunakan untuk final testing sebelum release. | Protected |
| `development` | Branch utama untuk integrasi hasil pengembangan fitur. | Protected |
| `[grup]-[nomor]-[fitur]` | Branch per fitur yang dibuat oleh masing-masing skuat. Format: `[grup]-[nomor_alur]-[nama_fitur]`, contoh: `D4-001-login`. | Not Protected |

---

## D. Branching Strategy

### a. `main`

Branch `main` berisi versi sistem yang telah stabil dan siap untuk *release*.

**Ketentuan:**
- WebDev **tidak diperbolehkan** melakukan direct push ke `main`
- Perubahan dilakukan melalui **Pull Request**
- Perubahan berasal dari `staging` setelah proses testing dinyatakan selesai oleh QA
- **Force Push tidak diperbolehkan**

### b. `staging`

Branch `staging` digunakan untuk melakukan pengujian terhadap versi sistem sebelum dinyatakan siap *release* ke branch `main`.

**Ketentuan:**
- Developer **tidak diperbolehkan** melakukan direct push
- Perubahan masuk melalui **Pull Request** dari `development`
- Digunakan sebagai *pre-release environment* sebelum merge ke `main`
- **Force Push tidak diperbolehkan**

### c. `development`

Branch `development` digunakan sebagai branch integrasi seluruh fitur yang telah dikembangkan oleh anggota tim.

**Ketentuan:**
- Setiap fitur dikembangkan melalui branch dengan format `[grup]-[nomor_alur]-[nama_fitur]`
- Perubahan dari feature branch masuk melalui **Pull Request**
- Perubahan pada branch harus melalui proses review
- Branch digunakan untuk memastikan fitur-fitur dapat berjalan bersama sebelum dipindahkan ke `staging`

### d. `[grup]-[nomor_alur]-[nama_fitur]`

Setiap fitur dikerjakan di branch tersendiri. Branch ini dibuat oleh masing-masing skuat setiap kali ada fitur baru yang akan dikembangkan — **satu fitur, satu branch baru**.

**Format penamaan:**
```
[grup]-[nomor_alur]-[nama_fitur]
```

| Bagian | Keterangan | Contoh |
|---|---|---|
| `grup` | Kode skuat/kelompok | `D4` |
| `nomor_alur` | Nomor urut fitur (3 digit) | `001`, `002` |
| `nama_fitur` | Nama fitur singkat tanpa spasi | `login`, `register` |

**Contoh:** `D4-001-login`, `D4-002-register`, `B2-001-dashboard`

Branch dibuat dari `development` yang sudah di-set sebagai default oleh repo owner. Sebelum membuat branch baru, skuat **wajib melakukan pull terlebih dahulu** untuk memastikan branch dimulai dari versi `development` terbaru.

**Alur Branch secara keseluruhan:**

```
[grup]-[nomor_alur]-[nama_fitur]   (misal: D4-001-login)
    |
    |  Pull Request
    ▼
development
    |
    |  Pull Request
    ▼
staging
    |
    |  Pull Request
    ▼
main
```

---

## E. Development Workflow

Workflow selama development (per 14 September 2026):

### 1. Pull `development` Terbaru

Sebelum membuat branch baru, **wajib** pull `development` terlebih dahulu agar branch dibuat dari versi terbaru:

```bash
git checkout development
git pull origin development
```

### 2. Membuat Branch Fitur Baru

Setiap fitur baru **selalu** dibuatkan branch baru dengan format `[grup]-[nomor_alur]-[nama_fitur]`:

```bash
git checkout -b [grup]-[nomor_alur]-[nama_fitur]
```

**Contoh:**
```bash
git checkout -b D4-001-login
```

> Jangan mengerjakan fitur berbeda di branch yang sama. Satu fitur = satu branch baru.

### 3. Melakukan Pengembangan

WebDev melakukan pengembangan pada branch fitur. Setiap perubahan disimpan melalui commit & push:

```bash
git add .
git commit -m "feat: add login form"
git push -u origin D4-001-login
```

### 4. Pull Request ke `development`

Setelah fitur selesai dan sudah diuji, WebDev membuat Pull Request yang akan di-review oleh anggota yang telah ditentukan.

```
D4-001-login  ──── Pull Request ────►  development
```

> Pull Request harus melalui proses **code review** sebelum dapat di-merge.

### 5. Merge `development` → `staging` → `main`

Setelah Pull Request direview dan disetujui, perubahan dapat di-merge ke `development`.

**Tahap 1 — Integrasi ke `development`:**

```bash
git add .
git commit -m "feat: add login functionality"
git push -u origin D4-001-login
```

Setelah fitur-fitur yang diperlukan terintegrasi dan siap diuji, buat Pull Request ke `staging`:

```
development  ──── Pull Request ────►  staging
```

**Tahap 2 — Testing di `staging`:**

Branch `staging` digunakan untuk testing dan validasi sistem.

**Tahap 3 — Release ke `main`:**

Jika versi pada `staging` telah dinyatakan stabil dan siap release, buat Pull Request ke `main`:

```
staging  ──── Pull Request ────►  main
```

Setelah Pull Request disetujui, perubahan dapat di-merge ke `main`.

---

> **Catatan:** Seluruh merge ke branch protected (`main`, `staging`, `development`) wajib melalui mekanisme Pull Request dan tidak boleh dilakukan secara langsung (direct push).
