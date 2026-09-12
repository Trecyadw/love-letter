# loveletter

Aplikasi chat realtime untuk dua orang, dibangun sebagai satu file HTML tanpa framework dan tanpa proses build. Tampilannya mengambil bentuk jendela desktop lama dengan sprite pixel art yang digambar sendiri.

## Fitur

- **Chat realtime** lewat Firebase Realtime Database, dengan listener `onChildAdded` sehingga pesan masuk tanpa polling.
- **Status pengiriman tiga tingkat** — `belum sampai`, `sampai`, `dibaca` — dihitung dari kombinasi presence, timestamp terakhir online, dan penanda baca lawan bicara.
- **Indikator kehadiran** memakai `onDisconnect`, jadi status berubah otomatis saat koneksi putus atau perangkat dimatikan.
- **Notifikasi tanpa izin browser**: ikon widget menampilkan badge pixel dan judul tab ikut berubah saat ada pesan belum dibaca.
- **Mode kiss** — layar berubah jadi taman, tiap ketukan memicu animasi dan dihitung, lalu dikirim sebagai satu pesan rangkuman supaya chat tidak banjir.
- **Latar mengikuti waktu setempat**: pagi, siang, sore, dan malam punya langit, awan, serta objek langit sendiri. Malam hari karakter pindah ke ayunan dan berubah jadi siluet saat berciuman.
- **Musik ambient** dibangkitkan langsung dengan Web Audio API (progresi F–C–Dm–B♭), bukan file audio, sehingga tidak menambah aset.
- **Autentikasi email/password**, dengan pemetaan email ke karakter, dan security rules yang membatasi akses hanya pada dua alamat email tertentu.

## Teknologi

Vanilla JavaScript (ES modules), CSS murni untuk seluruh animasi dan sprite layout, Firebase Authentication, Firebase Realtime Database, Web Audio API. Di-hosting di GitHub Pages.

## Catatan teknis

Seluruh sprite disimpan sebagai data URI di dalam file, jadi tidak ada request gambar terpisah dan aplikasi tetap berupa satu berkas. Semua tampilan memakai `image-rendering: pixelated` dengan sprite beresolusi asli agar tepiannya tetap tajam saat diperbesar.

Konfigurasi Firebase memang terlihat di kode sumber — itu wajar untuk aplikasi web Firebase, karena kunci publik bukan kredensial. Pengamanan sesungguhnya ada di security rules:

```json
{
  "rules": {
    "rooms": {
      "$room": {
        ".read": "auth.token.email === 'a@contoh.com' || auth.token.email === 'b@contoh.com'",
        ".write": "auth.token.email === 'a@contoh.com' || auth.token.email === 'b@contoh.com'"
      }
    }
  }
}
```

Siapa pun boleh menyalin repositori ini, tetapi database menolak permintaan dari akun di luar dua email tersebut.

## Menjalankan sendiri

1. Buat project Firebase, aktifkan Realtime Database dan Email/Password pada Authentication.
2. Salin `firebaseConfig` ke konstanta `CONFIG` di `index.html`.
3. Isi `ROOM` dengan string acak dan `EMAILS` dengan alamat email kedua pengguna.
4. Pasang security rules seperti contoh di atas.
5. Hosting berkasnya di GitHub Pages atau layanan statis lain. Firebase menolak protokol `file://`, jadi aplikasi harus diakses lewat `http://` atau `https://`.

## Struktur data

```
rooms/<room-id>
  messages/<push-id>   { who, text, t, s }
  here/<karakter>      boolean
  lastOn/<karakter>    timestamp
  seen/<karakter>      timestamp
```
