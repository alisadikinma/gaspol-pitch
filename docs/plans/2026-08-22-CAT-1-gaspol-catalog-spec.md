**Ticket:** CAT-1

# gaspol-catalog — mesin katalog & deck penjualan B2B

> Spec hasil `gaspol-brainstorm`, 2026-08-22. Jalur: **ARCHITECTURAL**.
> Tahap berikutnya: `gaspol-plan` (menulis `-plan.md` terpisah, tiket sama).

---

## Design

### 1. Konteks dan masalah

`gaspol-pitch` menghasilkan deck **investor**: rubrik VC 11 dimensi (TAM, moat,
traction, the ask), referensi SAFE/dilusi/term-sheet, ambang LTV≥3×CAC.
Mesinnya bagus; audiensnya salah untuk kebutuhan berikut.

Kebutuhan nyata: memasarkan platform **INDUSIA AI Cargo** (repo
`indusia-ai-logistics`) ke perusahaan logistik, trucking, forwarder, dan EMKL di
Batam, Pekanbaru, Medan, Surabaya, Semarang, Makassar, Jakarta, dan kota
pelabuhan lain. Pembacanya **owner dan manajemen perusahaan logistik**, bukan
investor. Yang dibeli bukan moat, melainkan selisih hari kerja, solar yang tidak
hilang, dan bukti bahwa sistemnya sudah jalan di tempat lain.

Tim marketing harus bisa menjalankannya berulang kali tanpa mendesain ulang tiap
prospek.

### 2. Keputusan yang sudah diambil (brainstorm 2026-08-22)

| # | Keputusan | Alasan |
|---|---|---|
| D1 | Precedent vault diterapkan **penuh** — `30-Knowledge/deck-b2b-standard.md` (51 KB) dan `30-Knowledge/playbook-cargo-logistics.md` (59 KB) jadi sumber otak | Disuling dari 4 deck B2B nyata yang sudah dikirim: Yafindo, Ekaputra Dinata Utama, Tranzporter.ai, Global Pratama. Menemukan ulang = membayar dua kali |
| D2 | **Pola** masuk `references/` (generik, bisa dipublikasi); **instansi** (IRN, Yafindo, harga INDUSIA, kota) tetap runtime input | Aturan keras CLAUDE.md #2 + CI guard. Nol isi hilang, guard tetap hijau |
| D3 | Deliverable = **kit penjualan lengkap**, bukan satu deck | Tim marketing butuh alat, bukan artefak |
| D4 | **Plugin terpisah** `gaspol-catalog`, bukan jalur kedua di `gaspol-pitch` | Batas rilis bersih, jalur investor tidak berisiko rusak |
| D5 | `pitch-review` **tidak disalin**. Rubriknya dijadikan parameter, `gaspol-catalog` menyodorkan rubrik B2B-nya sendiri | Duplikasi rubrik adalah cara dua standar jadi berbeda diam-diam |
| D6 | Referensi IRN: **anonim di Batam, bernama di luar Batam** | Target Batam adalah kompetitor langsung IRN. Bukti tetap konkret, hubungan klien aman |
| D7 | Bentuk plugin = **C**: `catalog-master` (sekali) · `catalog-variant` (per prospek) · `catalog-gate` (pemblokir) | Varian dijalankan puluhan kali, master sekali. Bentuk mengikuti frekuensi pemakaian |
| D8 | Prior art diadopsi: symlink `founder-playbook`, install clawfu `skills/sales` + `strategy`, pakai pola `enablement-kit` | Potongannya beli, orkestrasi dan gerbangnya bangun |

### 3. Prior-art probe — hasil (2026-08-22)

Diperiksa: `npx skills find` (4 kueri), `plugin-catalog-cache.json`, 175 skill
`guia-matthieu/clawfu-skills`, 244 skill `gtmagents/gtm-agents`, 56 skill
`whyashthakker/agent-skills-marketing`, 16 skill `getagentseal/founder-playbook`.

