# Testing Document — Social Network (CSE3044)

| Field | Value |
|--------|--------|
| **Project** | Social Network Term Project |
| **Repository** | https://github.com/dev-yunuskaya/swe_public_SocialNetwork |
| **Test framework** | Jest 29 + Supertest 7 |
| **Total automated tests** | 62 |
| **Last verified** | All tests passing (`npm test`) |

---

## 1. Purpose

This document describes how the Social Network application is verified through **automated tests**. It covers test strategy, environment setup, tools, requirement traceability, and a catalogue of test cases grouped by functional area.

Manual API testing is supported separately via `docs/postman_collection.json` (Postman). This document focuses on the **Jest** suite under `tests/`.

---

## 2. Scope

### In scope

- REST API behaviour (HTTP status codes, JSON responses)
- Authentication and authorization (JWT)
- Core business rules (posts, hashtags, likes, comments, follows, feed, recommendations, messages, notifications)
- Recommendation scoring and selection logic (unit tests)
- Hashtag extraction utility (unit tests)

### Out of scope

- Frontend UI/browser automation (no Cypress/Playwright)
- Load/stress testing
- Security penetration testing
- WebSocket/real-time messaging (not implemented)

---

## 3. Test strategy

| Layer | Approach | Rationale |
|--------|-----------|-----------|
| **Unit** | Direct function calls on pure logic | Fast feedback on algorithms (hashtags, recommendation scoring/mixing) |
| **Integration** | HTTP requests to Express app via Supertest | Validates real routing, middleware, services, and database together |
| **Isolation** | Fresh database state per test (`beforeEach`) | Tests do not depend on execution order |
| **Data** | Separate test database `social_network_test` | Production/demo data is never modified by tests |

Tests run **sequentially** (`--runInBand`) to avoid database race conditions.

---

## 4. Test environment

### Prerequisites

- Node.js 18+
- Docker (PostgreSQL)
- `npm install` completed

### Configuration

| Variable | Purpose |
|----------|---------|
| `NODE_ENV=test` | Set automatically by test script |
| `TEST_DATABASE_URL` | Optional; defaults to `social_network_test` derived from `DATABASE_URL` |
| `JWT_SECRET` | Test secret in `tests/setup.js` |
| `UPLOAD_DIR` | `uploads-test` (isolated from demo uploads) |

### Database setup (automatic)

`tests/globalSetup.js`:

1. Creates `social_network_test` if missing  
2. Runs `prisma migrate deploy` against the test database  

### Per-test cleanup

`tests/helpers.js` → `resetDatabase()` deletes all user-generated rows and re-seeds interest categories before each integration test.

---

## 5. Tools and commands

| Tool | Role |
|------|------|
| **Jest** | Test runner and assertions |
| **Supertest** | HTTP client against in-process Express app |
| **Prisma** | Database access (same as application) |
| **bcrypt** | Verified indirectly via registration tests |

### Run all tests

```bash
docker compose up -d
npm test
```

**Expected result:**

```text
Test Suites: 10 passed, 10 total
Tests:       62 passed, 62 total
```

### Watch mode (development)

```bash
npm run test:watch
```

### Test file location

```
tests/
  auth.test.js
  profile.test.js
  follow.test.js
  posts.test.js
  posts-detail.test.js
  feed.test.js
  recommendations.test.js
  messages.test.js
  notifications.test.js
  utils.test.js
  helpers.js
  globalSetup.js
  setup.js
```

---

## 6. Requirements traceability (RSD)

| RSD area | Requirement summary | Test file(s) | Tests |
|----------|---------------------|--------------|-------|
| FR 3.2.1–3.2.2 | Registration & login | `auth.test.js` | 8 |
| FR 3.2.3 | Profile management | `profile.test.js` | 5 |
| FR 3.2.4 | Follow / unfollow | `follow.test.js` | 6 |
| FR 3.2.5 | Post management & hashtags | `posts.test.js` | 6 |
| FR 3.2.6 | Likes, comments, replies | `posts.test.js` | 7 |
| FR 3.2.7 | Personalized feed | `feed.test.js` | 4 |
| FR 3.2.8 | Recommendations | `recommendations.test.js` | 11 |
| FR 3.2.9 | Direct messaging | `messages.test.js` | 4 |
| FR 3.2.10 | Notifications | `notifications.test.js` | 4 |
| — | Hashtag utility | `utils.test.js` | 4 |
| — | Post detail / smoke | `posts-detail.test.js` | 4 |

---

## 7. Test case catalogue

### 7.1 Authentication (`auth.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| AUTH-01 | Valid registration with interests | HTTP 201 |
| AUTH-02 | Registration without interests | HTTP 400 |
| AUTH-03 | Duplicate username/email | HTTP 409 |
| AUTH-04 | Password stored as bcrypt hash | Hash ≠ plaintext |
| AUTH-05 | Valid login | JWT returned |
| AUTH-06 | Invalid credentials | HTTP 401 |
| AUTH-07 | Logout | HTTP 200 |
| AUTH-08 | Protected route without valid JWT | HTTP 401 |

