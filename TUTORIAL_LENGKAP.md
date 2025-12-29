# 🚀 TUTORIAL LENGKAP - Dari NOL sampai RUNNING
## Portfolio Application dengan Docker - Scalable & Production-Ready

---

## 📋 CHECKLIST REQUIREMENT TUGAS

### ✅ Yang Sudah Dibuat:

**1. Website/Aplikasi Scalable**
- [x] Load Balancer (Nginx)
- [x] Multiple Backend Instances (3 pods, auto-scale sampai 10)
- [x] Database dengan Connection Pooling
- [x] Redis Cache untuk Performance
- [x] Auto-scaling dengan Kubernetes
- [x] Monitoring dengan Prometheus & Grafana

**2. Arsitektur Scalable, Reliable, dan Aman**
- [x] Horizontal Pod Autoscaler (HPA)
- [x] Health Checks & Auto-restart
- [x] Rate Limiting & Security Headers
- [x] Graceful Shutdown
- [x] Database Indexing & Optimization

**3. Desain Menggunakan Docker**
- [x] Dockerfile untuk Backend
- [x] Docker Compose untuk Orchestration
- [x] Multi-stage Build
- [x] Container Networking
- [x] Volume Persistence

**4. Uji Hasil Implementasi**
- [x] Health Check Endpoints
- [x] Load Testing Guide
- [x] Performance Monitoring
- [x] Metrics Collection

---

## 🎯 TAHAP 1: PERSIAPAN AWAL (WINDOWS)

### Step 1.1: Install Docker Desktop

**Windows 10/11:**
1. Download Docker Desktop: https://www.docker.com/products/docker-desktop/
2. Klik 2x file installer (Docker Desktop Installer.exe)
3. Ikuti wizard instalasi → Next → Next → Install
4. **WAJIB: Restart komputer setelah instalasi**
5. Setelah restart, buka **Docker Desktop** dari Start Menu
6. Tunggu sampai muncul "Docker Desktop is running" (ikon paus di system tray bawah kanan)

**⚠️ Catatan Penting untuk Windows:**
- Windows 10: Minimal versi 2004, Build 19041
- Butuh WSL 2 (Windows Subsystem for Linux) - biasanya auto-install saat install Docker
- Jika diminta enable virtualization, restart PC → masuk BIOS (tekan Del/F2) → enable VT-x/AMD-V

### Step 1.2: Verifikasi Instalasi

**Buka Command Prompt/PowerShell:**
- Tekan tombol `Windows + R`
- Ketik `cmd` (atau `powershell`)
- Tekan Enter

**Ketik command ini satu per satu:**

```cmd
docker --version
```
✅ Output yang benar: `Docker version 24.0.x, build xxxxx`

```cmd
docker-compose --version
```
✅ Output yang benar: `Docker Compose version v2.x.x`

```cmd
docker run hello-world
```
✅ Jika berhasil, akan muncul: `Hello from Docker!` dengan text panjang

**❌ Jika ada error "docker is not recognized":**
1. Pastikan Docker Desktop sedang running (cek ikon paus di system tray)
2. Klik kanan Docker Desktop → Restart
3. Tutup dan buka ulang Command Prompt
4. Coba lagi

---

## 📦 TAHAP 2: DOWNLOAD & SETUP PROJECT (WINDOWS)

### Step 2.1: Buat Folder Project

**Cara 1: Pakai File Explorer**
1. Buka File Explorer (`Windows + E`)
2. Pilih lokasi (misal: `C:\Users\NamaAnda\Documents`)
3. Klik kanan → New → Folder
4. Beri nama: `portfolio-app`

**Cara 2: Pakai Command Prompt**
```cmd
cd C:\Users\%USERNAME%\Documents
mkdir portfolio-app
cd portfolio-app
```

### Step 2.2: Copy Semua File

**Copy semua file dari outputs ke folder `portfolio-app`:**

```
C:\Users\NamaAnda\Documents\portfolio-app\
├── backend\
│   ├── server.js
│   ├── Dockerfile
│   └── package.json
├── database\
│   └── schema.sql
├── nginx\
│   └── nginx.conf
├── k8s\
│   └── deployment.yaml
├── monitoring\
│   └── prometheus.yml
├── docker-compose.yml
├── docker-compose.monitoring.yml
├── .env.example
├── README.md
└── QUICKSTART.md
```

**⚠️ Penting:** Pastikan struktur folder persis seperti di atas!

### Step 2.3: Buat File .env

**SANGAT PENTING!** File ini berisi password dan konfigurasi.

**Cara Buat File .env di Windows:**

**Option 1: Pakai Notepad**
1. Klik kanan di folder `portfolio-app` → New → Text Document
2. Buka file text tersebut dengan Notepad
3. Copy paste isi ini:

