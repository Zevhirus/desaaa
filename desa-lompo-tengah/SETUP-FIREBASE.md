# Panduan Setup Firebase & Deploy — Website Desa Lompo Tengah

Panduan ini untuk mengaktifkan fitur **admin update berita/pengumuman** dan
mem-publish situs supaya bisa diakses online, memakai Firebase (gratis untuk
skala desa).

---

## 1. Buat Project Firebase

1. Buka https://console.firebase.google.com → **Add project**.
2. Beri nama, misalnya `desa-lompo-tengah`. Google Analytics boleh dimatikan.
3. Setelah project dibuat, klik ikon **`</>`** (Add app → Web) untuk
   mendaftarkan situs ini. Beri nickname bebas, tidak perlu centang Firebase
   Hosting dulu (akan diaktifkan di langkah 5).
4. Firebase akan menampilkan blok `firebaseConfig` seperti ini:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "desa-lompo-tengah.firebaseapp.com",
     projectId: "desa-lompo-tengah",
     storageBucket: "desa-lompo-tengah.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcxyz"
   };
   ```
5. **Salin blok ini**, lalu tempel menggantikan `firebaseConfig` di **dua
   file**: `index.html` (dekat bagian bawah, cari komentar
   `FIREBASE — PENGUMUMAN DINAMIS`) dan `admin.html` (dekat bagian bawah).

---

## 2. Aktifkan Firestore Database

1. Di sidebar Firebase Console: **Build → Firestore Database → Create database**.
2. Pilih **Production mode**, lokasi server terdekat (misalnya `asia-southeast2 (Jakarta)`).
3. Setelah aktif, buka tab **Rules** dan ganti dengan aturan berikut:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /pengumuman/{docId} {
         allow read: if true;              // semua orang boleh membaca
         allow write: if request.auth != null;  // hanya admin login yang boleh ubah
       }
     }
   }
   ```
4. Klik **Publish**.

Koleksi `pengumuman` akan otomatis terbentuk begitu Anda menambah berita
pertama lewat halaman admin.

---

## 3. Aktifkan Login Admin

1. Di sidebar: **Build → Authentication → Get started**.
2. Pilih provider **Email/Password** → aktifkan → Save.
3. Buka tab **Users → Add user**. Isi email & kata sandi admin
   (misalnya `admin@lompotengah.desa.id`). Ini akun yang dipakai login di
   `admin.html`.
4. Bisa tambah lebih dari satu akun admin bila perlu.

---

## 4. Coba Jalankan

1. Buka `admin.html` di browser, login dengan akun yang dibuat di langkah 3.
2. Tambahkan satu pengumuman contoh → Simpan.
3. Buka `index.html` → bagian **Pengumuman** akan otomatis menampilkan data
   dari Firestore (menggantikan 3 contoh statis).

Jika bagian Pengumuman masih menampilkan data statis lama, cek Console
browser (F12) untuk pesan error — biasanya karena `firebaseConfig` belum
ditempel di kedua file, atau Rules belum di-publish.

---

## 5. Deploy Supaya Online (Firebase Hosting)

Karena backend sudah pakai Firebase, cara termudah adalah hosting di Firebase
juga (gratis, otomatis dapat HTTPS).

1. Install Node.js (jika belum ada): https://nodejs.org
2. Buka terminal di folder situs ini, lalu jalankan:
   ```
   npm install -g firebase-tools
   firebase login
   firebase init hosting
   ```
   - Pilih **Use an existing project** → pilih project yang dibuat di langkah 1.
   - Public directory: ketik `.` (folder saat ini, karena `index.html` ada di sini).
   - Configure as single-page app: **No**.
   - Jangan timpa `index.html` yang sudah ada bila ditanya.
3. Deploy:
   ```
   firebase deploy
   ```
4. Firebase akan memberi URL publik, contoh:
   `https://desa-lompo-tengah.web.app`

**Alternatif lain** (tanpa Firebase Hosting): drag-and-drop seluruh folder ini
ke https://app.netlify.com/drop, atau upload ke hosting cPanel/Hostinger biasa
via File Manager — situs ini murni file statis (HTML/CSS/JS) jadi kompatibel
di hosting mana pun. Fitur admin tetap jalan karena datanya tersimpan di
Firebase, bukan di server hosting.

---

## 6. Yang Masih Perlu Diisi Manual

- **Google Form pengaduan**: buat form di https://forms.google.com, salin
  link-nya, tempel ke `href="#"` pada tombol *"Isi Formulir Pengaduan"* di
  `index.html` (bagian Pengaduan Masyarakat).
- **Google Maps**: buka Google Maps → cari lokasi Kantor Desa → Share →
  Embed a map → salin kode `<iframe>` → tempel menggantikan kotak placeholder
  peta di bagian Kontak dan Profil Desa.
- **Foto kegiatan asli**: ganti kartu foto berwarna (placeholder) di bagian
  Beranda dan Galeri dengan tag `<img src="assets/nama-foto.jpg">` berisi foto
  sungguhan.
- **Data angka** (jumlah penduduk, KK, UMKM, dll) di bagian Data Desa.
