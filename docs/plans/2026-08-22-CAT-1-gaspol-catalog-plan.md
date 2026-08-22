> **For Claude:** REQUIRED SKILL: Use gaspol-execute to implement this plan.
> **CRITICAL:** This plan specifies real integrations. During execution,
> NEVER substitute placeholders for real data sources without explicit
> user approval. If a data source doesn't exist yet, STOP and ask.
> **Progress ledger — HARD PER-PHASE GATE:** `.gaspol/progress/PROGRESS-CAT-1.md`. After EACH phase and **BEFORE** starting the next, STOP and do BOTH: (a) tick that phase's `## Checklist` line, (b) append a `## Log` line ending with the handoff cursor. This is **blocking**, like a test gate: no next phase until both are written. **Never batch all updates at the end.** Update ONLY this file — never the shared `.gaspol/progress.md`.
> **Self-contained:** this plan is the COMPLETE spec. It must be executable by an agent with **no other context** — a fresh post-`/compact` session, or an external executor in a separate process. Every file path, contract, config key, and convention it needs is written here **verbatim**.

**Ticket:** CAT-1
**Ledger:** .gaspol/progress/PROGRESS-CAT-1.md
**Spec:** docs/plans/2026-08-22-CAT-1-gaspol-catalog-spec.md
**Artifact:** https://claude.ai/code/artifact/063c6ff8-5dee-491f-939f-87efba7ca2b4

## Goal

Bangun `gaspol-catalog` — plugin Claude Code yang menghasilkan katalog dan deck
penjualan B2B untuk pembeli **owner perusahaan logistik**, bukan investor. Plugin
mewarisi ±70% mesin review `gaspol-pitch` (arsitektur 6 lapis, kontrak uji-diri
fixture, verdict BLOCKING) dan menukar ±30% muatannya (rubrik pembeli menggantikan
rubrik VC), lalu menambah tiga lapis yang tidak ada di mana pun: review render,
pagar kejujuran per-operator, dan cek keabsahan kit urgensi.

Rencana ini mencakup **sub-proyek A (riset pembeli)** dan **sub-proyek B (mesin)**.
Selesai saat fixture `bad-catalog.md` kena BLOCKING dan `good-catalog.md` PASS.
Sub-proyek C (deliverable INDUSIA AI Cargo) dapat rencana sendiri setelah mesin ini
terbukti.

## Architecture Context

Dua repo tersentuh. **Baca CLAUDE.md masing-masing sebelum menyentuh filenya.**

### Repo 1 — `/Users/alisadikin/Drive-D/Projects/claude-plugin/gaspol-pitch` (ada)

Plugin investor yang jadi induk warisan. Satu file dimodifikasi (Phase H).

Struktur sekarang:

```
gaspol-pitch/
  .claude-plugin/plugin.json     name: gaspol-pitch, version 0.1.0
  CLAUDE.md                      aturan plugin + gaspol Ticket Counter (prefix CAT)
  skills/
    gaspol-pitch/SKILL.md        orchestrator
    pitch-discovery/SKILL.md
    pitch-narrative/SKILL.md
    pitch-draft/SKILL.md
    pitch-review/SKILL.md        111 baris — SATU-SATUNYA yang dimodifikasi rencana ini
    pitch-visual/SKILL.md
    pitch-finish/SKILL.md
  references/
    investor-deck-rubric.md      55 baris — lint biner PART A-D
    vc-review-rubric.md          90 baris — skor /55 + ABC + lapis keyakinan
    unit-economics.md            49 baris
    vc-fundamentals.md           68 baris
    business-model.md            52 baris
    deck-narrative.md            64 baris
    hormozi-offer.md             58 baris
    deck-design.md               88 baris
    examples/good-deck.md        87 baris — fixture WAJIB PASS
    examples/bad-deck.md         60 baris — fixture WAJIB BLOCKING
  docs/plans/                    spec + plan
```

Aturan keras `gaspol-pitch/CLAUDE.md` yang **tetap berlaku** dan diwarisi
`gaspol-catalog`:

1. Jangan pernah lewati fase review sebelum fase finish.
2. Bundel hanya pengetahuan generik. Pengetahuan satu-perusahaan / satu-acara /
   satu-identitas adalah **runtime input**, tidak pernah di-commit ke `references/`.
3. Brand chrome (logo, palet, font, @handle) = runtime input, selalu placeholder.
4. Kebijakan render = gambar full-slide + jaring pengaman QA.
5. Image MCP opsional — selalu emit prompt; PNG adalah langkah terpisah.
6. Frontmatter `SKILL.md` = **hanya** `name` + `description`. Tidak ada kunci YAML lain.

### Repo 2 — `/Users/alisadikin/Drive-D/Projects/claude-plugin/gaspol-catalog` (BARU)

Dibuat Phase C. Repo git sendiri, sejajar `gaspol-pitch` dan `gaspol-dev`.

### Otak domain — Obsidian vault (SUMBER, dibaca saja, tidak pernah ditulis)

- `/Users/alisadikin/Drive-D/Obsidian-Vault/30-Knowledge/deck-b2b-standard.md`
  (51 KB) — §1 kapan berlaku · §2 nol wajah · §3 palet ko-branding · §4 kontrak
  visual 60/40 · §5 arsitektur deck (§5.1 cover · §5.2 pasangan cover-penutup ·
  §5.3 slide DAMPAK · §5.4 pita tepi babak · §5.5 cliffhanger glyph) · §6 pagar
  kejujuran · §7 harga/jangkar/kit urgensi · §8 distribusi PDF · §10 format &
  pagar hukum · §11 asumsi & penjualan berfase · §12 menu à-la-carte · §13 coret
  harga presisi · §14 band termin · **§0b** temuan render Tranzporter · **§0c**
  putaran 20 Tranzporter · **§0d** temuan render Ekaputra.
- `/Users/alisadikin/Drive-D/Obsidian-Vault/30-Knowledge/playbook-cargo-logistics.md`
  (59 KB) — domain cargo, positioning platform, varian smart-gate.
- `/Users/alisadikin/Drive-D/Obsidian-Vault/20-Projects/IRN-Logistik/README.md` —
  angka kapabilitas terverifikasi + pagar kejujuran per-operator.

**Aturan penyalinan (keputusan D2 di spec):** yang disalin ke `references/b2b/`
adalah **polanya**, digenerikkan. Nama klien, kota, harga, warna hex spesifik
TIDAK ikut. `tests/guard-generic.sh` menegakkan ini secara mekanis.

## Tech Stack

Tidak ada. `detect-stack` pada `gaspol-pitch` mencetak **nol baris** — repo ini
murni markdown, tanpa `package.json`, `Makefile`, atau harness uji.

Konsekuensinya untuk rencana ini:

- Verifikasi otomatis = **skrip bash yang ditulis rencana ini sendiri**, di
  `gaspol-catalog/tests/`. Isinya ditulis verbatim di bawah, bukan dirujuk.
- Verifikasi kontrak fixture = **dijalankan agen**, bukan skrip. Ditandai eksplisit
  di tiap Verification block yang memakainya.
- Bahasa: bash (POSIX + `grep -E`), tersedia di macOS darwin 25.5.0 tanpa install.
- `ponytail:` sengaja tidak menambah runner uji berbasis Node/Python. Menambah
  toolchain untuk memeriksa file markdown melanggar tangga kemalasan rung 2-4.

## Data Integration Map