```env
NODE_ENV=production
PORT=3001

# Database
DB_HOST=postgres
DB_PORT=5432
DB_NAME=portfolio_db
DB_USER=postgres
DB_PASSWORD=MySecurePass123!

# Redis
REDIS_URL=redis://:RedisPass456@redis:6379
REDIS_PASSWORD=RedisPass456

# Frontend
FRONTEND_URL=http://localhost:3000
```

4. Klik File → Save As
5. **File name:** ketik `.env` (HARUS pakai titik di depan!)
6. **Save as type:** pilih "All Files (*.*)" (BUKAN .txt!)
7. Klik Save
8. **Hapus file `.env.txt` atau `New Text Document.txt` yang tadi**

**Option 2: Copy dari .env.example (Lebih Mudah)**
```cmd
cd C:\Users\%USERNAME%\Documents\portfolio-app
copy .env.example .env
notepad .env
```
Lalu edit password-nya dan Save (Ctrl+S)

**⚠️ PENTING:** 
- Ganti `MySecurePass123!` dengan password yang kuat
- Ganti `RedisPass456` dengan password Redis yang kuat
- File harus bernama `.env` (dengan titik), BUKAN `.env.txt`

**Cara cek file .env sudah benar:**
```cmd
dir /a
```
Harus ada file bernama `.env` (tanpa extension .txt)

---

## 🚀 TAHAP 3: JALANKAN APLIKASI (WINDOWS)

### Step 3.1: Buka Command Prompt di Folder Project

**Cara 1: Dari File Explorer**
1. Buka folder `portfolio-app` di File Explorer
2. Ketik `cmd` di address bar (tempat path folder)
3. Tekan Enter
4. Command Prompt akan terbuka di folder tersebut

**Cara 2: Manual**
```cmd
cd C:\Users\%USERNAME%\Documents\portfolio-app
```

**Verifikasi Anda di folder yang benar:**
```cmd
dir
```
✅ Harus muncul: `docker-compose.yml`, `.env`, folder `backend`, dll

### Step 3.2: Start Docker Compose

**Pastikan Docker Desktop sedang running!** (Cek ikon paus di system tray)

```cmd
docker-compose up -d
```

**Apa yang Terjadi?**
1. ✅ Download images (PostgreSQL, Redis, Nginx, Node.js) - **Pertama kali: 5-10 menit tergantung internet**
2. ✅ Build backend container dari Dockerfile (2-3 menit)
3. ✅ Create network untuk komunikasi antar container
4. ✅ Start PostgreSQL database
5. ✅ Start Redis cache
6. ✅ Start 3 backend API instances
7. ✅ Start Nginx load balancer
8. ✅ Run database schema (create tables & insert sample data)

**Progress yang muncul:**
```
[+] Running 6/6
 ✔ Network portfolio_network     Created
 ✔ Container portfolio_db         Started
 ✔ Container portfolio_redis      Started
 ✔ Container portfolio_backend_1  Started
 ✔ Container portfolio_backend_2  Started
 ✔ Container portfolio_backend_3  Started
 ✔ Container portfolio_nginx      Started
```

### Step 3.3: Tunggu Sampai Semua Healthy

**Check status (tunggu 1-2 menit):**
```cmd
docker-compose ps
```

**✅ Output yang benar:**
```
NAME                  STATUS
portfolio_nginx       Up (healthy)
portfolio_backend_1   Up (healthy)
portfolio_backend_2   Up (healthy)
portfolio_backend_3   Up (healthy)
portfolio_db          Up (healthy)
portfolio_redis       Up (healthy)
```

**❌ Jika ada yang tidak healthy atau status "starting":**
```cmd
# Tunggu 2 menit lagi, lalu cek ulang
docker-compose ps

# Lihat logs untuk debug
docker-compose logs postgres
docker-compose logs backend_1
```

**Troubleshooting Windows:**

**Problem: "Port 80 is already in use"**
```cmd
# Cek apa yang pakai port 80
netstat -ano | findstr :80

# Ganti port di docker-compose.yml
notepad docker-compose.yml
# Edit baris: "80:80" jadi "8080:80"
# Save, lalu restart:
docker-compose down
docker-compose up -d
# Akses di http://localhost:8080
```

**Problem: "Error response from daemon"**
- Restart Docker Desktop
- Tunggu sampai running
- Coba lagi: `docker-compose up -d`

---

## ✅ TAHAP 4: UJI COBA APLIKASI

### Step 4.1: Test Health Check

**Di browser, buka:**
```
http://localhost/health
```

**Atau di terminal:**
```bash
curl http://localhost/health
```

**Expected output:**
```json
{
  "status": "healthy",
  "timestamp": "2024-12-27T...",
  "uptime": 123.45
}
```

