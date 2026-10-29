# PhoneTempGuard — simulasi suhu dua HP via MQTT

## Isi
- `phone_temp.py`: dijalankan di Termux pada HP A dan HP B.
- `dashboard/index.html`: dashboard live di browser laptop.

## 1. Pilih ID unik
Buat ID sederhana, misalnya `kelas3_tim07_x9`. Ganti nilai `PROJECT_ID` di `phone_temp.py` dan isi ID yang sama pada dashboard.

Jangan gunakan ID contoh jika kelompok lain mungkin memakainya. Broker publik dibagi bersama, jadi jangan kirim data pribadi.

## 2. Siapkan kedua HP
Pasang Termux dan Termux:API dari sumber instalasi yang sama/kompatibel. Buka Termux pada tiap HP, lalu jalankan:

```bash
pkg update
pkg install python termux-api
pip install "paho-mqtt>=2.0"
```

Beri izin yang diminta Android. Salin `phone_temp.py` ke folder Termux masing-masing.

Cek apakah suhu baterai bisa dibaca:
```bash
termux-battery-status
```
Cari field `temperature`. Tidak semua perangkat/versi API memberikan data yang sama.

## 3. Jalankan HP
Untuk pembacaan suhu baterai aktual (jika tersedia):
```bash
python phone_temp.py --device A
```
Pada HP satunya:
```bash
python phone_temp.py --device B
```

Jika suhu tidak bisa dibaca, gunakan mode demo:
```bash
python phone_temp.py --device A --simulate --demo-hot
```
dan di HP B:
```bash
python phone_temp.py --device B --simulate
```
`--demo-hot` membuat suhu simulasi berubah antara normal dan panas secara berkala. Mode `--simulate` biasa meminta input suhu dari terminal; masukkan `42` untuk menguji peringatan.

## 4. Dashboard laptop
Buka `dashboard/index.html` di browser laptop. Isi Project ID yang sama, lalu klik **Hubungkan MQTT**. Dashboard menggunakan MQTT over WebSocket TLS ke HiveMQ publik; laptop perlu internet.

Jika browser membatasi koneksi saat membuka file lokal, jalankan server web sederhana di folder project pada laptop:
```bash
python -m http.server 8000
```
Kemudian buka `http://localhost:8000/dashboard/`.

## 5. Uji
1. Jalankan script di kedua HP.
2. Hubungkan dashboard.
3. Atur ambang awal 40°C.
4. Uji dengan mode simulasi: kirim suhu 33°C, lalu 42°C.
5. Pastikan dashboard menampilkan status panas dan HP lain menerima notifikasi jika Termux:API mendukung notifikasi/getaran.

## Catatan penting
- Suhu yang dibaca adalah suhu baterai yang dilaporkan Android, bukan selalu suhu CPU atau suhu permukaan HP.
- Ambang 40°C hanya nilai demo. Sesuaikan dengan definisi proyek dan perangkat; jangan jadikan ini alat keselamatan/medis.
- HiveMQ publik tidak memerlukan akun, tetapi topik bersifat publik dan dapat dibaca pihak lain. Gunakan Project ID yang sulit ditebak dan jangan kirim data pribadi.
- Notifikasi Android mungkin memerlukan izin notifikasi, dan proses Termux perlu tetap berjalan di latar depan. Nonaktifkan pembatasan baterai Termux jika proses sering berhenti.
- Jika broker publik sedang membatasi koneksi atau jaringan memblokir WebSocket, coba lagi atau gunakan broker yang disediakan dosen.
