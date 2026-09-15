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
| `feature/*` | Branch untuk mengembangkan fitur atau perubahan tertentu. | Not Protected |

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
- Setiap fitur dikembangkan melalui branch `feature/*`
- Perubahan dari feature branch masuk melalui **Pull Request**
- Perubahan pada branch harus melalui proses review
- Branch digunakan untuk memastikan fitur-fitur dapat berjalan bersama sebelum dipindahkan ke `staging`

### d. `feature/*`

Feature branch digunakan untuk mengembangkan fitur, perbaikan, atau perubahan tertentu pada sistem.

**Format penamaan:**
```
feature/<nama-fitur>
```
**Contoh:** `feature/login`

Feature branch dibuat berdasarkan branch `development` yang sudah di-set sebagai default oleh repo owner.

**Alur Branch secara keseluruhan:**

```
feature/*
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

### 1. Update `development`

Sebelum memulai, WebDev disarankan untuk melakukan update branch `development` agar branch baru dibuat berdasarkan versi terbaru:

```bash
git checkout development
git pull origin development
```

### 2. Membuat Feature Branch

Buat branch baru dari `development`:

```bash
git checkout -b feature/<nama-fitur>
```

**Contoh:** `git checkout -b feature/login`

### 3. Melakukan Pengembangan

WebDev melakukan pengembangan pada feature branch. Selama proses pengembangan, perubahan disimpan melalui commit & push:

```bash
git add .
git commit -m "feat: add login form"
git push -u origin feature/login
```

### 4. Pull Request ke `development`

Setelah fitur selesai dan sudah diuji, WebDev membuat Pull Request yang akan di-review oleh anggota yang telah ditentukan.

```
feature/*  ──── Pull Request ────►  development
```

> Pull Request harus melalui proses **code review** sebelum dapat di-merge.

### 5. Merge `development` → `staging` → `main`

Setelah Pull Request direview dan disetujui, perubahan dapat di-merge ke `development`.

**Tahap 1 — Integrasi ke `development`:**

```bash
git add .
git commit -m "feat: add login functionality"
git push -u origin feature/login
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
