# Sıfırdan Kurulum

Bu rehber, projeyi **sıfırdan** çalıştırmak içindir. Bilgisayarda yalnızca **Docker** kurulu olsa bile yeterlidir; PostgreSQL ayrı kurulmaz — Docker içinde indirilir ve çalışır.

**Ayrıca gerekli:** Node.js 18+ ve npm ([https://nodejs.org](https://nodejs.org) — LTS).

---

## 0. Gereksinimler

| Yazılım | Minimum | Kontrol |
|---------|---------|---------|
| **Node.js** | 18.x veya üzeri | `node -v` |
| **npm** | Node ile gelir | `npm -v` |
| **Docker** | Docker Desktop veya Docker Engine | `docker -v` |
| **Git** | İsteğe bağlı (klon için) | `git -v` |

> PostgreSQL’i sisteme kurmanız gerekmez. İlk `docker compose up -d` komutu **postgres imajını indirir** ve konteyneri başlatır.

---

## 1. Projeyi alın

**GitHub (önerilen):**

```bash
git clone https://github.com/dev-yunuskaya/swe_public_SocialNetwork.git
cd swe_public_SocialNetwork
```

**ZIP indirdiyseniz** (ör. `swe_public_SocialNetwork-main`):

```bash
cd "/yol/swe_public_SocialNetwork-main"
```

Bundan sonraki tüm komutlar proje kök dizininde çalıştırılır.

---

## 2. Ortam dosyası (`.env`)

```bash
cp .env.example .env
```

Varsayılan içerik `docker-compose.yml` ile uyumludur; genelde değiştirmeniz gerekmez:

```env
DATABASE_URL="postgresql://user:password@localhost:5432/social_network"
TEST_DATABASE_URL="postgresql://user:password@localhost:5432/social_network_test"
JWT_SECRET="change-me-in-production"
JWT_EXPIRES_IN="24h"
PORT=3000
UPLOAD_DIR="./uploads"
```

| Ayar | Değer |
|------|--------|
| DB kullanıcı | `user` |
| DB şifre | `password` |
| DB adı | `social_network` |
| Port | `3000` |

---

## 3. PostgreSQL: indirme ve container (Docker)

```bash
docker compose up -d
```

### İlk çalıştırmada arka planda olanlar

1. **`postgres:16-alpine` Docker imajı indirilir** (internet gerekir; birkaç dakika sürebilir).
2. Docker **network** oluşturulur (proje klasör adına göre, örn. `..._default`).
3. **`pgdata` volume** oluşturulur — veritabanı dosyaları burada kalır.
4. **PostgreSQL container** oluşturulup başlatılır.
5. Sunucu **localhost:5432** üzerinde dinler.

### Kontrol

```bash
docker compose ps
```

Beklenen: `postgres` servisi **Up**, port `0.0.0.0:5432->5432/tcp`.

Container hazır olana kadar birkaç saniye bekleyin, sonra migration adımına geçin.

---

## 4. Node bağımlılıkları

```bash
npm install
```

`node_modules` ve paketler kurulur (~400+ paket).

---

## 5. Veritabanı şeması (tablolar)

```bash
npm run db:migrate
```

Bu komut:

- `prisma migrate deploy` — migration dosyalarını uygular:
  - `20260530180000_init`
  - `20260530200000_comment_replies`
- `prisma generate` — Prisma client üretir

---

## 6. Demo verisi (önerilir)

```bash
npm run demo:seed
```

Bu komut:

- Varsa eski demo verisini temizler (`--reset`)
- **10 kullanıcı** (2 giriş: Ayşe, Can + 8 içerik havuzu)
- **~204 gönderi** (~yarısı görselli)
- Ayşe ↔ Can karşılıklı takip
- Görselleri indirip `uploads/` altına yazar

**Not:** İnternet gerekir. Süre: yaklaşık 30 saniye – 15 dakika (bağlantıya göre).

Sadece boş şema + ilgi alanları (demo olmadan):

```bash
npm run db:seed
```

---

## 7. Uygulamayı başlatın

```bash
npm start
```

Terminalde benzeri bir satır görünür:

```text
Social Network API listening on port 3000
```

Tarayıcı: **http://localhost:3000/login.html**

### Demo hesapları

| E-posta | Şifre |
|---------|--------|
| test.ayse@demo.com | password123 |
| test.can@demo.com | password123 |

---

## 8. Kurulum doğrulama (isteğe bağlı)

```bash
curl http://localhost:3000/health
# Beklenen: {"status":"ok"}

curl -X POST http://localhost:3000/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test.ayse@demo.com","password":"password123"}'
# Beklenen: JSON içinde "token" ve "user"
```

---

## 9. Testler (isteğe bağlı)

PostgreSQL container çalışırken:

```bash
npm test
```

Beklenen: **62** test geçer. Test veritabanı (`social_network_test`) ilk koşuda otomatik oluşturulur.

---

## Tüm adımlar — tek blok

```bash
cd swe_public_SocialNetwork   # veya ZIP klasörünüz
cp .env.example .env
docker compose up -d
npm install
npm run db:migrate
npm run demo:seed
npm start
```

---

## Sonraki açılışlar (seed tekrar gerekmez)

Veritabanı Docker volume’unda kalır:

```bash
cd swe_public_SocialNetwork
docker compose up -d
npm start
```

---

## İki kullanıcıyla test

**İki tarayıcı (tek sunucu):** Chrome → Ayşe, Firefox/gizli → Can — `npm start`

**İki port:**

```bash
# Terminal 1
npm start

# Terminal 2
npm run start:3001
```

- http://localhost:3000 → Ayşe  
- http://localhost:3001 → Can  

---

## Durdurma

| Ne | Komut |
|----|--------|
| Uygulama sunucusu | Terminalde `Ctrl+C` |
| PostgreSQL container | `docker compose down` |
| Veritabanını da sil (sıfırdan) | `docker compose down -v` |

---

## Sık karşılaşılan sorunlar

| Sorun | Çözüm |
|--------|--------|
| `docker: command not found` | Docker Desktop kurun ve açın |
| `npm: command not found` | Node.js LTS kurun |
| `ECONNREFUSED` veritabanı | `docker compose up -d`, 10 sn bekleyin |
| Port 3000 dolu | `.env` → `PORT=3001`, `http://localhost:3001/login.html` |
| `prisma` / migration hatası | `npm run db:migrate` tekrar |
| Giriş olmuyor | `npm run demo:seed` çalıştırıldı mı? |
| Demo seed yavaş / hata | İnternet; `npm run demo:seed` tekrar |

---

## İlgili bağlantılar

- Hoca / public repo: https://github.com/dev-yunuskaya/swe_public_SocialNetwork  
- İngilizce kısa README: repo kökündeki `README.md`  
- API: [API.md](API.md)