**BELI — sudah ada:**

| Sumber | Isi | Status |
|---|---|---|
| `getagentseal/founder-playbook` | 16 skill buku bisnis; menutup 10/10 dependensi `gaspol-pitch`; bonus `spin-selling`, `crossing-the-chasm` | **Sudah di disk** di `~/.agents/skills/`, belum di-symlink ke `~/.claude/skills/` |
| `guia-matthieu/clawfu-skills` → `skills/sales/` | `challenger-sale` · `meddic-scorecard` · `spin-selling` · `sandler-system` · `never-split-difference` · `sales-narrative` (April Dunford *Sales Pitch* 2023, 8 langkah) | Perlu install. Audit skills.sh: Socket PASS, Snyk PASS |
| `guia-matthieu/clawfu-skills` → `skills/strategy/` | `positioning` · `pricing-strategy` | Perlu install |
| `gtmagents/gtm-agents@enablement-kit` | Kerangka 5 bagian: Context Brief · Talk Tracks & Scripts · Collateral Pack · System Instructions · Feedback Loop | Pola saja, tidak perlu install |
| `ui-ux-pro-max:slides`, `dataviz` | render slide + chart | Sudah terpasang |

**BANGUN — nol padanan di pasar (temuan probe):**

1. Standar deck B2B Indonesia — nol wajah, kontrak visual 60/40, cover tanpa
   harga, pita tepi babak, cliffhanger glyph. Semua skill pasar berorientasi
   US-SaaS.
2. Review render sebagai fase pemblokir tersendiri (model gambar mengulang teks
   dan mengarang angka).
3. Pagar kejujuran per-operator — apa yang platform BISA vs apa yang klien
   SETUJU dikatakan.
4. Varian per-prospek yang menambang temuan dari dokumen prospek sendiri.
5. Model penjualan berfase Survei → Pilot → Rollout dengan kriteria sukses
   ditandatangani klien.

### 4. Arsitektur

```
gaspol-catalog/
  .claude-plugin/plugin.json
  CLAUDE.md
  skills/
    gaspol-catalog/        router — pilih master / variant / gate
    catalog-master/        sekali per produk
    catalog-variant/       per prospek (murah, sering)
    catalog-gate/          dua lapis pemblokir: teks + render
  references/
    b2b/
      b2b-deck-rubric.md         lint biner — 9 cek (7 warisan + 2 ditulis ulang)
      b2b-buyer-rubric.md        skor berdimensi pembeli + ABC + lapis keyakinan
      b2b-deck-architecture.md   8 babak COVER→ISSUES→…→PENUTUP
      b2b-honesty-rails.md       pagar kejujuran + kit urgensi + jangkar harga
      b2b-visual-contract.md     nol wajah · 60/40 · pita babak · pagar render
      b2b-phased-sale.md         Survei→Pilot→Rollout, kriteria ditandatangani
      enablement-kit.md          kerangka 5 bagian
      examples/
        good-catalog.md          fixture WAJIB PASS
        bad-catalog.md           fixture WAJIB BLOCKING
```

#### 4.1 `catalog-master` — sekali per produk

Input: katalog kapabilitas produk, kontrak konten, hasil riset pembeli
(sub-proyek A), daftar modul + harga (input manusia).

Panggil: `positioning`, `pricing-strategy`, `monetizing-innovation`,
`sales-narrative`, `crossing-the-chasm`.

Output `catalog/`:
- katalog master kapabilitas (12–16 slide, arsitektur 8 babak)
- menu modul à-la-carte + harga — **wajib punya baris yang tidak bisa dilepas**
  (deck-b2b-standard §12.1)
- kit enablement 5 bagian (Context Brief · Talk Tracks · Collateral Pack ·
  System Instructions · Feedback Loop)
- one-pager 1 halaman
- skrip WhatsApp / telepon / tangani keberatan
- spesifikasi kalkulator ROI (input: jumlah truk, rute, harga solar)