| Feature | Data Source | Hook/API | Exists? | Action |
|---|---|---|---|---|
| Framework buku (mom-test, storybrand, obviously-awesome, dll) | `getagentseal/founder-playbook`, 16 skill | `~/.agents/skills/<name>/SKILL.md` | **Ya, di disk** — belum di-symlink ke `~/.claude/skills/` | Symlink (Phase A) |
| Metodologi sales B2B | `guia-matthieu/clawfu-skills` → `skills/sales/` | `npx skills add` | Tidak | Install 5 skill (Phase A) |
| Positioning & pricing | `guia-matthieu/clawfu-skills` → `skills/strategy/` | `npx skills add` | Tidak | Install 2 skill (Phase A) |
| Kerangka kit penjualan | `gtmagents/gtm-agents@enablement-kit` | — | Pola saja | Salin strukturnya, jangan install (Phase G) |
| Riset perilaku pembeli | Firecrawl MCP + arsip internal + NotebookLM | `mcp__firecrawl__firecrawl_search` / `firecrawl_scrape`; `notebooklm` CLI | Tidak | Buat `research/buyer-brain.md` (Phase B) |
| Standar deck B2B | vault `30-Knowledge/deck-b2b-standard.md` | baca file langsung | Ya | Suling ke `references/b2b/` (Phase E, F, G) |
| Domain cargo | vault `30-Knowledge/playbook-cargo-logistics.md` | baca file langsung | Ya | Rujuk di Phase B |
| Mesin skor deck | `gaspol-pitch/skills/pitch-review/SKILL.md` | invoke skill | Ya | Beri parameter rubrik (Phase H) |
| Fixture kontrak investor | `gaspol-pitch/references/examples/{good,bad}-deck.md` | baca file langsung | Ya | Jadikan pola untuk fixture B2B (Phase D) |
| Palet & font slide | `gaspol-design` | invoke skill | **Opsional** | Degrade anggun ke `b2b-visual-contract.md` |
| Render PNG | image MCP | tool MCP | **Opsional** | Tanpa MCP → emit prompt saja, bukan kegagalan |
| Harga & modul produk | Ali (manusia) | — | **Tidak** | `catalog-master` BERHENTI dan bertanya. Haram dikarang |
| Ukuran armada prospek | Ali / dokumen prospek | — | **Tidak** | `catalog-variant` BERHENTI dan bertanya |

## Gotcha yang wajib dibaca sebelum Phase A

**Tabrakan nama `spin-selling`.** Skill bernama `spin-selling` ada di **dua** paket:
`getagentseal/founder-playbook` dan `guia-matthieu/clawfu-skills`
(`skills/sales/spin-selling`). Keduanya tidak bisa hidup bersama di
`~/.claude/skills/` karena nama direktorinya sama.

Keputusan: **pakai milik `founder-playbook`** (sudah di disk, sudah tercatat di
vault `30-Knowledge/founder-playbook-skills.md`). Dari clawfu ambil **5** skill
sales saja — `challenger-sale`, `meddic-scorecard`, `sandler-system`,
`never-split-difference`, `sales-narrative` — dan **2** skill strategy —
`positioning`, `pricing-strategy`. Jangan install `spin-selling` clawfu.

Kalau `npx skills add` menolak instalasi per-skill dan memaksa seluruh repo,
STOP dan tanya user sebelum menimpa `spin-selling` yang ada.

---

## Phases

### Phase A: Prasyarat skill — symlink founder-playbook + install clawfu

**Estimated time:** 12 menit

**Files:**
- Create: `/Users/alisadikin/Drive-D/Projects/claude-plugin/gaspol-catalog/tests/deps-present.sh`
- Modify: `~/.claude/skills/` (symlink baru, 16 buah)

**Steps:**

1. Write failing test for kehadiran 19 skill prasyarat di `~/.claude/skills/`.
   Expected error: `MISSING founder-playbook: mom-test` (dan 18 baris sejenis),
   skrip exit 1.

   Buat direktori repo dulu jika belum ada:
   `mkdir -p /Users/alisadikin/Drive-D/Projects/claude-plugin/gaspol-catalog/tests`

   Tulis `tests/deps-present.sh` **verbatim**:

   ```bash
   #!/usr/bin/env bash
   # CAT-1 Phase A — 19 skill prasyarat harus bisa dirouting Claude Code.
   set -uo pipefail
   missing=0

   # getagentseal/founder-playbook — 12 dipakai (dari 16 terpasang)
   for s in mom-test lean-startup storybrand made-to-stick influence \
            obviously-awesome 100m-offers monetizing-innovation traction \
            blue-ocean-strategy spin-selling crossing-the-chasm; do
     [ -e "$HOME/.claude/skills/$s/SKILL.md" ] \
       || { echo "MISSING founder-playbook: $s"; missing=1; }
   done

   # guia-matthieu/clawfu-skills — 7 (spin-selling SENGAJA tidak diambil, lihat gotcha)
   for s in challenger-sale meddic-scorecard sandler-system \
            never-split-difference sales-narrative positioning pricing-strategy; do
     [ -e "$HOME/.claude/skills/$s/SKILL.md" ] \
       || { echo "MISSING clawfu: $s"; missing=1; }
   done

   [ "$missing" -eq 0 ] && echo "deps OK — 19/19"
   exit "$missing"
   ```

   `chmod +x tests/deps-present.sh`

2. Jalankan `bash tests/deps-present.sh`, konfirmasi gagal dengan 19 baris MISSING.

3. Symlink 12 skill founder-playbook dari `~/.agents/skills/` ke `~/.claude/skills/`.
   Sumbernya **sudah ada di disk** — jangan download ulang:

   ```bash
   for s in mom-test lean-startup storybrand made-to-stick influence \
            obviously-awesome 100m-offers monetizing-innovation traction \
            blue-ocean-strategy spin-selling crossing-the-chasm; do
     ln -sfn "$HOME/.agents/skills/$s" "$HOME/.claude/skills/$s"
   done
   ```

4. Jalankan ulang `bash tests/deps-present.sh` — 12 baris founder-playbook hilang,
   7 baris clawfu tersisa.

5. Install 7 skill clawfu. Perintah tepatnya:

   ```bash
   npx --yes skills add https://github.com/guia-matthieu/clawfu-skills \
     --skill challenger-sale --skill meddic-scorecard --skill sandler-system \
     --skill never-split-difference --skill sales-narrative \
     --skill positioning --skill pricing-strategy
   ```

   Kalau flag `--skill` tidak menerima banyak nilai dalam satu perintah, jalankan
   satu per satu. Kalau CLI memaksa memasang seluruh repo (175 skill), **STOP dan
   tanya user** — memasang 175 skill akan menimpa `spin-selling` dan membanjiri
   daftar skill.

6. Jalankan ulang `bash tests/deps-present.sh`, konfirmasi `deps OK — 19/19`.

