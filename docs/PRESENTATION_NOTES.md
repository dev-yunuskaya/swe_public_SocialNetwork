# Presentation Speaker Notes (English)

Aligned with the **actual codebase** (Express + Prisma + plain HTML/CSS/JS).  
Use with: `SocialNetwork_Final_Professional_Presentation (1).pptx` (23 slides).

**Repositories**

- Instructor / public: https://github.com/dev-yunuskaya/swe_public_SocialNetwork  
- Full project: https://github.com/dev-yunuskaya/swe_SocialNetwork  

---

## Before you present — fix outdated slide text

The PPTX template mentions **React** and **SPA**. Our project does **not** use React. Update these slides or say the corrected wording aloud:

| Slide | Wrong in template | Say / write instead |
|-------|-------------------|---------------------|
| 6 | Stack: … **React** … | **HTML, CSS, JavaScript** (no frontend framework) |
| 6 | SPA | **Multi-page web UI** served by Express (`public/*.html`) |
| 7 | React frontend | **Vanilla JavaScript** + REST API (`fetch` + JWT) |
| 7–9 | Postman-only automated tests | **62 Jest + Supertest** tests; Postman for manual API checks |

---

## Slide 1 — Title

**On slide (suggested text)**

```
Social Network — Term Project Presentation
CSE3044 Software Engineering

[Team names and student IDs]
```

**Speaker notes**

- Introduce the project name and course.
- Mention deliverables: working app, RSD, DSD, GitHub repo, automated tests.
- ~30 seconds.

**Team line (example — adjust to your group)**

- Ubeydullah Tuna – 150119713  
- Abdulah Puskar – 150119811  
- Muhammed Emin Erdoğan – 150123048  
- Yunus Kaya – 150119059  

---

## Slide 2 — Problem definition

**On slide**

```
Problem Definition
• Web platform for posts, follows, messaging, and notifications
• Needs secure auth, persistent data, and clear API boundaries
• Demonstrates full-stack design with PostgreSQL
```

**Speaker notes**

- Users want to share content, follow others, and get relevant suggestions—not a production-scale Facebook clone, but a **complete academic full-stack system**.
- Stress **security** (JWT, password hashing) and **data model** (users, posts, relationships).

---

## Slide 3 — Motivation & importance

**On slide**

```
Motivation & Importance
• Social features are familiar and easy to demonstrate
• Practice REST APIs, relational DB design, and testing
• Learn modular backend structure (routes → services → Prisma)
```

**Speaker notes**

- Connect to course goals: requirements (RSD), design (DSD), implementation, verification.
- One minute max.

---

## Slide 4 — Project aims

**On slide**

```
Project Aims
• User registration with interest areas
• Profiles, follow/unfollow, posts with hashtags and images
• Likes, comments, replies, notifications
• Direct messaging (REST)
• Rule-based personalized recommendations
• Maintainable, testable codebase
```

**Speaker notes**

- Each bullet maps to an RSD functional requirement.
- Emphasize **interest-based recommendations** and **hashtag extraction** as differentiators vs. a plain CRUD app.

---

## Slide 5 — Related work & research

**On slide**

```
Related Work
• Inspired by Twitter / Instagram / Reddit (follow, feed, hashtags)
• Our scope: smaller, self-hosted, rule-based recommendations (no ML)
• Clean REST API + documented endpoints (API.md, Postman)
```

**Speaker notes**

- We implement **standard patterns** (JWT, REST, relational schema) without external OAuth or video streaming.
- Recommendation is **transparent and rule-based**—easy to explain and test.

---

## Slide 6 — Scope

**On slide (corrected)**

```
Scope
• Web-only application (English UI)
• Stack: Node.js, Express, HTML/CSS/JavaScript, PostgreSQL, Prisma
• Images: JPEG/PNG, max 5 MB, stored under uploads/
• Designed for classroom / demo load (~50 concurrent users)
• Out of scope: video, OAuth providers, ML recommendations, WebSocket chat
```

**Speaker notes**