#### 4.2 `catalog-variant` — per prospek

Input: nama PT, kota, ukuran armada, dokumen/informasi publik prospek.

Panggil: `spin-selling` (gali), `challenger-sale` (reframe),
`meddic-scorecard` (nilai kelayakan deal sebelum deck dibuat).

Output: deck 10–16 slide, arsitektur 8 babak, slide ISSUES lahir dari temuan
prospek itu sendiri — bukan template diisi. Varian referensi dipilih otomatis
dari kota prospek (D6).

Gerbang murah di depan: kalau `meddic-scorecard` menilai deal tidak layak,
laporkan dan **jangan** buat decknya.

#### 4.3 `catalog-gate` — dua lapis, dua-duanya memblokir

**Lapis teks** — memanggil `pitch-review` dengan path rubrik B2B. Menjalankan
seluruh arsitektur 6 lapis yang diwarisi (lihat §5).

**Lapis render** — fase tersendiri, dijalankan setelah PNG ada. Memeriksa:
teks terduplikasi, angka yang tidak ada di `deck.md`, kebocoran bahasa,
kotak logo gagal, headline terhapus, kualifikasi terpotong, disclaimer hilang.
Sumber: deck-b2b-standard §0b, §0c-C, §0d-A/B/C/D.

Alasan lapis render terpisah: deck Ekaputra Dinata Utama **PASS di teks dan
BLOCKING di render pada hari yang sama** (§0d-E). Lapis teks tidak bisa
menangkapnya karena cacatnya lahir di prompt, bukan di `deck.md`.

### 5. Peta warisan dari `gaspol-pitch`

±70% mesin diwarisi, ±30% muatan ditukar.

#### 5.1 Dibawa utuh

| Yang diwarisi | Catatan |
|---|---|
| Arsitektur review 6 lapis: (a) lint biner → (b) skor → (c) ambang → (d) konsistensi lintas-slide → (e) gerbang naratif → (f) subagen skeptis Q&A | Kerangka agnostik audiens; hanya muatan tiap lapis yang ditukar |
| Kontrak uji-diri: `good-*` WAJIB PASS, `bad-*` WAJIB BLOCKING; skill dinyatakan benar hanya kalau dua-duanya terpenuhi | Nol padanan di 400+ skill yang diprobe |
| Verdict BLOCKING + gate-loop, larangan melunakkan verdict | Lebih kritis di B2B: satu deck beredar ke puluhan PT |
| Subagen skeptis Q&A | Di B2B jadi simulasi keberatan owner; diisi hasil riset sub-proyek A |
| `references/` = otak, skill = tipis (ponytail) | |
| Disiplin ekonomi slide keep / merge / cut / split | |
| Aturan headline: membaca headline saja harus merekonstruksi argumen | PART D |
| Angka selalu dikontekstualisasi, tidak pernah mengambang | PART D |

#### 5.2 Cek biner — 7 dari 9 pindah verbatim

Pindah apa adanya: `Value-Anchor` · `Pyramid Principle` · `Assertion-vs-Topic` ·
`One-Message` · `Linguistic-Fluff` · `5-Second Rule` · `Data-Parsing`.

`Forwardable Test` pindah dan **naik jadi wajib** — deck-b2b-standard §1
menyatakan premis yang sama: dokumen ini beredar tanpa kita, dibuka di rapat
oleh orang yang tidak hadir, setiap slide harus berdiri sendiri.

`Platform-Risk` **ditulis ulang**: dari "ketergantungan satu API tertutup" jadi
vendor lock-in, SLA, kepemilikan data, dan kelanjutan layanan.

#### 5.3 Ditukar isinya