### Step 4.2: Test API Endpoints

```bash
# Get Profile
curl http://localhost/api/profile

# Get Experiences
curl http://localhost/api/experiences

# Get Skills
curl http://localhost/api/skills

# Get Projects
curl http://localhost/api/projects

# Get Certificates
curl http://localhost/api/certificates
```

### Step 4.3: Buka Frontend (Portfolio Website)

**Di browser:**
```
http://localhost
```

Anda akan melihat portfolio website dengan dark theme yang sudah saya buat!

---

## 📊 TAHAP 5: MONITORING (OPTIONAL)

### Step 5.1: Start Monitoring Stack

```bash
# Start Prometheus & Grafana
docker-compose -f docker-compose.monitoring.yml up -d
```

### Step 5.2: Akses Dashboard

**Grafana** (Visualization):
```
http://localhost:3002
Username: admin
Password: admin
```

**Prometheus** (Metrics):
```
http://localhost:9090
```

### Step 5.3: Setup Dashboard di Grafana

1. Login ke Grafana
2. Click "+" → "Import"
3. Masukkan dashboard ID: `1860` (Node Exporter Full)
4. Click "Load" → "Import"
5. Lihat metrics real-time!

---

## 🧪 TAHAP 6: UJI SCALABILITY

### Test 6.1: Load Testing

**Install Apache Bench:**
```bash
# Ubuntu/Debian
sudo apt-get install apache2-utils

# macOS
brew install httpd
```

**Run Load Test:**
```bash
# Test 1: 1000 requests, 100 concurrent
ab -n 1000 -c 100 http://localhost/api/profile

# Test 2: 10000 requests, 200 concurrent (heavy load)
ab -n 10000 -c 200 -k http://localhost/api/profile
```

**Lihat Hasil:**
- Requests per second: Harus > 500 req/s
- Failed requests: Harus 0
- Time per request: Harus < 100ms (P95)

### Test 6.2: Scale Backend Instances

```bash
# Scale to 5 instances
docker-compose up -d --scale backend_1=5

# Check running instances
docker-compose ps | grep backend

# Run load test lagi, should handle more traffic!
ab -n 10000 -c 300 http://localhost/api/profile
```

### Test 6.3: Test Cache Performance

```bash
# First request (cache MISS - slower)
time curl http://localhost/api/profile

# Second request (cache HIT - faster!)
time curl http://localhost/api/profile

# Check cache hit rate di Grafana
# Seharusnya > 80% cache hit
```

### Test 6.4: Test Failover

```bash
# Stop 1 backend instance
docker stop portfolio_backend_1

# Test API masih works (load balancer redirect ke instance lain)
curl http://localhost/api/profile

# Start kembali
docker start portfolio_backend_1
```

---

## 🎨 TAHAP 7: CUSTOMIZE DATA

### Step 7.1: Akses Database

```bash
# Masuk ke PostgreSQL container
docker exec -it portfolio_db psql -U postgres -d portfolio_db
```

### Step 7.2: Update Data

```sql
-- Update profile
UPDATE profiles 
SET name = 'Nama Anda', 
    title = 'Job Title Anda',
    email = 'email@anda.com'
WHERE id = 1;

-- Lihat data
SELECT * FROM profiles;

-- Update experience
UPDATE experiences 
SET title = 'Software Engineer',
    company = 'Company Anda'
WHERE id = 1;

-- Exit
\q
```

### Step 7.3: Clear Cache (agar data baru muncul)

```bash
# Clear Redis cache
docker exec portfolio_redis redis-cli FLUSHALL

# Atau restart backend
docker-compose restart backend_1 backend_2 backend_3
```

---

## 📸 TAHAP 8: DOKUMENTASI UNTUK TUGAS

### Screenshot yang Perlu Diambil:

1. **Arsitektur System**
   - Buka file `docs/architecture.mermaid` di browser
   - Screenshot diagram arsitektur

2. **Docker Compose Running**
   ```bash
   docker-compose ps
   ```
   - Screenshot output semua container running & healthy

3. **Health Check Response**
   - Screenshot browser dengan `http://localhost/health`

4. **API Response**
   - Screenshot browser dengan `http://localhost/api/profile`

5. **Load Test Results**
   ```bash
   ab -n 1000 -c 100 http://localhost/api/profile
   ```
   - Screenshot hasil load testing

6. **Monitoring Dashboard**
   - Screenshot Grafana dashboard dengan metrics

7. **Scalability Test**
   ```bash
   docker-compose ps
   ```
   - Screenshot setelah scaling (5-10 instances)

8. **Frontend Website**
   - Screenshot portfolio website yang berjalan

---

## 🎯 KESIMPULAN TUGAS

