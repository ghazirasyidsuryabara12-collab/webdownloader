# Website Statis Social Media Downloader

Landing page ini kini dilengkapi form untuk menempel URL video dan meminta backend mengunduhkannya secara otomatis.

## Struktur
- `index.html` - halaman utama berbahasa Indonesia beserta skrip interaktif.
- `styles.css` - gaya visual.
- `Dockerfile` - image Nginx sederhana untuk menyajikan konten statis.

## Menjalankan Secara Lokal
```
cd website
python -m http.server 8080
```
Lalu akses `http://localhost:8080`.

## Build & Jalankan dengan Docker
```
cd website
docker build -t smd-web .
docker run -d --name smd-web -p 8080:80 smd-web
```
Website dapat diakses melalui `http://SERVER_IP:8080`.

## Integrasi Backend Download
Form pada bagian "Coba Langsung di Browser" memanggil endpoint `POST /api/download`. Anda perlu menjalankan backend FastAPI di folder `../web-backend`:
```
cd ../web-backend
python -m venv .venv
. .venv/Scripts/activate  # Windows
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000
```
Secara default, frontend mengarahkan permintaan ke path relatif `/api/download`. Jika backend berjalan di host/port berbeda, lakukan salah satu opsi berikut:
1. Konfigurasi reverse proxy (misal Nginx) agar `/api/*` diarahkan ke backend.
2. Tambahkan variabel global sebelum skrip di `index.html`:
   ```html
   <script>
     window.SMD_API_BASE = "http://backend-server:8000";
   </script>
   ```

## Deploy di VPS dengan Docker Compose (opsional)
Contoh `docker-compose.yml` untuk menyajikan website dan backend sekaligus:
```
services:
  smd-web:
    build: ./website
    ports:
      - "80:80"
    depends_on:
      - smd-api
    restart: unless-stopped

  smd-api:
    build: ./web-backend
    ports:
      - "8000:8000"
    restart: unless-stopped
```

Pastikan firewall VPS membuka port HTTP (80/443) dan port backend bila dibutuhkan.