| `gaspol-pitch` | `gaspol-catalog` |
|---|---|
| 11 dimensi VC (TAM, moat, the ask) | dimensi pembeli — apakah prospek melihat masalahnya sendiri |
| Ambang LTV ≥ 3×CAC, payback < 12 bulan | ROI prospek, balik modal armada dia |
| Gerbang *earned secret* | gerbang **bukti operasi** — deployment referensi yang berjalan |
| *Dinner test* (partner ke partner) | apa yang manajer ops ulangi ke ownernya |
| Konsistensi TAM ↔ traksi ↔ ask | konsistensi harga ↔ paket ↔ linimasa ↔ klaim hemat |
| `vc-fundamentals.md` (SAFE, dilusi, term sheet) | pengadaan Indonesia: termin, SLA, struktur konsorsium, kriteria pilot |

#### 5.4 Lapis yang ditambah — tidak ada di `gaspol-pitch`

- **(g) review render** — §4.3 di atas.
- **(h) pagar kejujuran** — kontrak konten per-operator; kematangan platform ≠
  kematangan modul (pelajaran Global Pratama); asumsi dibenarkan oleh sumbernya
  bukan oleh hasilnya (§11.1); ekstrapolasi N=1 boleh, menyembunyikannya tidak
  (§11.2).
- **(i) keabsahan kit urgensi** — §7.2: enam alat urgensi, semuanya harus
  benar-benar berlaku. Tenggat palsu = BLOCKING.

### 6. Perubahan pada `gaspol-pitch` (repo ini)

Satu perubahan, eksplisit karena repo ini sudah dipublikasi ke marketplace:

`skills/pitch-review/SKILL.md` menerima **path rubrik sebagai parameter**.
Default tetap `../../references/investor-deck-rubric.md` +
`../../references/vc-review-rubric.md` sehingga perilaku jalur investor tidak
berubah sama sekali. `gaspol-catalog` memanggilnya dengan path rubrik B2B.

Kontrak uji-diri `gaspol-pitch` yang ada (`good-deck` PASS, `bad-deck` BLOCKING)
harus tetap hijau setelah perubahan ini — itu buktinya perilaku default utuh.

### 7. Data Integration Map

| Komponen | Sumber | Sudah ada? | Catatan |
|---|---|---|---|
| Rubrik B2B (teks) | `deck-b2b-standard.md` §1–§14 | vault ✅ | perlu digenerikkan (D2) |
| Rubrik render | `deck-b2b-standard.md` §0b, §0c-C, §0d | vault ✅ | perlu digenerikkan |
| Mesin skor | `pitch-review` | ✅ + 1 ubahan | §6 |
| Otak sales B2B | clawfu `skills/sales/` (6) | ❌ install | Socket/Snyk PASS |
| Positioning & pricing | clawfu `skills/strategy/` (2) | ❌ install | |
| Framework buku | `founder-playbook` (16) | ✅ di disk | perlu symlink |
| Bentuk kit | `gtm-agents@enablement-kit` | pola | tidak install |
| Domain cargo | `playbook-cargo-logistics.md` | vault ✅ | |
| **Riset pembeli** | NotebookLM + Firecrawl + arsip internal | ❌ | **sub-proyek A, belum dikerjakan** |
| Kapabilitas produk | README `indusia-ai-logistics` | ✅ | 280 operasi API · 61 model · 10 antrian · 30 layar · 3.500+ test (terverifikasi 2026-08-15) |
| Pagar kejujuran | `docs/content/irn-public-content-contract.md` | ✅ | per-operator |
| Varian referensi | anonim Batam / bernama luar Batam | ✅ D6 | |
| Palet & font | `gaspol-design` opsional, `deck-design.md` fallback | ✅ | degrade anggun |
| Render PNG | image MCP | opsional | tanpa MCP = emit prompt saja |
| **Harga & modul** | Ali | ❌ | lihat §8 |

### 8. Wajib input manusia — haram dikarang (Iron Law)

1. **Harga per modul dan paket.** Tidak ada di sumber mana pun yang dibaca.
   Katalog tanpa harga bukan katalog. `catalog-master` harus BERHENTI dan
   bertanya, bukan mengarang angka.