### Checklist Lengkap:

- [x] **Aplikasi Scalable**: ✅ Load balancer + auto-scaling
- [x] **Arsitektur**: ✅ Documented dengan diagram
- [x] **Docker**: ✅ Dockerfile + Docker Compose
- [x] **Testing**: ✅ Load test + monitoring

### Deliverables untuk Dikumpulkan:

1. ✅ **Source Code** (semua file dalam folder portfolio-app)
2. ✅ **README.md** (dokumentasi lengkap)
3. ✅ **Screenshot** (8 screenshot di atas)
4. ✅ **Architecture Diagram** (Mermaid diagram)
5. ✅ **Load Test Results** (Apache Bench output)
6. ✅ **Docker Compose YAML** (konfigurasi deployment)

---

## 🆘 TROUBLESHOOTING KHUSUS WINDOWS

### Problem 1: "docker is not recognized as internal or external command"

**Penyebab:** Docker belum masuk ke system PATH atau Docker Desktop belum running

**Solusi:**
1. Pastikan Docker Desktop sedang running (cek ikon paus di system tray)
2. Restart Docker Desktop
3. Tutup dan buka ulang Command Prompt
4. Jika masih error:
   - Windows + R → ketik `sysdm.cpl` → Enter
   - Tab "Advanced" → "Environment Variables"
   - Cek di "Path" ada: `C:\Program Files\Docker\Docker\resources\bin`
   - Jika tidak ada, tambahkan path tersebut
   - Restart PC

### Problem 2: Port 80 Already in Use

**Error:** "Bind for 0.0.0.0:80 failed: port is already allocated"

**Penyebab:** Ada program lain (IIS, Skype, Apache) yang pakai port 80

**Solusi A - Cari program yang pakai port 80:**
```cmd
netstat -ano | findstr :80
```
Output: `TCP  0.0.0.0:80  0.0.0.0:0  LISTENING  1234`
- Angka 1234 adalah Process ID (PID)

```cmd
tasklist | findstr 1234
```
Muncul nama program, stop program tersebut atau:

**Solusi B - Ganti port Nginx:**
```cmd
notepad docker-compose.yml
```
Cari baris:
```yaml
nginx:
  ports:
    - "80:80"     # Ganti jadi "8080:80"
    - "443:443"
```
Ubah jadi:
```yaml
nginx:
  ports:
    - "8080:80"   # Akses di http://localhost:8080
    - "443:443"
```
Save, lalu:
```cmd
docker-compose down
docker-compose up -d
```

### Problem 3: WSL 2 Installation Error

**Error:** "WSL 2 installation is incomplete"

**Solusi:**
1. Buka PowerShell **sebagai Administrator** (klik kanan → Run as Administrator)
2. Jalankan:
```powershell
wsl --install
```
3. Restart PC
4. Buka Docker Desktop lagi

### Problem 4: Container Exited / Unhealthy

**Cek status:**
```cmd
docker-compose ps
```

**Jika backend_1 status "Exited":**
```cmd
# Lihat logs error
docker-compose logs backend_1

# Common errors:
# - "ECONNREFUSED" → Database belum ready, tunggu 30 detik
# - "ENOTFOUND postgres" → Network issue, restart:
docker-compose down
docker-compose up -d
```

**Jika postgres status "Restarting":**
```cmd
# Cek logs
docker-compose logs postgres

# Biasanya password issue, cek file .env
notepad .env
# Pastikan DB_PASSWORD tidak ada spasi atau karakter aneh
```

### Problem 5: Firewall Blocking Docker

**Error:** "Error response from daemon: Get https://registry-1.docker.io/v2/"

**Solusi:**
1. Windows Defender Firewall → Allow an app
2. Cari "Docker Desktop" → centang Private dan Public
3. Atau matikan firewall sementara (untuk testing)

### Problem 6: Disk Space Full

**Error:** "no space left on device"

**Solusi:**
```cmd
# Hapus unused containers, images, networks
docker system prune -a

# Cek disk usage
docker system df
```

### Problem 7: File .env Tidak Terbaca

**Penyebab:** File bernama `.env.txt` bukan `.env`

**Solusi:**
```cmd
# Cek nama file sebenarnya
dir /a
```
Jika muncul `.env.txt`:
```cmd
# Rename tanpa .txt
ren .env.txt .env

# Atau buat ulang
notepad .env
# Paste konfigurasi, Save as → All Files → .env
```

### Problem 8: Permission Denied (Rare di Windows)

**Solusi:**
```cmd
# Jalankan Command Prompt sebagai Administrator
# Windows + X → Command Prompt (Admin)
# Atau PowerShell (Admin)
```

---

## 🎯 KESIMPULAN TUGAS