### 7.2 Profile (`profile.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| PROF-01 | Get profile by user id | Profile JSON |
| PROF-02 | Update own profile | Updated fields |
| PROF-03 | Get profile by username | Profile JSON |
| PROF-04 | Unknown user | HTTP 404 |
| PROF-05 | Update without JWT | HTTP 401 |

### 7.3 Follow (`follow.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| FOL-01 | Follow another user | Success |
| FOL-02 | Follow self | HTTP 400 |
| FOL-03 | Follow non-existent user | HTTP 404 |
| FOL-04 | Follow twice (idempotent) | HTTP 200 |
| FOL-05 | Unfollow | Success |
| FOL-06 | Follow creates notification | `new_follower` |

### 7.4 Posts, likes, comments (`posts.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| POST-01 | Create text post with hashtags | Hashtags extracted |
| POST-02 | Empty content | HTTP 400 |
| POST-03 | Content over 500 chars | HTTP 400 |
| POST-04 | Author deletes own post | HTTP 200 |
| POST-05 | Delete another user's post | HTTP 403 |
| POST-06 | Invalid image type | HTTP 400 |
| LIKE-01 | Like post | Success |
| LIKE-02 | Duplicate like | HTTP 409 |
| LIKE-03 | Unlike post | Success |
| CMT-01 | Add comment | HTTP 201 |
| CMT-02 | Delete comment (author only) | 403 for non-author |
| CMT-03 | Like/comment notifications | `post_liked`, `post_commented` |
| CMT-04 | Like adds interest from hashtag | e.g. Travel on profile |
| CMT-05 | Reply to comment | `comment_replied` notification |

### 7.5 Feed (`feed.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| FEED-01 | Feed shows followed users' posts | Non-empty list |
| FEED-02 | No follows → empty feed | Empty array |
| FEED-03 | Pagination (page/limit) | Correct page size |
| FEED-04 | Exclude post IDs | Excluded posts omitted |

### 7.6 Recommendations (`recommendations.test.js`)

**Unit tests**

| ID | Description | Expected |
|----|-------------|----------|
| REC-U01 | Score by matching hashtags | Weighted score |
| REC-U02 | Liked author boost | Higher score |
| REC-U03 | Like signal > profile interest alone | Verified weights |
| REC-U04 | Discovery score for non-matching tags | Low discovery weight |
| REC-U05 | ~80% interest / ~20% discovery in 10 picks | Ratio bounds |
| REC-U06 | Round-robin category mix | Adjacent variety |

**Integration tests**

| ID | Description | Expected |
|----|-------------|----------|
| REC-I01 | GET recommendations | Array of posts |
| REC-I02 | Own posts not recommended | Excluded |
| REC-I03 | Refresh with exclude list | No overlap |

### 7.7 Messages (`messages.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| MSG-01 | Send message | HTTP 201 |
| MSG-02 | Empty message | HTTP 400 |
| MSG-03 | Invalid recipient | HTTP 404 |
| MSG-04 | Conversation order | Chronological |

### 7.8 Notifications (`notifications.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| NOT-01 | List notifications | Array |
| NOT-02 | Mark one as read | Updated |
| NOT-03 | Mark all as read | All read |
| NOT-04 | Invalid notification id | HTTP 404 |

### 7.9 Utilities (`utils.test.js`)

| ID | Description | Expected |
|----|-------------|----------|
| UTL-01 | Extract hashtags, lowercase | Normalized tags |
| UTL-02 | Deduplicate hashtags | Unique list |
| UTL-03 | No hashtags in plain text | `[]` |
| UTL-04 | Ignore numeric-only tags (`#3`) | Only valid tags kept |

---

## 8. Manual testing (supplementary)

| Artefact | Location | Use |
|----------|----------|-----|
| Postman collection | `docs/postman_collection.json` | Manual API exploration |
| API reference | `docs/API.md` | Endpoint documentation |
| Demo accounts | README | `test.ayse@demo.com` / `test.can@demo.com` — `password123` |

Suggested manual scenarios: register → login → create post → follow → feed → recommendations → message → notifications.

---

## 9. Test results summary

| Metric | Value |
|--------|--------|
| Test suites | 10 |
| Total tests | 62 |
| Passed | 62 |
| Failed | 0 |
| Unit tests | 15 |
| Integration (API) tests | 47 |

All tests are executed against the **test database** only. Running `npm test` does not delete or alter demo seed data in `social_network`.

---

## 10. Known limitations

1. **No UI automation** — frontend is tested manually in the browser.  
2. **Single-process execution** — tests are not parallelized across workers.  
3. **Network** — demo image seeding is not part of `npm test`; only API/DB logic is tested.  
4. **Recommendation randomness** — integration tests use fixed seeds where needed; unit tests validate deterministic scoring/mixing.

---

## 11. References

- Software Requirements Specification: `Social_Network_RSD.pdf`  
- Design Specification: `Social_Network_DSD.pdf`  
- Turkish test notes (internal): `docs/TESTLER.md`  
- Setup guide: `README.md`

---

**Author:** Yunus Kaya · CSE3044 Software Engineering Term Project