- Single server serves **API + static pages** (`src/server.js`, `public/`).
- Docker Compose for PostgreSQL in development.

---

## Slide 7 — Methodology

**On slide (corrected)**

```
Methodology
• Layered backend: routes → services → Prisma ORM
• JWT authentication, bcrypt password hashing
• Frontend: multi-page HTML + vanilla JS (api.js)
• Database migrations + demo seed script
• Testing: Jest + Supertest (62 tests); Postman for manual checks
```

**Speaker notes**

- **No React**—intentionally simple frontend per course constraints.
- Each feature tested via HTTP integration tests against a **separate test database**.

---

## Slide 8 — Technical algorithms & approaches

**On slide**

```
Technical Highlights
• JWT stateless auth; protected routes via middleware
• Hashtag parsing from post text (#technology); numeric-only tags ignored
• Feed: chronological posts from followed users + pagination
• Recommendations: score by interests, likes, comments; ~80% interest / ~20% discovery
• Interest learning: like/comment can add category to user profile (max 8 interests)
• Round-robin mixing so categories do not appear in long runs
```

**Speaker notes**

- This is the **core “how it works”** slide for technical questions.
- File references: `recommendation.service.js`, `interestLearning.service.js`, `hashtags.js`.
- No machine learning—weights and rules in code.

---

## Slide 9 — Tasks completed

**On slide**

```
Tasks Completed
✓ RSD & DSD documentation
✓ Prisma schema + migrations
✓ Auth, profile, follow, posts, feed, recommendations, messages, notifications
✓ English web UI (login, feed, profile, messages, notifications)
✓ Demo seed: 200 posts, 2 login accounts, 8 content categories
✓ 62 automated tests passing
✓ GitHub repository + README setup guide
```

**Speaker notes**

- Offer to run `npm test` live if time allows (~20 s).
- Point to `Social_Network_RSD.pdf` and `Social_Network_DSD.pdf` in repo root.

---

## Slide 10 — Class diagram

**On slide**

```
Class Diagram
[Insert diagram from DSD]
```

**Speaker notes**

- Paste the **DSD class diagram** (User, Post, Comment, Follow, Like, Message, Notification, Interest, Hashtag).
- Walk through: User → Post, User → Follow, Post ↔ Hashtag.
- If diagram missing: open `prisma/schema.prisma` as backup.

---

## Slide 11 — Design class diagram

**On slide**

```
Design Class Diagram
[Same or refined DSD diagram]
```

**Speaker notes**

- Optional duplicate of slide 10 or a more detailed design view.
- Keep under 2 minutes unless asked.

---

## Slide 12 — Use case diagram

**On slide**

```
Use Case Diagram
[Insert from DSD]
```

**Speaker notes**

- Actors: **Registered user**, **Guest** (register/login only).
- Core use cases: Register, Login, Create post, Follow, Like, Comment, Reply, View feed, View recommendations, Send message, View notifications.

---

## Slide 13 — Sequence diagram (overview)

**On slide**

```
Sequence Diagrams
[Overview or index of flows]
```

**Speaker notes**

- Introduce next slides as **concrete flows** from DSD.

---

## Slide 14 — Register with interest selection

**On slide**

```
Register with Interest Selection
[Sequence diagram: Client → POST /api/register → DB]
```

**Speaker notes**

- User sends username, email, password, **interests[]**.
- Server validates, hashes password, creates User + UserInterest rows.
- Optional live demo: `register.html` with Technology + Sports checked.

---

## Slide 15 — Recommendation system

**On slide**

```
Recommendation System
1. Exclude self + followed users’ posts
2. Score: profile interests, past likes/comments, author affinity, discovery
3. Select ~80% matching interests, ~20% exploration
4. Mix categories (round-robin), return enriched posts
```

**Speaker notes**

- `GET /api/recommendations` and `POST /api/recommendations/refresh` with optional exclude list.
- Frontend **For You** tab; seen posts tracked in `localStorage` (`seen.js`).

---

## Slide 16 — Messaging sequence

**On slide**