7. Commit: `test: add CAT-1 dependency presence check for 19 prerequisite skills`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/deps-present.sh` exit 0, mencetak `deps OK — 19/19`
- [ ] `ls -l ~/.claude/skills/spin-selling` menunjuk ke `~/.agents/skills/spin-selling`, bukan ke cache clawfu
- [ ] `ls ~/.claude/skills/ | wc -l` naik tepat 19 dari nilai sebelumnya (catat nilai awal sebelum Phase A dimulai)
- [ ] No placeholder/TODO comments in new code

---

### Phase B: Riset pembeli (sub-proyek A) — `research/buyer-brain.md`

**Estimated time:** 45 menit (jalan terlama di rencana ini; sebagian besar menunggu I/O)

**Files:**
- Create: `gaspol-catalog/research/buyer-brain.md`
- Create: `gaspol-catalog/research/sources.md`
- Create: `gaspol-catalog/tests/research-contract.sh`

**Steps:**

1. Write failing test for kontrak keluaran riset. Expected error:
   `MISSING: research/buyer-brain.md`, skrip exit 1.

   Tulis `tests/research-contract.sh` **verbatim**:

   ```bash
   #!/usr/bin/env bash
   # CAT-1 Phase B — riset pembeli wajib punya bentuk yang bisa dikonsumsi rubrik.
   set -uo pipefail
   cd "$(dirname "$0")/.." || exit 1
   fail=0

   for f in research/buyer-brain.md research/sources.md; do
     [ -f "$f" ] || { echo "MISSING: $f"; fail=1; }
   done
   [ "$fail" -eq 1 ] && exit 1

   # Enam bagian wajib — ini yang dikonsumsi b2b-buyer-rubric.md (Phase F)
   for h in "## Pemicu keputusan" "## Keberatan" "## Bahasa pembeli" \
            "## Bukti yang dipercaya" "## Pola pengadaan" "## Anti-pola"; do
     grep -qF "$h" research/buyer-brain.md \
       || { echo "MISSING heading: $h"; fail=1; }
   done

   # Tiap klaim wajib punya penanda sumber [S<n>]; nol penanda = riset tak tertelusur
   claims=$(grep -cE '^\- ' research/buyer-brain.md || echo 0)
   sourced=$(grep -cE '^\- .*\[S[0-9]+\]' research/buyer-brain.md || echo 0)
   if [ "$claims" -ne "$sourced" ]; then
     echo "UNSOURCED: $((claims - sourced)) dari $claims klaim tanpa penanda [S<n>]"
     fail=1
   fi

   # Tiap [S<n>] yang dipakai wajib terdaftar di sources.md
   for n in $(grep -oE '\[S[0-9]+\]' research/buyer-brain.md | sort -u | tr -d '[]S'); do
     grep -qE "^S${n}\b" research/sources.md \
       || { echo "DANGLING: [S${n}] tidak ada di sources.md"; fail=1; }
   done

   [ "$fail" -eq 0 ] && echo "research contract OK — $claims klaim, semua tertelusur"
   exit "$fail"
   ```

   `chmod +x tests/research-contract.sh`

2. Jalankan `bash tests/research-contract.sh`, konfirmasi gagal dengan
   `MISSING: research/buyer-brain.md`.

3. Kumpulkan sumber eksternal lewat Firecrawl MCP. Minimal 12 sumber, sasaran
   pencarian (bahasa Indonesia dan Inggris):
   - asosiasi: ALFI/ILFA, Aptrindo, GINSI, GPEI, Organda angkutan barang
   - keluhan dan biaya operasional: pencurian solar, idle truk, demurrage,
     detensi kontainer, ritase, uang jalan
   - pengadaan pelabuhan Indonesia: NPK/NLE, Inaportnet, autogate terminal
   - adopsi teknologi armada: GPS tracking, fleet management, alasan gagal adopsi
   - kota sasaran: Batam, Pekanbaru, Medan, Surabaya, Semarang, Makassar, Jakarta

   Simpan tiap sumber sebagai baris `S<n> | <judul> | <URL> | <tanggal akses>` di
   `research/sources.md`.

4. Kumpulkan sumber internal. Baca dan daftarkan sebagai `S<n>` juga:
   - `/Users/alisadikin/Drive-D/Obsidian-Vault/30-Knowledge/playbook-cargo-logistics.md`
   - `/Users/alisadikin/Drive-D/Obsidian-Vault/30-Knowledge/deck-b2b-standard.md` §6, §7, §11, §12
   - `/Users/alisadikin/Drive-D/Obsidian-Vault/20-Projects/IRN-Logistik/README.md`
   - `/Users/alisadikin/Drive-D/Projects/indusia-ai-logistics/docs/content/irn-public-content-contract.md`
   - direktori `research/` di `/Users/alisadikin/Drive-D/Projects/indusia-ai-logistics` bila ada

5. Buat notebook NotebookLM dan masukkan sumber eksternal sebagai sumber terkutip.
   CLI sudah terpasang dan terautentikasi (profil `default`, `notebooklm doctor`
   semua hijau):

   ```bash
   notebooklm create "CAT-1 buyer research — logistik Indonesia"
   notebooklm use <id-yang-dikembalikan>
   # tambahkan tiap URL sebagai sumber, lalu:
   notebooklm ask "Apa pemicu keputusan pembelian sistem armada bagi pemilik perusahaan trucking di Indonesia? Kutip sumbernya."
   ```

   **PERINGATAN, dari vault 2026-08-08:** NotebookLM tercatat **mengarang** —
   pernah mengklaim dokumen 111 halaman padahal aslinya 22, dan salah memetakan
   struktur chart. Setiap klaim yang masuk `buyer-brain.md` **wajib** ditelusuri
   balik ke sumber aslinya, bukan diterima dari ringkasan NotebookLM. Kalau sebuah
   klaim tidak bisa ditelusuri, buang klaimnya — jangan tandai perkiraan.

   Kalau `notebooklm` gagal (auth kedaluwarsa, API berubah), ini **bukan pemblokir**:
   lanjutkan sintesis langsung dari sumber Firecrawl + arsip internal, dan catat
   di `sources.md` bahwa NotebookLM dilewati beserta alasannya.

6. Tulis `research/buyer-brain.md` dengan enam bagian wajib. Tiap butir satu baris
   `- ` diakhiri penanda `[S<n>]`:
   - `## Pemicu keputusan` — apa yang membuat owner bergerak dari "menarik" ke "beli"
   - `## Keberatan` — keberatan yang benar-benar diucapkan, beserta bentuk jawabannya
   - `## Bahasa pembeli` — istilah yang dipakai owner sendiri (ritase, uang jalan,
     susut solar, demurrage) versus istilah vendor yang membunuh percakapan
   - `## Bukti yang dipercaya` — bentuk bukti yang mengubah pikiran, berperingkat
   - `## Pola pengadaan` — siapa memutuskan, siapa memveto, termin, siklus anggaran
   - `## Anti-pola` — yang membuat owner langsung menutup deck

7. Jalankan `bash tests/research-contract.sh`, konfirmasi lolos.

8. Commit: `feat: add CAT-1 buyer research brain with source-traceable claims`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/research-contract.sh` exit 0
- [ ] `research/sources.md` memuat **≥ 12** sumber eksternal berbeda dan **≥ 4** sumber internal
- [ ] Setiap bagian `##` di `buyer-brain.md` punya **≥ 3** butir
- [ ] Nol klaim tanpa penanda `[S<n>]` (ditegakkan skrip)
- [ ] Nol angka yang berasal hanya dari ringkasan NotebookLM — tiap angka ada di sumber aslinya
- [ ] No placeholder/TODO comments in new code

---

### Phase C: Kerangka repo `gaspol-catalog` + harness uji

**Estimated time:** 15 menit

**Files:**
- Create: `gaspol-catalog/.claude-plugin/plugin.json`
- Create: `gaspol-catalog/CLAUDE.md`
- Create: `gaspol-catalog/.gitignore`
- Create: `gaspol-catalog/README.md`
- Create: `gaspol-catalog/tests/guard-generic.sh`
- Create: `gaspol-catalog/tests/frontmatter.sh`
- Create: `gaspol-catalog/tests/run-all.sh`

**Steps:**

1. Write failing test for pagar generik atas `references/`. Expected error:
   `GUARD ERROR: references/ tidak ada`, skrip exit 1 (direktorinya memang belum ada).

   Tulis `tests/guard-generic.sh` **verbatim**:

   ```bash
   #!/usr/bin/env bash
   # CAT-1 Phase C — references/ hanya boleh memuat pengetahuan GENERIK.
   # Nama klien, kota sasaran, warna merek, dan identitas pribadi adalah
   # runtime input — lihat gaspol-pitch/CLAUDE.md aturan keras #2 dan #3.
   set -uo pipefail
   cd "$(dirname "$0")/.." || exit 1

   [ -d references ] || { echo "GUARD ERROR: references/ tidak ada"; exit 1; }

   PAT='indusia|irn[ -]?cargo|indrajaya|yafindo|ekaputra|tranzporter|global pratama|hub71|alisadikin|0F59B6|batam|pekanbaru|makassar'
   hits=$(grep -rniE "$PAT" references/ 2>/dev/null || true)
   if [ -n "$hits" ]; then
     echo "GUARD FAIL — isi spesifik-klien di references/:"
     echo "$hits"
     exit 1
   fi
   echo "guard OK — references/ generik"
   ```

   `chmod +x tests/guard-generic.sh`

2. Jalankan `bash tests/guard-generic.sh`, konfirmasi gagal dengan
   `GUARD ERROR: references/ tidak ada`.

