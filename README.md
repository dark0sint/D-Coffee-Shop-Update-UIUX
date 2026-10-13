# D Coffee Shop — paket lengkap

Isi: `server/` (Node.js + Express + PostgreSQL), `server/public/` (aplikasi pelanggan PWA `index.html`, panel kasir `staff.html`),
`docker-compose.yml` (app + PostgreSQL + Caddy HTTPS otomatis).

## 1. Deploy ke server (VPS Ubuntu + Docker)
1. Arahkan domain (A record) ke IP VPS. Install Docker: `curl -fsSL https://get.docker.com | sh`
2. Upload folder ini, lalu: `cp .env.example .env` dan isi semua nilai (DOMAIN, DB_PASSWORD, JWT_SECRET, STAFF_PHONE, STAFF_PASSWORD).
3. `docker compose up -d --build`
4. Buka `https://DOMAIN` (pelanggan) dan `https://DOMAIN/staff` (kasir/barista), dan `https://DOMAIN/admin` (pemilik: kelola menu, harga, laporan 7 hari; login pakai ADMIN_PHONE/ADMIN_PASSWORD).
Cek log: `docker compose logs -f app`. Backup DB: `docker compose exec db pg_dump -U dcoffee dcoffee > backup.sql`

## 2. Pembayaran
- `PAYMENT_MODE=demo`: top-up & e-wallet disimulasikan (uang tidak nyata). Hanya untuk uji coba.
- Produksi: `PAYMENT_MODE=live`, isi `MIDTRANS_SERVER_KEY` (+ `MIDTRANS_PROD=1`), lalu di dashboard Midtrans set
  Payment Notification URL ke `https://DOMAIN/api/payments/midtrans`. Tanpa Midtrans, hanya pembayaran Saldo D Coffee yang aktif.

## 3. Play Store (Trusted Web Activity)
1. `npm i -g @bubblewrap/cli` (butuh JDK 17; Bubblewrap menawarkan install otomatis).
2. `bubblewrap init --manifest=https://DOMAIN/manifest.json` → `bubblewrap build` (menghasilkan `app-release-bundle.aab`).
3. Ambil SHA-256 fingerprint keystore (`bubblewrap fingerprint` / `keytool -list -v`), isi `server/public/.well-known/assetlinks.json`:
   `[{"relation":["delegate_permission/common.handle_all_urls"],"target":{"namespace":"android_app","package_name":"PACKAGE_ANDA","sha256_cert_fingerprints":["SHA256_ANDA"]}}]`
   lalu `docker compose up -d --build`. Jika Play App Signing aktif, tambahkan juga fingerprint dari Play Console.
4. Daftar Google Play Console (biaya sekali bayar ±US$25), upload `.aab`, isi listing & kebijakan privasi.
   Akun personal baru biasanya wajib closed testing dulu — cek syarat terbaru Google.

## 4. Desktop
Buka `https://DOMAIN` di Chrome/Edge → ikon Install di address bar.

## Keterbatasan versi ini
Belum ada: reset password, notifikasi push, refund/batal pesanan,
pembatalan otomatis pesanan gateway yang tidak dibayar. Kode belum diuji dengan database & gateway sungguhan.

## Kredit
Aplikasi pelanggan menampilkan "Dibuat oleh Shadow Security Indonesia · @shadowsecindonesia" (tautan Instagram) di footer semua halaman dan layar login. Ubah di konstanta `CREDIT` pada `server/public/index.html`.
<img width="780" height="1688" alt="D-Coffee-06-gerai-terdekat" src="https://github.com/user-attachments/assets/a5d011d3-c134-4a1a-b5fd-c52d79a6efeb" />
<img width="780" height="1688" alt="D-Coffee-05-dompet-loyalitas" src="https://github.com/user-attachments/assets/3e26352d-df70-4068-baf7-a7cf83b37938" />
<img width="2560" height="1582" alt="D-Coffee-04-tampilan-desktop" src="https://github.com/user-attachments/assets/5d37a6f6-8c75-4a5e-a09c-996f2ce2398e" />
<img width="780" height="1688" alt="D-Coffee-03-lacak-pesanan" src="https://github.com/user-attachments/assets/651f0e92-467d-4de3-8021-466635cd8f97" />
<img width="780" height="1688" alt="D-Coffee-02-kustomisasi-menu" src="https://github.com/user-attachments/assets/43355187-4fd7-4f24-9be2-206bf71f6609" />
<img width="780" height="1688" alt="D-Coffee-01-beranda" src="https://github.com/user-attachments/assets/a2b1ef11-671a-49e7-80fa-019d9ce14290" />
<img width="780" height="1688" alt="D-Coffee-07-masuk-daftar" src="https://github.com/user-attachments/assets/a2e4d9c3-8e14-410f-8b69-e0105b5e906d" />