```
Messaging (REST)
POST /api/messages → store message
GET /api/messages/:partnerId → conversation history
```

**Speaker notes**

- Not real-time WebSocket; refresh or navigate to update thread.
- Demo: Ayşe → Can via Messages page.

---

## Slide 17 — Screenshot: Home / dashboard

**On slide**

```
Screenshot: Home (Feed)
```

**Speaker notes**

- Capture `feed.html` after login: **Following** tab, new post form, sample posts.
- Mention tabs: **Following** vs **For You**.

**Demo tip**

- URL: http://localhost:3000/feed.html  

---

## Slide 18 — Screenshot: Registration & login

**On slide**

```
Screenshot: Registration & Login
```

**Speaker notes**

- `register.html`: interest checkboxes (8 categories).
- `login.html`: demo account buttons (Ayşe / Can).

**Demo accounts**

- test.ayse@demo.com / test.can@demo.com — password123  

---

## Slide 19 — Screenshot: Feed & post creation

**On slide**

```
Screenshot: Feed & Post Creation
```

**Speaker notes**

- Show post with `#technology` and optional image.
- Hashtags stored and linked via PostHashtag table.
- Images constrained inside card (`post-media`, CSS).

---

## Slide 20 — Screenshot: Comments & likes

**On slide**

```
Screenshot: Comments & Likes
```

**Speaker notes**

- Feed inline like/comment OR `post.html` detail.
- Show **Reply** on a comment → triggers `comment_replied` notification.
- Like on `#travel` post can add **Travel** to user interests.

---

## Slide 21 — Screenshot: Messaging

**On slide**

```
Screenshot: Messaging Interface
```

**Speaker notes**

- Two-column layout: conversation list + thread.
- Start chat by username.

---

## Slide 22 — Screenshot: Notifications

**On slide**

```
Screenshot: Notifications
```

**Speaker notes**

- Types: started following you, liked your post, commented, replied to your comment.
- Mark read / mark all read.

---

## Slide 23 — Conclusion

**On slide**

```
Conclusion
• Full-stack social network delivered per RSD/DSD
• Secure auth, rich interactions, rule-based recommendations
• Tested (62 automated tests) and documented
• Repository: github.com/dev-yunuskaya/swe_public_SocialNetwork

Thank you — questions?
```

**Speaker notes**

- Recap strengths: clarity, testability, demo data for grading.
- Future work (if asked): WebSocket chat, ML recommendations, React refactor optional.

---

## Live demo script (~5–7 minutes)

| Step | Action | What to highlight |
|------|--------|-------------------|
| 1 | `docker compose up -d` && `npm start` | Stack running |
| 2 | Login as test.ayse@demo.com | JWT session |
| 3 | **Following** — see Can’s posts | Follow graph |
| 4 | New post with `#technology` | Hashtag + optional image |
| 5 | **For You** — mixed categories | Recommendations |
| 6 | Like a **Travel** or **Gaming** post | Interest learning |
| 7 | Second browser: login as test.can@demo.com | Two users |
| 8 | Reply to Ayşe’s comment on post detail | `comment_replied` |
| 9 | Ayşe → **Notifications** | Notification list |
| 10 | **Messages** — short message to Can | REST messaging |
| 11 | (Optional) `npm test` | 62 tests pass |

---

## Expected Q&A (short answers)

| Question | Answer |
|----------|--------|
| Why no React? | Course allowed plain HTML/CSS/JS; faster to align with RSD and test via REST. |
| How do recommendations work? | Rule-based scoring + 80/20 interest/discovery split + category mixing. |
| How do you test? | Jest + Supertest on isolated DB; 62 tests cover auth through notifications. |
| How to run? | README on public repo: Docker, migrate, demo:seed, npm start. |
| Real-time messages? | No WebSocket; polling/refresh model by design (out of scope). |
| Security? | JWT, bcrypt, author-only delete, file type/size checks on upload. |

---

## Copy-paste: one-line stack (for any slide footer)

```
Node.js · Express · PostgreSQL · Prisma · HTML/CSS/JavaScript · JWT · Jest
```