3. Tulis `tests/frontmatter.sh` **verbatim** — menegakkan aturan keras #6
   (frontmatter hanya `name` + `description`):

   ```bash
   #!/usr/bin/env bash
   # CAT-1 Phase C — SKILL.md frontmatter hanya boleh punya name + description.
   set -uo pipefail
   cd "$(dirname "$0")/.." || exit 1
   fail=0
   found=0

   for f in skills/*/SKILL.md; do
     [ -f "$f" ] || continue
     found=$((found + 1))
     head -1 "$f" | grep -qx -- '---' || { echo "$f: baris 1 bukan ---"; fail=1; continue; }
     keys=$(awk 'NR>1{ if ($0 == "---") exit; if ($0 ~ /^[A-Za-z_-]+:/) { sub(/:.*/, ""); print } }' "$f")
     [ -n "$keys" ] || { echo "$f: frontmatter kosong"; fail=1; }
     for k in $keys; do
       case "$k" in
         name|description) ;;
         *) echo "$f: kunci frontmatter terlarang '$k'"; fail=1 ;;
       esac
     done
     for req in name description; do
       printf '%s\n' "$keys" | grep -qx -- "$req" || { echo "$f: '$req' hilang"; fail=1; }
     done
   done

   [ "$found" -eq 0 ] && { echo "frontmatter: nol SKILL.md ditemukan"; exit 1; }
   [ "$fail" -eq 0 ] && echo "frontmatter OK — $found skill"
   exit "$fail"
   ```

   `chmod +x tests/frontmatter.sh`

4. Tulis `tests/run-all.sh` **verbatim**:

   ```bash
   #!/usr/bin/env bash
   # CAT-1 — gerbang uji gaspol-catalog. Nol dependensi selain bash + grep + awk.
   set -uo pipefail
   cd "$(dirname "$0")" || exit 1
   rc=0
   for t in deps-present.sh research-contract.sh guard-generic.sh \
            frontmatter.sh refs-present.sh fixture-shape.sh; do
     [ -f "$t" ] || { echo "SKIP $t (belum ada)"; continue; }
     echo "=== $t"
     bash "$t" || rc=1
   done
   [ "$rc" -eq 0 ] && echo "ALL GREEN" || echo "SOME RED"
   exit "$rc"
   ```

   `chmod +x tests/run-all.sh`

5. Buat `references/b2b/examples/` (kosong dulu) supaya guard punya sasaran:
   `mkdir -p references/b2b/examples`

6. Jalankan `bash tests/guard-generic.sh`, konfirmasi lolos dengan
   `guard OK — references/ generik`.

7. Tulis `.claude-plugin/plugin.json` **verbatim**:

   ```json
   {
     "name": "gaspol-catalog",
     "description": "Orchestrates B2B sales catalog and offer-deck creation for operations buyers, not investors: master catalog once, per-prospect variant many times, and a two-layer blocking gate that reviews the text AND the rendered image. Inherits the adversarial review engine from gaspol-pitch and swaps the rubric.",
     "version": "0.1.0",
     "author": { "name": "Ali Sadikin", "url": "https://github.com/alisadikinma" },
     "homepage": "https://github.com/alisadikinma/gaspol-catalog",
     "repository": "https://github.com/alisadikinma/gaspol-catalog",
     "license": "MIT",
     "keywords": [
       "sales-deck", "b2b-sales", "proposal", "catalog", "offer",
       "adversarial-review", "render-review", "claude-code"
     ]
   }
   ```

8. Tulis `.gitignore` dengan isi: `.DS_Store`, `*.pdf`, `*.pptx`, `*.png`,
   `.gaspol/`, `graphify-out/`, `.claude/`, `research/raw/`.

