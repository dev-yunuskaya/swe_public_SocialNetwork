# Social Network — CSE3044 Term Project

A small social network built with **Node.js**, **Express**, **PostgreSQL** (Prisma), and a plain **HTML/CSS/JavaScript** frontend. Users can register, follow others, post content with hashtags, like and comment, receive notifications, send direct messages, and see personalized recommendations.

**Course documents:** `Social_Network_RSD.pdf`, `Social_Network_DSD.pdf`  
**API reference:** `docs/API.md` · **Postman:** `docs/postman_collection.json`

---

## Requirements

- **Node.js** 18+
- **Docker** (for PostgreSQL)
- **npm**

---

## Setup (from scratch)

### 1. Clone and configure

```bash
git clone https://github.com/dev-yunuskaya/swe_public_SocialNetwork.git
cd swe_public_SocialNetwork
cp .env.example .env
```

Default `.env` matches `docker-compose.yml` (`user` / `password` / database `social_network`).

### 2. Start the database

```bash
docker compose up -d
```

### 3. Install dependencies and prepare the database

```bash
npm install
npm run db:migrate
```

### 4. Load demo data (recommended for grading)

```bash
npm run demo:seed
```

Creates demo users, 200 sample posts (~half with images), and follow relationships. Requires internet for image download; may take several minutes.

### 5. Run the application

```bash
npm start
```

Open in a browser: **http://localhost:3000/login.html**

---

## Demo accounts

| Email | Password |
|--------|----------|
| test.ayse@demo.com | password123 |
| test.can@demo.com | password123 |

These two accounts follow each other. Use **Following** for their posts and **For You** for recommendations from other demo users.

---

## Main features (quick tour)

1. **Log in** with a demo account (or register a new user with interests).
2. **Following** tab — posts from people you follow; create a new post with `#hashtags`.
3. **For You** tab — recommended posts (interest-based mix + discovery).
4. **Like / comment** on a post; open **Details** for replies and notifications.
5. **Profile** — view or edit bio and interests.
6. **Messages** — direct messaging between users.
7. **Notifications** — follows, likes, comments, and comment replies.

---

## Run tests

PostgreSQL must be running:

```bash
npm test
```

Expected: **62** passing tests (Jest + Supertest, separate test database).

---

## Useful commands

| Command | Description |
|---------|-------------|
| `npm start` | Start server (port 3000) |
| `npm run demo:seed` | Reset and load demo data |
| `npm run db:migrate` | Apply database migrations |
| `npm test` | Run automated tests |

---

## Project layout

```
src/        Backend (Express routes & services)
prisma/     Database schema and migrations
public/     Web UI (HTML, CSS, JS)
tests/      Automated tests
scripts/    Demo seed scripts
```

---

## Troubleshooting

| Issue | Fix |
|--------|-----|
| Cannot connect to database | Run `docker compose up -d` |
| Login fails | Run `npm run demo:seed` first |
| Port 3000 in use | Change `PORT` in `.env` |

---

