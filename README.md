# A Little Crush Adventure 🎀

Website interaktif untuk mengajak gebetan pacaran. Dibangun dengan Vue 3 + Vite, mobile-first, pink coquette, dan navigasi berbasis klik (bukan slide).

## Menjalankan di Windows

1. Ekstrak ZIP ini.
2. Buka folder `a-little-crush-adventure` di VS Code.
3. Buka terminal VS Code (**Terminal → New Terminal**).
4. Pastikan terminal berada di folder yang memiliki `package.json`.
5. Jalankan:

```powershell
npm install
npm run dev
```

6. Buka alamat lokal yang muncul di terminal, biasanya `http://localhost:5173`.

Untuk mengecek build produksi:

```powershell
npm run build
npm run preview
```

## Personalisasi

Edit `src/config.js`. Ganti:
- `senderName`: nama pengirim.
- `recipientName`: nama gebetan.
- `quizOptions`: pilihan dan respons mini kuis.
- `secretCards`: tiga pesan rahasia.
- `plotTwistMessage`, `question`, `yesMessage`, dan `thinkMessage`.
- `photoPath`: lokasi foto pribadi.
- `musicPath`: lokasi file musik.

### Menambahkan foto

Buat folder `public/images/` lalu letakkan foto di dalamnya. Secara default website mencari:

`public/images/our-photo.jpg`

Jika nama file berbeda, ubah `photoPath` di `src/config.js`. Jika foto tidak tersedia, area foto otomatis diganti ilustrasi.

### Menambahkan musik

Buat folder `public/music/` lalu letakkan file instrumental yang kamu miliki hak untuk digunakan, misalnya:

`public/music/dreamy-instrumental.mp3`

Jika nama file berbeda, ubah `musicPath` di `src/config.js`.

Browser dapat memblokir autoplay bersuara. Musik mulai dicoba setelah tombol pembuka diklik, dan tombol di kanan atas bisa memutar/jeda musik. Musik akan mengulang otomatis selama halaman masih terbuka.

## Catatan

- Tidak memerlukan backend atau database.
- Pilihan dan pesan disimpan di sisi browser; website ini tidak mengirim jawaban ke mana pun.
- Jika ingin membagikan website lewat internet, deploy folder project ini ke Vercel atau Netlify setelah `npm run build`.