2. **Ukuran armada tiap prospek.** Slide ISSUES lahir dari angka prospek.
   §11.2: ekstrapolasi dari N=1 sah, menyembunyikannya tidak.
3. **Multi-tenancy belum nyata.** Vault eksplisit: jangan diklaim. Konsekuensinya
   tiap pelanggan baru = deployment terpisah, dan itu mengubah linimasa serta
   harga di katalog. Wajib jujur tertulis.

### 9. Pembagian sub-proyek

| | Sub-proyek | Isi | Ketergantungan |
|---|---|---|---|
| **A** | Riset pembeli | Perilaku beli dan pemicu keputusan owner logistik / armada truk / forwarder / EMKL di kota pelabuhan Indonesia. Sumber: Firecrawl (asosiasi, artikel industri, pengadaan pelabuhan, keluhan operator) **+** arsip internal (riset IRN, deck Yafindo/Tranzporter/Global Pratama, kontrak konten). Keduanya masuk NotebookLM sebagai sumber terkutip | — |
| **B** | Mesin `gaspol-catalog` | 3 skill + `references/b2b/` + ubahan `pitch-review` | A (mengisi dimensi pembeli & simulasi keberatan) |
| **C** | Deliverable INDUSIA AI Cargo | Kit penjualan lengkap untuk tim marketing | B |

Urutan: **A → B → C**. Tiap sub-proyek punya siklus spec → plan → implement
sendiri; spec ini mencakup A dan B.

**Peringatan riset (dari vault, 2026-08-08):** NotebookLM tercatat **mengarang** —
mengklaim dokumen 111 halaman padahal aslinya 22, dan salah memetakan struktur
chart. Keluarannya hanya sekuat sumber yang dimasukkan, dan setiap klaim yang
akan masuk deck wajib ditelusuri balik ke sumbernya. NotebookLM membuat notebook
di akun Google milik Ali — jalankan hanya setelah spec ini disetujui.

### 10. Kontrak uji-diri (syarat kebenaran plugin)

Diwarisi utuh dari `pitch-review`, dengan fixture B2B:

- `references/b2b/examples/bad-catalog.md` **WAJIB** kena BLOCKING, dan tiap
  cacat harus disebut lapis mana yang menangkapnya:
  - fluff B2B ("solusi end-to-end terintegrasi", "world-class") → lapis (a)
  - headline label ("Harga", "Solusi") bukan asersi → lapis (a)
  - harga muncul di cover → lapis (b) arsitektur
  - wajah manusia di slide → lapis (g) kontrak visual
  - klaim modul belum jadi disamakan dengan platform yang live → lapis (h)
  - tenggat yang tidak benar-benar berlaku → lapis (i)
- `references/b2b/examples/good-catalog.md` **WAJIB** PASS.
- Plugin dinyatakan benar hanya kalau **dua-duanya** terpenuhi.

### 11. Di luar lingkup

- Video promo (`ai-video-promo-engine` ada di disk; sub-proyek terpisah).
- Multi-tenancy `indusia-ai-logistics` — wave produk, bukan pekerjaan katalog.
- Dasbor demo klik dan kalkulator ROI: **spesifikasinya** masuk `catalog-master`,
  **implementasinya** sub-proyek C dan akan memicu Phase 3 desain UI.
- Menerbitkan `gaspol-catalog` ke marketplace publik — setelah fixture hijau.

### 12. Pertanyaan terbuka

1. Daftar modul + harga (§8.1) — menunggu Ali.
2. Apakah `gaspol-catalog` repo git sendiri sejak awal, atau tinggal dulu di
   `claude-plugin/` sampai fixture hijau? (usul: yang kedua)
3. Izin tertulis IRN untuk penyebutan bernama di luar Batam — perlu dikonfirmasi
   sebelum deck varian luar-Batam dikirim.
