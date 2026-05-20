# ⛏ OXIN Mining - Panduan Setup & Deploy

## 📋 Fitur
- ✅ Login / Daftar dengan Email & Password
- ✅ Login dengan Google
- ✅ Mining harian (1x per 24 jam = +10 Poin)
- ✅ Countdown timer mining
- ✅ Wallet dengan saldo poin
- ✅ Withdraw ke Bank / DANA / OVO / GoPay
- ✅ Program Referral (bonus 50 poin)
- ✅ Riwayat withdraw
- ✅ Data tersimpan di Firebase (gratis)

---

## 🚀 LANGKAH 1: Setup Firebase (Gratis)

### Buat Project Firebase
1. Buka https://console.firebase.google.com
2. Klik **"Tambahkan Project"**
3. Beri nama project (contoh: `oxin-mining`)
4. Klik **Buat Project**

### Aktifkan Authentication
1. Di sidebar, klik **Authentication** → **Get Started**
2. Tab **Sign-in method** → aktifkan:
   - ✅ **Email/Password**
   - ✅ **Google**

### Buat Firestore Database
1. Di sidebar, klik **Firestore Database** → **Create Database**
2. Pilih **Start in test mode** (kita set rules nanti)
3. Pilih region → Klik **Enable**

### Setup Firestore Rules (Penting!)
Di Firestore → tab **Rules**, paste ini:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
      allow read: if request.auth != null;
    }
    match /withdrawals/{docId} {
      allow read, write: if request.auth != null 
        && (resource == null || resource.data.uid == request.auth.uid)
        && request.auth.uid == request.resource.data.uid;
      allow read: if request.auth != null 
        && resource.data.uid == request.auth.uid;
    }
    match /mining_logs/{docId} {
      allow create: if request.auth != null 
        && request.resource.data.uid == request.auth.uid;
      allow read: if request.auth != null;
    }
  }
}
```

### Dapatkan Config Firebase
1. Di Firebase → klik ikon ⚙️ **Project Settings**
2. Scroll bawah → **Your apps** → klik ikon **`</>`** (Web)
3. Daftarkan app (isi nama apa saja)
4. Copy konfigurasi yang muncul:

```javascript
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "oxin-mining.firebaseapp.com",
  projectId: "oxin-mining",
  storageBucket: "oxin-mining.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

5. **Paste config tersebut ke dalam `index.html`** (cari baris `GANTI_API_KEY` dst.)

---

## 🚀 LANGKAH 2: Upload ke GitHub

### Buat Repository GitHub
1. Buka https://github.com → **New repository**
2. Nama repo: `oxin-mining`
3. Pilih **Public** → klik **Create repository**

### Upload File
```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/USERNAME/oxin-mining.git
git push -u origin main
```

---

## 🚀 LANGKAH 3: Deploy Gratis di Vercel

1. Buka https://vercel.com → **Sign Up** dengan GitHub
2. Klik **New Project** → pilih repo `oxin-mining`
3. Klik **Deploy**
4. Website kamu live! Contoh: `https://oxin-mining.vercel.app`

---

## 🚀 LANGKAH 4: Tambahkan Domain ke Firebase

Setelah dapat URL dari Vercel:
1. Firebase → **Authentication** → **Settings** → **Authorized domains**
2. Klik **Add domain** → masukkan domain Vercel kamu
3. Ini penting agar Google Login bisa jalan!

---

## 💼 Cara Kelola Withdraw (Admin)

Semua request withdraw tersimpan di **Firestore → collection `withdrawals`**

Kamu bisa:
1. Buka Firebase Console → Firestore → `withdrawals`
2. Lihat semua pengajuan user
3. Setelah transfer manual ke rekening user, update field `status` dari `"pending"` → `"done"`
4. User akan melihat status berubah di riwayat withdraw mereka

---

## 📁 Struktur File

```
oxin-mining/
├── index.html    ← Semua halaman ada di sini (SPA)
├── style.css     ← Semua styling
└── README.md     ← Panduan ini
```

---

## ⚙️ Kustomisasi

| Yang mau diubah | Di mana |
|---|---|
| Nama coin/platform | Cari `OXIN` di index.html |
| Poin per mining | Cari `increment(10)` di script |
| Rate withdraw | Cari `amount * 10` (1000 poin = Rp 10.000) |
| Bonus referral | Cari `increment(50)` di script |

---

## 🆘 Bantuan

Jika ada error, cek:
1. **Console browser** (F12 → Console) untuk melihat error
2. Pastikan config Firebase sudah benar
3. Pastikan domain sudah ditambah di Firebase Auth