9. Tulis `CLAUDE.md` yang memuat, minimal: prinsip reuse, tabel reuse map per skill,
   indeks `references/b2b/`, enam aturan keras (disalin dari `gaspol-pitch/CLAUDE.md`
   dengan aturan #2 diperluas menyebut kota sasaran), dan bagian
   `## gaspol Ticket Counter` dengan `Prefix: CAT` / `Last ticket: CAT-1`.

10. `git init` di `gaspol-catalog/`, commit: `feat: scaffold gaspol-catalog plugin with generic-content guard`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/guard-generic.sh` exit 0
- [ ] `bash tests/run-all.sh` menjalankan skrip yang ada dan melewati yang belum ada tanpa error
- [ ] `python3 -c "import json;json.load(open('.claude-plugin/plugin.json'))"` exit 0
- [ ] `gaspol-catalog/CLAUDE.md` memuat keenam aturan keras dan tabel reuse map
- [ ] No placeholder/TODO comments in new code

---

### Phase D: Fixture kontrak — `good-catalog.md` + `bad-catalog.md`

**Estimated time:** 20 menit

Fixture ditulis **sebelum** rubriknya. Itu inti kontrak uji-diri yang diwarisi:
rubrik dinilai benar hanya kalau ia menangkap fixture buruk dan meloloskan yang baik.

**Files:**
- Create: `gaspol-catalog/references/b2b/examples/bad-catalog.md`
- Create: `gaspol-catalog/references/b2b/examples/good-catalog.md`
- Create: `gaspol-catalog/tests/fixture-shape.sh`

**Steps:**

1. Write failing test for keberadaan cacat yang sengaja ditanam di fixture buruk.
   Expected error: `MISSING fixture: references/b2b/examples/bad-catalog.md`,
   skrip exit 1.

   Tulis `tests/fixture-shape.sh` **verbatim**. Skrip ini mencegah **fixture
   membusuk** — kalau seseorang "merapikan" `bad-catalog.md` dan menghapus
   cacatnya, uji kontrak jadi bohong tanpa ada yang tahu:

   ```bash
   #!/usr/bin/env bash
   # CAT-1 Phase D — fixture buruk WAJIB tetap memuat cacat yang ditanam.
   set -uo pipefail
   cd "$(dirname "$0")/.." || exit 1
   B=references/b2b/examples/bad-catalog.md
   G=references/b2b/examples/good-catalog.md
   fail=0

   for f in "$B" "$G"; do
     [ -f "$f" ] || { echo "MISSING fixture: $f"; fail=1; }
   done
   [ "$fail" -eq 1 ] && exit 1

   # Enam cacat yang ditanam, satu per lapis review (lihat b2b-deck-rubric.md)
   grep -qiE 'end-to-end terintegrasi|world-class|solusi menyeluruh' "$B" \
     || { echo "bad fixture kehilangan cacat: fluff B2B (lapis a)"; fail=1; }
   grep -qE '^## (Harga|Solusi|Manfaat)\s*$' "$B" \
     || { echo "bad fixture kehilangan cacat: headline label (lapis a)"; fail=1; }
   awk '/^# COVER/,/^## /' "$B" | grep -qE 'Rp|IDR|[0-9]+ *(juta|jt|miliar)' \
     || { echo "bad fixture kehilangan cacat: harga di cover (lapis b)"; fail=1; }
   grep -qiE 'foto (founder|pendiri)|potret (founder|pendiri)|wajah' "$B" \
     || { echo "bad fixture kehilangan cacat: wajah manusia (lapis g)"; fail=1; }
   grep -qiE 'sudah terbukti di ratusan|telah digunakan ribuan' "$B" \
     || { echo "bad fixture kehilangan cacat: klaim kematangan palsu (lapis h)"; fail=1; }
   grep -qiE 'promo terbatas|hanya hari ini|kuota tinggal' "$B" \
     || { echo "bad fixture kehilangan cacat: urgensi tak berlaku (lapis i)"; fail=1; }

   # Fixture baik WAJIB bersih dari keenamnya
   grep -qiE 'world-class|end-to-end terintegrasi' "$G" \
     && { echo "good fixture tercemar fluff"; fail=1; }
   awk '/^# COVER/,/^## /' "$G" | grep -qE 'Rp|IDR' \
     && { echo "good fixture menaruh harga di cover"; fail=1; }

   [ "$fail" -eq 0 ] && echo "fixture shape OK"
   exit "$fail"
   ```

   `chmod +x tests/fixture-shape.sh`

2. Jalankan `bash tests/fixture-shape.sh`, konfirmasi gagal dengan
   `MISSING fixture: references/b2b/examples/bad-catalog.md`.

3. Tulis `bad-catalog.md` — deck penawaran fiktif untuk perusahaan generik
   ("PT Contoh Angkutan"), 10 slide, memuat keenam cacat di atas **plus** dua
   cacat kuantitatif tanpa penanda grep: satu angka yang tidak rekonsiliasi
   antar-slide (harga paket di slide 7 tidak sama dengan total di slide 9) dan
   satu slide yang mencampur dua pesan lewat "dan".

   Nama perusahaan, kota, dan angka harus **fiktif dan generik** — guard Phase C
   akan menolak `references/` yang menyebut klien atau kota nyata.

4. Tulis `good-catalog.md` — deck yang sama-sama 10 slide untuk perusahaan fiktif
   yang sama, memenuhi seluruh kontrak: headline asersi, cover tanpa harga
   (kicker · headline masalah↔sistem · durasi proyek · tenggat harga), slide DAMPAK
   sebelum/sesudah **nol rupiah nol persen**, penutup menggemakan cover dengan
   klausa terakhir diganti, satu perintah saja di penutup, angka rekonsiliasi
   lintas-slide, kematangan platform dan modul dipisah eksplisit, kit urgensi yang
   benar-benar berlaku dan disebutkan dasarnya.

5. Jalankan `bash tests/fixture-shape.sh`, konfirmasi lolos.

6. Commit: `test: add CAT-1 good/bad catalog fixtures with rot guard`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/fixture-shape.sh` exit 0
- [ ] `bash tests/guard-generic.sh` exit 0 (fixture tidak menyebut klien/kota nyata)
- [ ] `bad-catalog.md` dan `good-catalog.md` sama-sama 10 slide, perusahaan fiktif sama
- [ ] Kedua fixture menyebut **tiap** cacat/kepatuhan pada slide yang jelas nomornya
- [ ] No placeholder/TODO comments in new code

---

### Phase E: `references/b2b/b2b-deck-rubric.md` — lint biner

**Estimated time:** 15 menit

**Files:**
- Create: `gaspol-catalog/references/b2b/b2b-deck-rubric.md`
- Create: `gaspol-catalog/tests/refs-present.sh`

**Steps:**

1. Write failing test for kelengkapan berkas rubrik yang dimuat skill.
   Expected error: `MISSING ref: references/b2b/b2b-deck-rubric.md`, skrip exit 1.

   Tulis `tests/refs-present.sh` **verbatim**:

   ```bash
   #!/usr/bin/env bash
   # CAT-1 — tiap berkas references/b2b yang dimuat skill harus ada dan tidak kosong.
   set -uo pipefail
   cd "$(dirname "$0")/.." || exit 1
   fail=0
   for f in b2b-deck-rubric.md b2b-buyer-rubric.md b2b-deck-architecture.md \
            b2b-honesty-rails.md b2b-visual-contract.md b2b-phased-sale.md \
            enablement-kit.md; do
     p="references/b2b/$f"
     if [ ! -f "$p" ]; then echo "MISSING ref: $p"; fail=1; continue; fi
     n=$(wc -l < "$p")
     [ "$n" -ge 20 ] || { echo "TOO THIN: $p ($n baris, minimal 20)"; fail=1; }
   done
   [ "$fail" -eq 0 ] && echo "refs OK — 7 berkas"
   exit "$fail"
   ```

   `chmod +x tests/refs-present.sh`

2. Jalankan `bash tests/refs-present.sh`, konfirmasi gagal dengan 7 baris MISSING.

3. Tulis `b2b-deck-rubric.md`. **Sumber pola:**
   `gaspol-pitch/references/investor-deck-rubric.md` PART C (9 cek biner).

   Tujuh cek pindah **verbatim**, hanya contohnya diganti ke konteks pembeli
   operasional: `Value-Anchor` · `Pyramid Principle` · `Assertion-vs-Topic` ·
   `One-Message` · `Linguistic-Fluff` · `5-Second Rule` · `Data-Parsing`.

   `Forwardable Test` pindah dan **naik status jadi wajib**, dengan alasannya
   ditulis: dokumen penawaran beredar tanpa penyaji, dibuka di rapat oleh orang
   yang tidak hadir, jadi tiap slide harus berdiri sendiri.

   `Platform-Risk` **ditulis ulang** jadi vendor lock-in, SLA, kepemilikan data,
   dan kelanjutan layanan.

   Daftar fluff diganti ke fluff B2B Indonesia — "solusi end-to-end terintegrasi",
   "solusi menyeluruh", "world-class", "berbasis AI terdepan", "transformasi
   digital", "sinergi".

   Berkas ini **tidak** boleh memuat nama klien, kota, atau angka nyata.

4. Jalankan `bash tests/refs-present.sh` — 1 baris MISSING hilang, 6 tersisa.

5. Jalankan `bash tests/guard-generic.sh`, konfirmasi masih lolos.

6. Commit: `feat: add CAT-1 binary B2B deck rubric (7 checks inherited, 2 rewritten)`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/guard-generic.sh` exit 0
- [ ] `b2b-deck-rubric.md` memuat tepat **9** cek biner, tiap cek punya baris PASS dan baris FAIL
- [ ] Ketujuh cek warisan bisa dipetakan satu-satu ke `gaspol-pitch/references/investor-deck-rubric.md` PART C
- [ ] `Platform-Risk` tidak lagi menyebut API tertutup; `Forwardable` ditandai wajib
- [ ] No placeholder/TODO comments in new code

---

### Phase F: `references/b2b/b2b-buyer-rubric.md` — skor pembeli

**Estimated time:** 20 menit
**Depends on:** Phase B (`research/buyer-brain.md`)

**Files:**
- Create: `gaspol-catalog/references/b2b/b2b-buyer-rubric.md`

**Steps:**

1. Write failing test for keberadaan rubrik skor pembeli — dijalankan lewat
   `tests/refs-present.sh` yang sudah ada. Expected error:
   `MISSING ref: references/b2b/b2b-buyer-rubric.md`, skrip exit 1.

2. Jalankan `bash tests/refs-present.sh`, konfirmasi baris MISSING itu muncul.

3. Baca `research/buyer-brain.md` bagian `## Pemicu keputusan`, `## Keberatan`,
   `## Bukti yang dipercaya`, dan `## Anti-pola`. Dimensi rubrik **lahir dari
   sana**, bukan dari tebakan.

4. Tulis `b2b-buyer-rubric.md`. **Sumber pola:**
   `gaspol-pitch/references/vc-review-rubric.md`. Yang diwarisi bentuknya:
   - tabel dimensi berskor 1-5 dengan definisi "apa yang 5 terlihat seperti" dan
     "apa yang 1-2 terlihat seperti", plus perbaikan konkret untuk tiap dimensi < 3
   - pita nilai + ambang BLOCKING
   - uji ganda ABC per slide → di sini jadi: apakah slide ini **menurunkan risiko
     pembeli** atau **menaikkan nilai hasil bagi pembeli**; kalau tidak keduanya,
     kandidat potong
   - lapis keyakinan (Covered/Thin/Missing) → di sini empat lapis pembeli:
     **Masalah** (prospek melihat masalahnya sendiri) · **Mekanisme** (paham cara
     kerjanya) · **Bukti** (percaya sistemnya jalan) · **Risiko** (tahu jalan
     keluarnya kalau gagal)
   - konsistensi lintas-slide → harga ↔ paket ↔ linimasa ↔ klaim hemat
   - gerbang naratif → gantikan *earned secret* dengan **gerbang bukti operasi**;
     gantikan *dinner test* dengan "apa yang manajer ops ulangi ke ownernya"

   Dimensi berskor **wajib** memuat, minimal, dimensi yang menilai apakah prospek
   melihat masalahnya sendiri di deck — itu yang paling absen di rubrik investor.

   Berkas ini **tidak** memuat nama klien, kota, harga nyata, atau kutipan langsung
   dari `buyer-brain.md` yang menyebut sumber spesifik-klien.

5. Jalankan `bash tests/refs-present.sh` — 5 baris MISSING tersisa.

6. Jalankan `bash tests/guard-generic.sh`, konfirmasi masih lolos.

7. Commit: `feat: add CAT-1 scored buyer rubric grounded in buyer research`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/guard-generic.sh` exit 0
- [ ] Tiap dimensi berskor bisa ditelusuri ke satu butir `research/buyer-brain.md` — cantumkan penanda `[S<n>]`-nya di komentar rubrik
- [ ] Empat lapis keyakinan pembeli lengkap dengan definisi Covered/Thin/Missing
- [ ] Ambang BLOCKING dinyatakan sebagai angka, bukan kata sifat
- [ ] Nol dimensi warisan VC yang tidak relevan (TAM, moat, the ask, dilusi) tersisa
- [ ] No placeholder/TODO comments in new code

---

### Phase G: Lima berkas referensi sisanya

**Estimated time:** 25 menit

**Files:**
- Create: `gaspol-catalog/references/b2b/b2b-deck-architecture.md`
- Create: `gaspol-catalog/references/b2b/b2b-honesty-rails.md`
- Create: `gaspol-catalog/references/b2b/b2b-visual-contract.md`
- Create: `gaspol-catalog/references/b2b/b2b-phased-sale.md`
- Create: `gaspol-catalog/references/b2b/enablement-kit.md`

**Steps:**

1. Write failing test for kelima berkas — lewat `tests/refs-present.sh` yang sudah
   ada. Expected error: `MISSING ref: references/b2b/b2b-deck-architecture.md`
   dan 4 baris sejenis, skrip exit 1.

2. Jalankan `bash tests/refs-present.sh`, konfirmasi 5 baris MISSING.

3. Tulis `b2b-deck-architecture.md` — suling dari vault §5. Isi: arsitektur 8 babak
   `COVER → ISSUES → SOLUTIONS → DAMPAK → LINIMASA → NEXT → CTA → PENUTUP`; aturan
   cover (kicker · headline masalah↔sistem · durasi · tenggat; **tanpa harga**);
   cover dan penutup sebagai satu pasangan berkas dengan klausa terakhir diganti,
   bukan salinan persis; slide DAMPAK sebelum/sesudah wajib **nol rupiah nol
   persen**; penutup memuat satu perintah saja, dilarang menaruh peta jalan; aturan
   cliffhanger glyph antar-slide beserta peringatan jangan menjelaskan maksud glyph
   di prompt render yang sama.

4. Tulis `b2b-honesty-rails.md` — suling dari vault §6, §7, §11, §12, §13, §14.
   Isi: asumsi dibenarkan oleh sumbernya tidak pernah oleh hasilnya; ekstrapolasi
   N=1 sah tapi menyembunyikannya tidak; kematangan platform ≠ kematangan modul;
   pemisahan yang disengaja wajib dijahit ulang di titik temunya; enam alat urgensi
   yang semuanya harus benar-benar berlaku; jangkar mengikuti harga akhir bukan
   sebaliknya; gross dihitung mundur saat diskon persen adalah fakta; dua klaim
   "hemat" berbeda wajib saling menyebut sumbernya; band termin pembayaran terikat
   linimasa; menu à-la-carte wajib punya baris yang tidak bisa dilepas; harga per
   lokasi boleh, total seluruh lokasi tidak.

5. Tulis `b2b-visual-contract.md` — suling dari vault §2, §3, §4, §5.4, §0b, §0c-C,
   §0d. Isi: nol wajah manusia beserta alasannya (membuat pembeli menilai konsultan
   perorangan, bukan perusahaan yang bisa dikontrak); kontrak visual 60/40; palet
   ko-branding satu warna satu tugas; pita tepi babak ditempel tidak pernah
   digenerate, di tepi kiri, dengan alasan ketiga pojok selalu terpakai; rasio 16:9;
   **pagar render**: model gambar mengulang teks (kartu penuh dirender dua kali,
   label dobel dalam-luar wadah) dan mengarang angka, lockup logo ditempel bukan
   digenerate, frasa full-bleed harus menyebut kolom piksel pertama dan terakhir
   bukan "edge to edge", deteksi tinggi pita pakai proporsi piksel gelap bukan
   rata-rata luminansi.

6. Tulis `b2b-phased-sale.md` — suling dari vault §11.6, §11.7. Isi: model
   Survei → Pilot satu titik → Rollout; kriteria sukses pilot **wajib
   ditandatangani klien sebelum fase 2 dimulai** beserta alasannya (mencegah
   penolakan "tidak memenuhi ekspektasi saya" yang tidak pernah diungkapkan, dan
   memberi manajemen klien pelindung internal untuk keputusan lanjut/berhenti);
   struktur konsorsium dengan satu titik SLA menghadap pembeli; posisikan kemitraan
   jangka panjang, bukan eksekusi proyek pilot semata.

7. Tulis `enablement-kit.md` — kerangka 5 bagian dari `gtmagents/gtm-agents@enablement-kit`:
   Context Brief · Talk Tracks & Scripts · Collateral Pack · System Instructions ·
   Feedback Loop. Untuk tiap bagian, tulis apa isinya dalam konteks penawaran B2B
   dan apa bentuk berkas keluarannya.

8. Jalankan `bash tests/refs-present.sh`, konfirmasi `refs OK — 7 berkas`.

9. Jalankan `bash tests/guard-generic.sh`, konfirmasi lolos.

10. Commit: `feat: add CAT-1 architecture, honesty, visual, phased-sale and kit references`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/refs-present.sh` exit 0 — `refs OK — 7 berkas`
- [ ] `bash tests/guard-generic.sh` exit 0
- [ ] Tiap berkas menyebut **nomor seksi vault asalnya** di komentar sumber, supaya jejaknya bisa diaudit
- [ ] `b2b-visual-contract.md` memuat pagar anti-duplikasi teks dan anti-angka-karangan
- [ ] `b2b-phased-sale.md` menyatakan kriteria pilot ditandatangani **sebelum** fase 2
- [ ] Nol hex warna spesifik, nol nama font merek, nol nama klien
- [ ] No placeholder/TODO comments in new code

---

### Phase H: `pitch-review` menerima parameter rubrik (ubahan di `gaspol-pitch`)

**Estimated time:** 12 menit

Satu-satunya perubahan pada repo yang sudah dipublikasi. Perilaku default **wajib**
tidak berubah — itu dibuktikan oleh kontrak uji-diri `gaspol-pitch` yang sudah ada.

**Files:**
- Modify: `/Users/alisadikin/Drive-D/Projects/claude-plugin/gaspol-pitch/skills/pitch-review/SKILL.md`

**Steps:**

1. Write failing test for kemampuan `pitch-review` memuat rubrik dari path yang
   diberikan pemanggil. Uji ini **dijalankan agen**, bukan skrip: invoke
   `pitch-review` dengan argumen `rubric: <path>` yang menunjuk
   `gaspol-catalog/references/b2b/b2b-deck-rubric.md`. Expected error: skill
   mengabaikan argumen dan tetap memuat `../../references/investor-deck-rubric.md`,
   sehingga laporannya berisi dimensi VC (TAM, moat, the ask) untuk deck B2B.

2. Jalankan uji itu, konfirmasi gagal persis karena alasan tersebut. Catat kalimat
   di laporan yang membuktikannya.

3. Ubah bagian `## Load (the brain)` di `pitch-review/SKILL.md`: rubrik dimuat dari
   path yang diberikan pemanggil bila ada, dan **jatuh kembali ke default** bila
   tidak. Default tetap persis empat berkas yang sekarang:
   `../../references/investor-deck-rubric.md`, `../../references/vc-review-rubric.md`,
   `../../references/unit-economics.md`, `../../references/vc-fundamentals.md`.

   Tambahkan bagian `## Input` sebuah baris parameter opsional bernama `rubric`
   yang menerima satu atau lebih path, dan pernyataan eksplisit: tanpa `rubric`,
   perilaku identik dengan sebelum perubahan ini.

4. Jalankan ulang uji langkah 1, konfirmasi laporan sekarang memakai dimensi B2B.

5. **Uji regresi — ini gerbangnya.** Jalankan kontrak uji-diri `gaspol-pitch` yang
   sudah ada, tanpa parameter `rubric`:
   - `references/examples/bad-deck.md` **wajib** menghasilkan verdict BLOCKING,
     dengan tiap cacat disebut lapis penangkapnya (fluff dan headline label dan
     One-Message dan Data-Parsing di lapis (a); TAM top-down dan ask di lapis (c))
   - `references/examples/good-deck.md` **wajib** menghasilkan verdict PASS

6. Commit di repo `gaspol-pitch`: `feat(pitch-review): accept caller-supplied rubric paths, default unchanged`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] Agen-run: `pitch-review` tanpa `rubric` → `bad-deck.md` BLOCKING, `good-deck.md` PASS (regresi hijau)
- [ ] Agen-run: `pitch-review` dengan `rubric` menunjuk `b2b-deck-rubric.md` → laporan memakai cek B2B, nol dimensi VC
- [ ] `gaspol-pitch/skills/pitch-review/SKILL.md` menyatakan eksplisit bahwa tanpa `rubric` perilakunya identik
- [ ] Frontmatter `pitch-review/SKILL.md` tetap hanya `name` + `description` (aturan keras #6)
- [ ] Nol berkas lain di `gaspol-pitch` tersentuh
- [ ] No placeholder/TODO comments in new code

---

### Phase I: Skill `catalog-gate` — dua lapis pemblokir

**Estimated time:** 20 menit

**Files:**
- Create: `gaspol-catalog/skills/catalog-gate/SKILL.md`

**Steps:**

1. Write failing test for frontmatter dan keberadaan skill pertama.
   Expected error: `frontmatter: nol SKILL.md ditemukan`, `tests/frontmatter.sh`
   exit 1.

2. Jalankan `bash tests/frontmatter.sh`, konfirmasi gagal.

3. Tulis `catalog-gate/SKILL.md`. Frontmatter hanya `name` + `description`;
   description memuat frasa pemicu ("review deck penawaran", "cek katalog sebelum
   dikirim", "review render slide").

   Badan skill mendefinisikan **dua lapis, dua-duanya memblokir**:

   **Lapis teks** — panggil `pitch-review` dengan parameter `rubric` menunjuk
   `../../references/b2b/b2b-deck-rubric.md` dan
   `../../references/b2b/b2b-buyer-rubric.md`. Jalankan keenam lapis warisan
   berurutan: (a) lint biner 9 cek per slide → (b) skor berdimensi pembeli →
   (c) ambang → (d) konsistensi lintas-slide → (e) gerbang naratif → (f) subagen
   skeptis. Untuk (f), subagen diberi peran **manajer operasional yang skeptis**,
   bukan partner VC, dan diberi `research/buyer-brain.md` bagian `## Keberatan`
   sebagai bahan pertanyaan.

   Tambahkan lapis (h) pagar kejujuran dari `b2b-honesty-rails.md` dan lapis (i)
   keabsahan kit urgensi.

   **Lapis render** — fase **tersendiri**, dijalankan hanya bila PNG ada. Periksa
   tiap PNG terhadap `deck.md`-nya: teks terduplikasi, angka yang tidak ada di
   `deck.md`, kebocoran bahasa, kotak logo gagal, headline terhapus, kualifikasi
   terpotong, disclaimer hilang. Sumber pagar: `b2b-visual-contract.md`.

   Tulis alasannya di dalam skill supaya tidak dihapus orang lain: deck bisa **PASS
   di lapis teks dan BLOCKING di lapis render pada hari yang sama**, karena cacatnya
   lahir di prompt render, bukan di `deck.md`.

   Kontrak keluaran: `review.md` dengan verdict `PASS` atau `BLOCKING`. BLOCKING
   mengembalikan daftar perbaikan tepat ke `catalog-variant` atau `catalog-master`.
   **Dilarang melunakkan verdict** — itu membatalkan gunanya plugin.

   Tanpa PNG, lapis render dilaporkan `NOT RUN`, **bukan** PASS.

4. Jalankan `bash tests/frontmatter.sh`, konfirmasi `frontmatter OK — 1 skill`.

5. Commit: `feat: add catalog-gate with text and render blocking layers`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/frontmatter.sh` exit 0
- [ ] `catalog-gate/SKILL.md` menyebut keenam lapis warisan **dan** lapis (h) dan (i)
- [ ] Lapis render dinyatakan sebagai fase terpisah dengan alasannya tertulis
- [ ] Tanpa PNG, lapis render menghasilkan `NOT RUN`, bukan `PASS`
- [ ] Skill memuat larangan melunakkan verdict
- [ ] No placeholder/TODO comments in new code

---

### Phase J: Skill `catalog-master` — sekali per produk

**Estimated time:** 20 menit

**Files:**
- Create: `gaspol-catalog/skills/catalog-master/SKILL.md`

**Steps:**

1. Write failing test for penambahan skill kedua. Expected error:
   `frontmatter OK — 1 skill` padahal harus 2, jadi tambahkan dulu ke
   `tests/frontmatter.sh` sebuah pemeriksaan jumlah minimum. Ubah baris terakhir
   skrip jadi:

   ```bash
   [ "$found" -ge 3 ] || { echo "EXPECT >=3 skill, ada $found"; fail=1; }
   [ "$fail" -eq 0 ] && echo "frontmatter OK — $found skill"
   exit "$fail"
   ```

   Jalankan, expected error: `EXPECT >=3 skill, ada 1`.

2. Jalankan `bash tests/frontmatter.sh`, konfirmasi gagal dengan pesan itu.

3. Tulis `catalog-master/SKILL.md`. Reuse map yang wajib dipanggil:
   `positioning`, `pricing-strategy`, `monetizing-innovation`, `sales-narrative`,
   `crossing-the-chasm`, `100m-offers`.

   Baca: `b2b-deck-architecture.md`, `b2b-honesty-rails.md`, `enablement-kit.md`,
   `b2b-phased-sale.md`, dan `research/buyer-brain.md`.

   Keluaran ke `catalog/`:
   - katalog master kapabilitas, 12-16 slide, arsitektur 8 babak
   - menu modul à-la-carte + harga, **wajib punya baris yang tidak bisa dilepas**
   - kit enablement 5 bagian
   - one-pager satu halaman
   - skrip WhatsApp, telepon, dan tangani keberatan — keberatannya diambil dari
     `buyer-brain.md` bagian `## Keberatan`, bukan dikarang
   - spesifikasi kalkulator ROI (input: jumlah armada, rute, harga bahan bakar;
     keluaran: kondisi sekarang versus sesudah). **Spesifikasi saja** —
     implementasinya di luar lingkup plugin ini.

   **Gerbang input manusia (Iron Law).** Skill **wajib BERHENTI dan bertanya**,
   tidak boleh mengarang, untuk: daftar modul dan harganya; apakah produk mendukung
   banyak penyewa (multi-tenancy) atau tiap pelanggan berarti penggelaran terpisah;
   dan kontrak konten yang mengatur apa yang boleh dikatakan publik tentang
   pelanggan referensi. Tulis ketiganya sebagai gerbang eksplisit di dalam skill.

4. Jalankan `bash tests/frontmatter.sh`, konfirmasi `EXPECT >=3 skill, ada 2`.

5. Commit: `feat: add catalog-master with human-input gates for pricing and maturity claims`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `catalog-master/SKILL.md` memuat ketiga gerbang input manusia sebagai instruksi BERHENTI, bukan sebagai catatan
- [ ] Reuse map menyebut keenam skill terpasang dengan nama persisnya
- [ ] Kalkulator ROI dinyatakan sebagai **spesifikasi**, bukan implementasi
- [ ] `bash tests/guard-generic.sh` exit 0
- [ ] No placeholder/TODO comments in new code

---

### Phase K: Skill `catalog-variant` + router `gaspol-catalog`

**Estimated time:** 20 menit

**Files:**
- Create: `gaspol-catalog/skills/catalog-variant/SKILL.md`
- Create: `gaspol-catalog/skills/gaspol-catalog/SKILL.md`

**Steps:**

1. Write failing test for jumlah skill mencapai 4. Expected error:
   `EXPECT >=3 skill, ada 2`, `tests/frontmatter.sh` exit 1.

2. Jalankan `bash tests/frontmatter.sh`, konfirmasi gagal.

3. Tulis `catalog-variant/SKILL.md`. Reuse map: `spin-selling` (gali kebutuhan),
   `challenger-sale` (reframe dan ambil kendali), `meddic-scorecard` (nilai
   kelayakan deal), `made-to-stick`, `storybrand`.

   Alur:
   - **Gerbang murah di depan**: jalankan `meddic-scorecard` lebih dulu. Kalau deal
     dinilai tidak layak dikejar, laporkan alasannya dan **jangan** buat decknya.
     Membuat deck untuk deal mati adalah pemborosan yang paling sering terjadi.
   - Tambang temuan dari dokumen dan informasi prospek → slide ISSUES lahir dari
     temuan **prospek itu sendiri**, bukan template diisi. Kalau nol dokumen
     prospek tersedia, BERHENTI dan minta — deck tanpa temuan prospek melanggar
     arsitektur 8 babak.
   - Susun deck 10-16 slide mengikuti `b2b-deck-architecture.md`.
   - Pilih varian referensi pelanggan berdasarkan **wilayah prospek**: anonim bila
     prospek adalah pesaing langsung pelanggan referensi di pasar yang sama;
     bernama bila bukan. Aturan ini ditulis generik — pemetaan wilayah ke pelanggan
     adalah runtime input, tidak pernah di-commit.
   - Serahkan ke `catalog-gate`. **Dilarang** menyerahkan hasil ke pengguna sebelum
     verdict PASS.

4. Tulis `gaspol-catalog/SKILL.md` — orchestrator. Isi: pengumuman di awal;
   routing (`catalog-master` bila katalog induk belum ada, `catalog-variant` per
   prospek, `catalog-gate` sebelum apa pun dikirim); pernyataan bahwa
   `catalog-gate` **tidak pernah** boleh dilewati; daftar dependensi wajib beserta
   perintah pemasangannya bila hilang; dan daftar dependensi opsional yang boleh
   degrade anggun (`gaspol-design`, image MCP).

5. Jalankan `bash tests/frontmatter.sh`, konfirmasi `frontmatter OK — 4 skill`.

6. Commit: `feat: add catalog-variant with MEDDIC pre-gate and gaspol-catalog router`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/frontmatter.sh` exit 0 — `frontmatter OK — 4 skill`
- [ ] `catalog-variant` menjalankan `meddic-scorecard` **sebelum** menulis slide apa pun
- [ ] `catalog-variant` BERHENTI bila nol dokumen prospek tersedia
- [ ] Aturan varian referensi ditulis generik — nol nama pelanggan atau kota di `SKILL.md`
- [ ] Router menyatakan `catalog-gate` tidak pernah boleh dilewati
- [ ] `bash tests/guard-generic.sh` exit 0
- [ ] No placeholder/TODO comments in new code

---

### Phase L: Gerbang integrasi — kontrak fixture hijau

**Estimated time:** 20 menit

Ini gerbang sebenarnya. Plugin dinyatakan benar hanya kalau **dua-duanya** terpenuhi.

**Files:**
- Modify: `gaspol-catalog/skills/catalog-gate/SKILL.md` (tambah bagian uji-diri)
- Create: `gaspol-catalog/tests/CONTRACT.md`

**Steps:**

1. Write failing test for kontrak uji-diri end-to-end. Uji ini **dijalankan agen**:
   invoke `catalog-gate` pada `references/b2b/examples/bad-catalog.md`.
   Expected error: `catalog-gate` mengembalikan PASS atau tidak menyebut lapis
   penangkap tiap cacat — dua-duanya berarti kontrak belum terpenuhi.

2. Jalankan uji itu, catat verdict dan cacat mana yang lolos.

3. Tulis `tests/CONTRACT.md` — kontrak yang dibaca manusia dan agen, memetakan tiap
   cacat yang ditanam di `bad-catalog.md` ke lapis yang **wajib** menangkapnya:

   | Cacat di `bad-catalog.md` | Lapis penangkap |
   |---|---|
   | fluff B2B ("solusi end-to-end terintegrasi", "world-class") | (a) lint biner — Linguistic-Fluff |
   | headline label ("Harga", "Solusi") bukan asersi | (a) lint biner — Assertion-vs-Topic |
   | satu slide mencampur dua pesan lewat "dan" | (a) lint biner — One-Message |
   | harga muncul di cover | (b) arsitektur deck |
   | harga paket slide 7 tidak sama dengan total slide 9 | (d) konsistensi lintas-slide |
   | wajah manusia di slide | (g) kontrak visual |
   | klaim modul belum jadi disamakan dengan platform yang live | (h) pagar kejujuran |
   | tenggat yang tidak benar-benar berlaku | (i) keabsahan kit urgensi |

4. Tambahkan bagian `## Self-test` di `catalog-gate/SKILL.md` yang menyatakan
   kontrak ini verbatim, mengikuti pola `gaspol-pitch/skills/pitch-review/SKILL.md`:
   `bad-catalog.md` **WAJIB** BLOCKING dengan tiap cacat disebut lapis penangkapnya;
   `good-catalog.md` **WAJIB** PASS; skill benar hanya kalau keduanya terpenuhi.

5. Jalankan ulang uji langkah 1 sampai `bad-catalog.md` menghasilkan BLOCKING dengan
   kedelapan cacat terpetakan ke lapis yang benar. Kalau sebuah cacat tidak
   tertangkap, perbaiki **rubriknya** (Phase E/F/G), bukan fixture-nya.

6. Invoke `catalog-gate` pada `good-catalog.md`, konfirmasi PASS.

7. Jalankan `bash tests/run-all.sh`, konfirmasi `ALL GREEN`.

8. Commit: `test: add CAT-1 end-to-end fixture contract, gate green`

**Verification:**
- [ ] detect-stack: no stack markers for this project — verification is plan-declared only
- [ ] `bash tests/run-all.sh` exit 0 — `ALL GREEN`
- [ ] Agen-run: `bad-catalog.md` → BLOCKING, kedelapan cacat terpetakan ke lapis di `tests/CONTRACT.md`
- [ ] Agen-run: `good-catalog.md` → PASS
- [ ] Agen-run regresi: `gaspol-pitch` `bad-deck.md` → BLOCKING, `good-deck.md` → PASS (Phase H tidak merusak jalur investor)
- [ ] Nol cacat yang "tertangkap" dengan cara melonggarkan fixture
- [ ] No placeholder/TODO comments in new code

---

## Ringkasan fase

| Fase | Isi | Bergantung pada |
|---|---|---|
| A | Prasyarat skill — symlink + install | — |
| B | Riset pembeli (sub-proyek A) | — |
| C | Kerangka repo + harness uji | — |
| D | Fixture kontrak good/bad | C |
| E | Rubrik lint biner | C |
| F | Rubrik skor pembeli | B, C |
| G | Lima berkas referensi sisanya | C |
| H | `pitch-review` parameter rubrik | E |
| I | Skill `catalog-gate` | E, F, G, H |
| J | Skill `catalog-master` | B, G |
| K | Skill `catalog-variant` + router | A, G, I |
| L | Gerbang integrasi | semua |

**Bisa paralel:** A, B, dan C tidak saling bergantung. Begitu juga E dan G setelah C.
Sisanya berurutan.

## Di luar lingkup rencana ini

- Sub-proyek C — katalog INDUSIA AI Cargo jadi. Rencana sendiri setelah Phase L hijau.
- Implementasi dasbor demo dan kalkulator ROI (UI nyata, memicu fase desain sendiri).
- Video promo.
- Publikasi `gaspol-catalog` ke marketplace publik.
- Multi-tenancy produk.

## Pertanyaan terbuka yang dibawa dari spec

1. Daftar modul dan harga — dibutuhkan sub-proyek C, **tidak** memblokir rencana
   ini. `catalog-master` berhenti dan bertanya (Phase J).
2. Izin tertulis pelanggan referensi untuk penyebutan bernama — dibutuhkan sebelum
   deck varian dikirim, **tidak** memblokir rencana ini.
