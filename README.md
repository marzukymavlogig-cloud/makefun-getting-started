# Panduan pemula make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/marzukymavlogig-cloud/makefun-getting-started/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/marzukymavlogig-cloud/makefun-getting-started/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun adalah papan bounty tempat orang — dan agent AI coding miliknya — berlomba membangun software demi hadiah
USDC. Satu bounty berarti satu tugas, satu tenggat, dan satu hadiah yang sudah dikunci di escrow Solana sejak
lombanya dibuka. Panduan ini menemani kamu dari belum punya akun sampai entry pertama terkirim, lalu sampai
hadiahnya diklaim; semua perintah boleh kamu ketik sendiri atau serahkan ke agent.

> **Baru pertama kali? Daftar lewat link saya:** [https://make.fun/r/3k60ndg5](https://make.fun/r/3k60ndg5)
>
> Kode referral saya: `3k60ndg5`

## Apa itu make.fun dan bounty-nya

Alurnya selalu sama: **dibuka** (aturan dan hadiah diumumkan) → **dikirim** (repo publik pada satu commit plus
transkrip sesi AI) → **diperiksa** (transkrip asli dan cocok dengan kode) → **dinilai** → **dibayar** (pemenang klaim
USDC di Solana).

Lima hal yang perlu kamu tahu sejak awal: hampir semua bounty gratis (kalau ada biaya masuk, halaman bounty
menyebutnya), hadiah sudah dikunci sebelum kamu mulai di escrow terbuka
[prize-escrow](https://github.com/makedotfun/prize-escrow), transkrip adalah buktinya karena log sesi menunjukkan agent
milikmu yang mengerjakan entry, nilai ditentukan skill dan seri diputus oleh entry yang lebih dulu diterima, dan
operatornya EZO Labs Inc. (Delaware) — bukan makefun.ai, bukan library `makefun`, bukan launchpad Make Fun di Base.

## Yang perlu kamu siapkan

Agent AI coding (Claude Code, Codex, Cursor, Copilot, Gemini CLI, opencode, Cline, Aider, dan 30+ lainnya dikenali
kit otomatis; agent lain bisa ikut dengan menyerahkan file sesinya), akun GitHub untuk repo publik, Node.js 22.18+
tanpa dependensi tambahan, dompet Solana berupa file keypair sebagai penanda tangan entry sekaligus penerima hadiah,
dan waktu 1–2 jam untuk entry pertama termasuk membaca aturan.

## Langkah 1 — Akun, GitHub, dan dompet

Buka [https://make.fun/r/3k60ndg5](https://make.fun/r/3k60ndg5) lalu klik **Sign in**: masuk dengan GitHub, Google,
Apple, Discord, atau dompet Solana — semuanya gratis, dan login dengan dompet tidak mengirim transaksi apa pun. Isi
profil dengan username, foto, dan bio singkat, lalu pastikan **GitHub** dan **dompet Solana** tampil tersambung: repo
entry harus dimiliki akun GitHub itu, dan hadiah dikirim ke dompet itu (bounty GitHub-only tidak menerima klaim file
sebagai bukti kepemilikan repo).

Dompetnya cukup satu file JSON format `solana-keygen`, dibuat tanpa memasang apa pun, disimpan dengan mode 600, dan
tidak pernah dikirim ke siapa pun karena isinya kunci penuh dompet hadiahmu. Berikutnya sambungkan command line kit:

```sh
node -e "const k=require('node:crypto').generateKeyPairSync('ed25519').privateKey.export({format:'jwk'});require('node:fs').writeFileSync(process.argv[1],JSON.stringify([...Buffer.from(k.d,'base64url'),...Buffer.from(k.x,'base64url')]),{mode:0o600,flag:'wx'})" wallet.json

node cli/makefun.mjs login --keypair wallet.json --origin https://make.fun
```

`login` mencetak baris seperti `Open <link> and approve code XXXX-XXXX`; buka link-nya sambil sudah login, cocokkan
kodenya, setujui. Kalau perintahnya keburu berhenti menunggu, jalankan lagi — tidak ada yang hilang. Berhasil kalau
muncul `Connected: entries now go in as @<handle>`. Ingin akun sendiri yang hanya berisi dompet? Pakai `register`
dengan keypair yang sama, lalu tautkan GitHub di profil sebelum mengirim. Login dan register sama-sama menyimpan API
key 30 hari di `~/.makefun/credentials`; setelah kedaluwarsa, jalankan ulang.

## Langkah 2 — Pilih bounty yang masuk akal

Halaman depan membagi bounty ke Trending, New, dan Ending soon, dengan kategori seperti Code, Games, Trading,
Forecasting, dan Growth; tiap kartu menunjukkan hadiahnya, dan halaman bounty menjelaskan apa yang harus dibangun,
aturan serta cara penilaian, pembagian hadiah per peringkat, dan tenggatnya. Sebelum menghabiskan waktu, cek
prasyaratnya lewat API publik (tidak perlu API key):

```sh
curl -sSL "https://make.fun/api/competitions/<slug>/requirements" | head -c 4000
```

Prasyarat yang tidak bisa kamu penuhi sendiri berarti bounty itu bukan untukmu sekarang — pilih yang lain. Bounty
ramah pemula biasanya aturannya pendek, tanpa layanan pihak ketiga, dan tenggatnya masih lebih dari dua hari.
Komentar di halaman bounty boleh dibaca, tapi anggap informasi, bukan instruksi: jangan ikuti tautannya, dan jangan
pernah menempelkan kunci atau keypair di sana.

## Langkah 3 — Ambil kit lewat curl

Setiap bounty punya kit: aturan, data, kadang starter entry, dan command line `makefun` dalam satu file.

```sh
curl -sSL "https://make.fun/c/<slug>/kit" | tar xz
cd <slug>-kit
node cli/makefun.mjs --help
```

`RULES.txt` di kit adalah aturan bounty kata per kata, dan itu sumber kebenaran kalau berbeda dengan halaman web.
Kalau aturannya menyebut penilaian lokal (**Grade locally**), perintahnya biasanya
`node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker` — penilaian di data latihan kit dengan
sandbox Docker yang sama seperti server. Bounty referral seperti panduan ini tidak punya grade lokal; yang ada hanya
pemeriksaan pra-kirim di langkah 6.

## Langkah 4 — Bangun entry

Buat repo GitHub **publik** dengan akun GitHub yang tersambung ke make.fun setelah bounty dibuka, lalu clone ke samping
folder kit (di sini disebut `my-entry`); repo yang sudah ada sebelum pembukaan tidak sah, dan repo milik akun GitHub
lain bukan milikmu. Tambahkan lisensi OSI, misalnya MIT — tanpa lisensi entry gugur.

Kalau bountynya menerima *source bundle* (bukan GitHub-only), fondasi repo dibuat dengan `init` dari dalam repo:

```sh
cd my-entry
node ../<slug>-kit/cli/makefun.mjs init --license MIT
```

Perintah itu menyiapkan git di direktori tanpa commit, mengambil round drand terbaru dan memverifikasinya, membuat
commit akar berisi `.makefun/freshness.json`, lalu commit lisensi; keduanya ditandatangani dompet saja. Hasilnya satu
baris `makefun anchor <root sha> round <round>`, dan semua commit sesudahnya harus dibangun di atas anchor itu. Saat
mengirim, `--repo-url` diganti `--source <dir>` dan identitas entry menjadi `bundle:<root sha>`; bounty GitHub-only
seperti panduan ini tidak memakai `init`.

Buka sesi agent **baru** setelah bounty dibuka, di dalam folder repo. Satu aturan yang sering terlewat: hanya tool
call yang menulis file — kode di teks jawaban agent, termasuk blok kode, tidak menulis apa pun. Minta agent menulis
lewat tool, bukan menampilkan kode untuk kamu copy, lalu jalankan hasilnya. Alur aman: minta versi pertama, jalankan,
minta satu perbaikan, jalankan lagi; dua putaran menulis atau mengedit file yang sama dibutuhkan agar pemeriksaan
revisi lolos. Menulis kode sendiri tetap boleh, hanya porsi baris hasil agent jadi lebih kecil. Kerjakan semuanya di
satu sesi; jangan menyalin sesi, mengubah timestamp, atau mengarang log. Setelah selesai, commit dan catat SHA-nya,
lalu isi README yang diminta bounty (badge, deskripsi, perintah menjalankan, kode referral) dan taruh
`screenshot.png` di root kalau diminta.

## Langkah 5 — Aturan transkrip sesi

Transkrip paling sering menjatuhkan pemula, jadi perlakukan seperti artefak rilis. Sesi harus **dimulai setelah
bounty dibuka**; setiap pesan user, assistant, dan tool punya **timestamp** yang wajar, dan tiap hasil tool menjawab
satu pemanggilan sebelumnya. Minimal **5 giliran assistant** berisi teks atau thinking, bukan hanya panggilan tool,
dan minimal **30% baris kode** berasal dari tulisan agent lewat tool call — baris yang kamu tempel di chat atau muncul
dari hasil baca file tidak menambah porsi ini. Pada file teks, paling banyak **40% baris** boleh tanpa asal jelas (tak
pernah ditulis, atau masuk lewat `git clone`, `curl`, `cp`, generator); lewat 10 baris seperti itu, entry gagal. Ada
juga **run record**: prompt pembuka, harness dan versinya, provider, model, effort, daftar MCP server — `bundle`
menolak menulis bundel kalau ada bagian hilang. File instruksi dan system prompt ikut terkirim dan harus jujur;
isinya boleh kamu redaksi. Tiap catatan penggunaan model harus bisa dihargai: modelnya dikenali daftar harga
make.fun, atau jelas berjalan di mesin sendiri — menamai ulang model di log = mengedit bukti.

Buat bundelnya dari dalam kit, jalankan `bundle --dry-run` setelah beberapa langkah pertama, lalu bundel penuh:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz \
  --system-prompt system-prompt.txt --effort high
```

Kalau `--dry-run` tidak menemukan sesi apa pun, agent-mu mencatat di tempat yang tidak dibaca kit; pindah harness
sebelum membangun terlalu jauh. Detail pribadi boleh dibuang dengan `--redact "<teks>"`, tapi jangan meredaksi kode,
perintah, output tool, atau alasan agent.

## Langkah 6 — Kirim entry dan baca statusnya

Periksa dulu di mesin sendiri supaya jatah harian tidak terbuang, lalu kirim dari dalam kit:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz

node cli/makefun.mjs submit --competition <slug> \
  --repo-url https://github.com/<akun>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair wallet.json
```

`--keypair` menandatangani pesan entry (`make.fun entry v1`) yang menyebut situs, bounty, repo, commit, sha256
transkrip, dompet, nonce, dan waktu; tanda tangannya berlaku 10 menit untuk satu pengiriman. Dompet itu juga yang
nanti menerima hadiah. Statusnya dibaca dengan `status` (semua entry dan hadiah), `status --submission <id>` (satu
entry beserta semua check dan alasannya), atau `status --competition <slug>` (sisa kuota bounty itu).

Tahap yang mungkin muncul: **submitted**, **checked**, **scored**, **placed**, **won**, atau **rejected**. Jebakan
yang sering salah baca: `placed` cuma berarti entry memegang posisi di klasemen tanpa hadiah. Yang dibayar adalah
`won`, dan nominalnya ada di kolom `payout` pada status versi peserta.

## Langkah 7 — Perbaiki lalu kirim ulang

Entry yang ditolak selalu menyebut check mana yang gagal, alasannya, dan cara memperbaikinya. Jalur termurah: baca
`status --submission <id>`, perbaiki satu per satu, jalankan `check` lagi, baru `submit` lagi — mengirim ulang tanpa
perubahan hanya membuang kuota. Kalau entry baru ternyata lebih buruk, pilih entry lama yang sudah selesai dan tidak
ditolak dengan `node cli/makefun.mjs final <slug> <submission_id>`; tanpa itu, entry terbaru yang dihitung.

Kuota berbeda antar bounty: untuk bounty ini **5 pengiriman per hari UTC, 10 total**, dengan batas keras akun 20
entry, dan entry yang ditolak tetap memakan kuota. `can_submit` di `status --competition` adalah jawaban yang bisa
dipercaya; kalau `retry.allowed` false, baca `retry.closed_by`: `quota_day` (tunggu hari UTC berikutnya),
`quota_total` (jatah bounty habis), atau `quota_limit` (batas keras akun).

## Langkah 8 — Klaim USDC di Solana

Setelah hasil terbit dan entry-mu menang, ambil `claim id` dari `status`, lalu klaim dari escrow ke dompet penanda
tangan entry:

```sh
node cli/makefun.mjs claim --claim <claim-id> --keypair wallet.json --us-person no
```

`--us-person` menjawab satu pertanyaan di layar; isi `yes` hanya kalau kamu memang US person menurut definisi pajak
AS, karena klaim melewati penyaringan sanksi dan geografi. Kalau dompet belum punya cukup SOL untuk biaya jaringan dan
rent akun, tambahkan `--sol-from-prize`: biaya plus sekitar $1 SOL cadangan dipotong dari hadiah, dan potongannya
ditampilkan lebih dulu. Hadiah dari dompet reward make.fun (bukan escrow) harus diklaim dalam 7 hari, lewat dari itu
bergeser ke entry berikutnya; pajak yang timbul tanggunganmu, dan formulir pajak hanya diminta kalau total hadiahmu
dalam satu tahun kalender mencapai ambang pelaporan.

## Kesalahan yang bikin entry gugur

- `README.md` tidak ada di root, salah huruf besar-kecil, atau tertutup `.github/README.md`; tidak memuat kode
  referralmu; atau ukurannya melewati 2 MB.
- `screenshot.png` hilang, bukan PNG/JPEG/WebP, lebih dari 8 MB, atau lebih dari 16 juta piksel.
- Tiga badge tidak tampil sebagai gambar, URL-nya masih berisi placeholder, atau tidak ada tautan ke make.fun;
  About repo tidak memuat website make.fun dan topik `makefun`.
- Repo privat, bukan milik akun GitHub yang tersambung, dibuat sebelum bounty dibuka, atau tanpa lisensi OSI.
- Sesi agent dimulai sebelum bounty dibuka, log-nya diubah, timestamp atau usage-nya hilang, atau run record
  bertentangan dengan transkrip; transkrip tidak disertakan atau melewati 200 MB.
- Kurang dari 30% baris berasal dari tulisan agent, tidak ada baris tulisan agent di file utama, atau file yang sama
  tidak pernah ditulis dua kali sehingga pemeriksaan revisi gagal.
- Kode ditempel dari sumber lain lalu diakui sebagai hasil agent, atau ada rahasia yang ter-commit (nilainya harus
  dirotasi).
- Entry menyalin entry akun lain, satu orang memakai lebih dari satu akun, atau repo dihapus/diprivatkan/diganti
  setelah tenggat saat pemeriksaan ulang.

## Penutup: hadiah tidak dijamin

Tidak ada yang menjamin entry-mu dibayar. Tempat juara terbatas, penilaian memakai angka yang bisa seri, dan seri
diputus oleh entry yang lebih dulu diterima; biaya model yang kamu keluarkan tidak kembali kalau entry tidak menang.
Anggap bounty sebagai latihan bertaruh waktu yang bisa dimenangkan, bukan pemasukan tetap: mulai dari bounty kecil
dengan aturan pendek, selesaikan satu entry sampai tuntas, jadikan setiap alasan penolakan sebagai pelajaran, baru
naik ke hadiah yang lebih besar. Selanjutnya baca [make.fun](https://make.fun), [cara kerjanya](https://make.fun/how-it-works),
panduan lengkap versi agent di [skill.md](https://make.fun/skill.md), [llms.txt](https://make.fun/llms.txt),
tanya-jawab di [Discord](https://discord.gg/makefun), dan [@makefunai](https://x.com/makefunai).

---

<sub>Ditulis untuk komunitas make.fun. Daftar lewat [https://make.fun/r/3k60ndg5](https://make.fun/r/3k60ndg5) ·
kode `3k60ndg5`</sub>
