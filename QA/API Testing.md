*Written for a QA engineer with strong UI/browser testing skills who needs to close the API gap. Study order matters: Part 1 and 2 are the actual bottleneck. Don't skip to the tools.*

---

## Part 0 — Why this matters and where API tests sit

### The test pyramid

```
        /\
       /  \      UI / E2E tests        ← few, slow, brittle, expensive
      /----\
     /      \    API / Integration     ← many, fast, stable
    /--------\
   /          \  Unit tests            ← most, instant, cheapest
  /------------\
```

API tests are the sweet spot for QA automation:

| | UI test | API test |
|---|---|---|
| Speed | 5–30 seconds | 50–500 milliseconds |
| Breaks when CSS changes | Yes | No |
| Needs a browser | Yes | No |
| Can test error handling | Hard | Easy |
| Can run before frontend exists | No | Yes |

**The line to have ready in an interview:** *"A UI test tells you the button is broken. An API test tells you whether it's the frontend or the backend. And I can write API tests before the UI exists, so I'm not blocked waiting for frontend work."*

### What "testing the API" actually means

You are verifying the **contract** between client and server:

- Given this request, do I get the documented status code?
- Is the response body the documented shape, with the documented types?
- Did the data actually change in the system?
- Does it fail *correctly* when I send garbage?
- Does it refuse people who shouldn't have access?

---

## Part 1 — HTTP fundamentals (memorize this section)

### Anatomy of a request

```http
POST /api/v1/users HTTP/1.1          ← method, path, version
Host: api.example.com                 ← headers start
Content-Type: application/json
Authorization: Bearer eyJhbGciOi...
Accept: application/json
                                      ← blank line
{                                     ← body
  "email": "test@example.com",
  "password": "Str0ngP@ss",
  "name": "Test User"
}
```

**Four parts:** method, URL (+ query params), headers, body.

### Anatomy of a response

```http
HTTP/1.1 201 Created                  ← status line
Content-Type: application/json
Location: /api/v1/users/8842          ← where the new thing lives
X-Request-Id: abc-123

{
  "id": 8842,
  "email": "test@example.com",
  "name": "Test User",
  "createdAt": "2026-08-26T04:12:00Z"
}
```

**Three parts:** status code, headers, body.

### HTTP methods

| Method    | Purpose                     | Has body?  | Safe? | Idempotent?     |
| --------- | --------------------------- | ---------- | ----- | --------------- |
| `GET`     | Read a resource             | No         | Yes   | Yes             |
| `POST`    | Create a resource           | Yes        | No    | **No**          |
| `PUT`     | Replace a resource entirely | Yes        | No    | Yes             |
| `PATCH`   | Update part of a resource   | Yes        | No    | Not necessarily |
| `DELETE`  | Remove a resource           | Usually no | No    | Yes             |
| `HEAD`    | Like GET, headers only      | No         | Yes   | Yes             |
| `OPTIONS` | What methods are allowed    | No         | Yes   | Yes             |

**Safe** = does not change server state. **Idempotent** = calling it 5 times has the same effect as calling it once.

> **Interview trap:** "Is DELETE idempotent?" Yes. Delete user 42 five times: after the first call the user is gone, and the *state* is identical after calls 2–5. The **status code** may differ (204 then 404), but idempotency is about resulting state, not the response. Say that out loud and you've answered better than most mid-level candidates.

> **Why POST isn't idempotent:** POST the same "create user" body twice and you either get two users or a 409. Different outcome. This is exactly the Round 3 test-data bug — non-idempotent operations leaving state behind.

**PUT vs PATCH:** `PUT /users/42` with `{"name": "Bob"}` should *replace* the whole user — fields you omitted may be wiped to null. `PATCH /users/42` with the same body updates only `name`. A classic bug: an API documented as PUT that behaves like PATCH, or a PUT that silently nulls out fields the client didn't send.

### Status codes — the ones you must know cold

**2xx — Success**

| Code | Name | When |
|---|---|---|
| `200` | OK | Successful GET, PUT, PATCH; POST that doesn't create |
| `201` | Created | Successful POST that created a resource. Should include a `Location` header |
| `202` | Accepted | Request queued, processing async. Not done yet |
| `204` | No Content | Success with empty body. Typical for DELETE |

**3xx — Redirection**

| Code | Name | When |
|---|---|---|
| `301` | Moved Permanently | Resource has a new permanent URL |
| `302` | Found | Temporary redirect |
| `304` | Not Modified | Cached copy is still valid |

**4xx — Client's fault (you sent something wrong)**

| Code | Name | When |
|---|---|---|
| `400` | Bad Request | Malformed syntax — unparseable JSON, missing required field |
| `401` | Unauthorized | **Not authenticated.** No token, bad token, expired token |
| `403` | Forbidden | **Authenticated but not allowed.** Valid token, insufficient permission |
| `404` | Not Found | Resource doesn't exist |
| `405` | Method Not Allowed | Endpoint exists, wrong verb (DELETE on a read-only endpoint) |
| `409` | Conflict | State collision — duplicate email, editing a stale version |
| `415` | Unsupported Media Type | Sent XML where JSON was expected |
| `422` | Unprocessable Content | Syntax valid, **semantics** invalid — well-formed JSON, but `age: -5` |
| `429` | Too Many Requests | Rate limited |

**5xx — Server's fault**

| Code | Name | When |
|---|---|---|
| `500` | Internal Server Error | Unhandled exception. **Always a bug** |
| `502` | Bad Gateway | Upstream service returned garbage |
| `503` | Service Unavailable | Server down or overloaded |
| `504` | Gateway Timeout | Upstream service didn't respond in time |

**The three distinctions interviewers probe:**

1. **401 vs 403** — 401 is *"I don't know who you are."* 403 is *"I know who you are, and no."* Log in with no token → 401. Log in as a regular user and try to delete another user's account → 403.

2. **400 vs 422** — 400 is *"I can't parse this."* 422 is *"I parsed it, and it's nonsense."* Trailing comma in JSON → 400. Valid JSON with `"email": "not-an-email"` → 422. **In practice many APIs use 400 for both** — that's acceptable, but it must be *consistent*, and inconsistency is a reportable bug.

3. **Any 4xx vs any 5xx** — 4xx means you sent something bad and the server handled it gracefully. 5xx means the server *crashed*. **Sending bad input should never produce a 500.** If your malformed-JSON test returns 500, that's a real bug: an unhandled exception. Worse if the response contains a stack trace, which is an information disclosure vulnerability.

### Headers worth knowing

**Request headers**

- `Content-Type: application/json` — what I'm sending
- `Accept: application/json` — what I want back
- `Authorization: Bearer <token>` — who I am
- `User-Agent` — what client I am

**Response headers**

- `Content-Type` — what the server sent
- `Location` — URL of a newly created resource (on 201)
- `Set-Cookie` — session cookies
- `Cache-Control`, `ETag` — caching
- `X-RateLimit-Remaining` — how many calls you have left
- Security: `Strict-Transport-Security`, `X-Content-Type-Options`, `Content-Security-Policy`

### Query params vs path params vs body

```
GET /api/users/42?include=orders&limit=10
                ^^                          path param — identifies a resource
                   ^^^^^^^^^^^^^^^^^^^^^^   query params — filter, sort, paginate
```

- **Path param** — *which* resource. `/users/42`
- **Query param** — *how* to return it. `?sort=name&page=2&limit=50`
- **Body** — the *data* you're sending. POST/PUT/PATCH only.

**Test all three separately.** Query params are a rich bug source: `?limit=0`, `?limit=-1`, `?limit=999999`, `?page=99999`, `?sort=nonexistentField`, unknown params (ignored or 400?).

---

## Part 2 — Authentication

You cannot test a real API without understanding auth. Four common schemes:

### 1. Basic Auth

```http
Authorization: Basic dGVzdEBleGFtcGxlLmNvbTpwYXNzd29yZA==
```

Base64 of `username:password`. **Base64 is encoding, not encryption** — it's trivially reversible. Only safe over HTTPS. Rare in modern APIs.

### 2. API Key

```http
X-API-Key: sk_live_a8f7d6s5a4f3d2s1
```

Or sometimes `?api_key=...` in the query string. **Keys in query strings are a finding** — they end up in server logs, browser history, and referrer headers.

### 3. Bearer token / JWT

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiI0MiIsInJvbGUiOiJ1c2VyIn0.SflKxw...
```

A JWT has three dot-separated parts: **header.payload.signature**, each base64url-encoded.

**Critical thing to know:** the payload is *readable by anyone* — decode it at jwt.io. It is signed, not encrypted. So:

- Secrets in a JWT payload = a bug worth reporting
- But you *can't* tamper with it — changing the payload breaks the signature

**Tests to run on JWT auth:**

| Test | Expected |
|---|---|
| No `Authorization` header | 401 |
| Malformed token (`Bearer garbage`) | 401 |
| Expired token | 401 |
| Token with a tampered payload | 401 (signature check must fail) |
| Valid token, another user's resource | 403 or 404 |
| Token signed with algorithm `none` | 401 — this is a real historical vuln |

### 4. OAuth 2.0

Multi-step flow where you exchange credentials for a short-lived **access token** plus a **refresh token**. You mostly need to know: get a token, use it, and test that expiry and refresh work.

### The auth tests you should run on *every* protected endpoint

1. **No credentials** → 401
2. **Invalid credentials** → 401
3. **Expired credentials** → 401
4. **Valid credentials, wrong permission level** → 403
5. **Valid credentials, someone else's data** → 403 or 404

That fifth one is the highest-value security test in API QA. It's called **IDOR** (Insecure Direct Object Reference) or a broken-object-level-authorization bug:

> Log in as User A. Note your own record is `/api/users/42`. Now request `/api/users/43` with *your* token. If you can see User B's data — that's a critical vulnerability, and it's extremely common.

Mention IDOR in an interview and you will stand out.

---

## Part 3 — What to actually test

### The happy path checklist (your Round 3 gap)

Given a successful response, verify **all** of these — not just the status code:

- [ ] **Status code** is the *correct* success code (201 for creation, not 200)
- [ ] **Response body schema** — all documented fields present, correct names
- [ ] **Data types** — `id` is a number not a string, `isActive` is boolean not `"true"`
- [ ] **Values match what you sent** — no truncation, no mangled Unicode
- [ ] **No sensitive data leaked** — password, hash, salt, internal IDs, tokens
- [ ] **Headers** — `Content-Type`, `Location` on 201, security headers
- [ ] **Data actually persisted** — follow up with a `GET` and confirm it's really there
- [ ] **Side effects** — did the email get queued? Did the audit log get written?
- [ ] **Response time** within SLA
- [ ] **No extra undocumented fields** — often internal data leaking

> **The single most important habit:** a 2xx means the API *claimed* it worked. Verifying means confirming it *did*. Always read back what you wrote.

### The negative test catalogue

Run this mentally against any endpoint:

**Missing data**
- Omit each required field individually
- Empty body `{}`
- No body at all

**Invalid formats**
- Malformed email, phone, date, URL
- Wrong date format (`26/08/2026` vs `2026-08-26`)
- Invalid enum value (`"status": "banana"`)

**Type violations**
- String where number expected: `"age": "twenty"`
- Number where string expected
- Array where object expected
- `null` in a non-nullable field
- Boolean as `"true"` instead of `true`

**Boundaries** (bring your BVA skills over — they apply identically)
- Field at min length, min−1, min+1
- Field at max length, max+1
- Absurd: 10,000-character name, 10MB payload
- Numeric: `0`, `-1`, `2147483648` (int32 overflow), `1e308`

**State conflicts**
- Create the same resource twice → 409
- Delete the same resource twice → 204 then 404
- Update a resource that was already deleted → 404
- Update with a stale version → 409

**Structural**
- Malformed JSON (trailing comma, unclosed brace) → 400, **never** 500
- Wrong `Content-Type`
- Deeply nested JSON (nesting-bomb / DoS)
- Duplicate keys in the JSON object

**Authorization** — the five tests from Part 2

**Security**
- **Mass assignment** — send `{"role": "admin"}` or `{"isVerified": true}` in a signup body. If the API accepts fields the client shouldn't control, that's privilege escalation.
- **IDOR** — access another user's resource ID with your token
- **SQL injection** — `'; DROP TABLE users;--` in a text field. In a **safe, non-production environment**. You're verifying the input is parameterized and stored as a literal string.
- **XSS payload** — `<script>alert(1)</script>` in a name field. The API may store it happily; the bug appears when the UI renders it. Test that the API sanitizes or escapes on output.
- **Rate limiting** — hammer the endpoint. Is there a 429? Is login brute-forceable?
- **HTTP method tampering** — `DELETE` on a GET-only endpoint → 405
- **Info disclosure** — do errors expose stack traces, SQL, file paths, framework versions?

### The rule that makes negative tests real

**Every negative test asserts a specific status code AND a specific error message.**

Bad: *"it should fail."*
Good: *"returns 409 with body `{"error": "Email already registered"}` and does not create a second user."*

That last clause matters — after a rejected request, verify **nothing changed**.

---

## Part 4 — Tools

### Postman — learn this first

Not because it's the best automation tool, but because it's the fastest way to *understand* APIs, and it's on every job description.

**What to learn, in order:**

1. **Send a request** — method, URL, headers, body (raw → JSON)
2. **Collections** — group related requests; folders per resource
3. **Environments and variables** — `{{baseUrl}}`, `{{authToken}}`. Never hardcode. One collection, switch between dev/staging/prod.
4. **Tests tab** — JavaScript assertions that run on the response:

```javascript
pm.test("Status is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Response time under 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

pm.test("Body has correct shape", function () {
    const body = pm.response.json();
    pm.expect(body).to.have.property("id");
    pm.expect(body.id).to.be.a("number");
    pm.expect(body.email).to.eql("test@example.com");
});

pm.test("Password is not returned", function () {
    const body = pm.response.json();
    pm.expect(body).to.not.have.property("password");
    pm.expect(body).to.not.have.property("passwordHash");
});
```

5. **Pre-request scripts** — generate unique test data:

```javascript
pm.environment.set("uniqueEmail", `test_${Date.now()}@example.com`);
```

6. **Chaining requests** — save a value from one response for the next:

```javascript
// In the login request's Tests tab
const body = pm.response.json();
pm.environment.set("authToken", body.token);
pm.environment.set("userId", body.user.id);
// Then use {{authToken}} in later requests
```

7. **Collection Runner** — run the whole collection in sequence
8. **Newman** — the CLI runner. This is how Postman collections get into CI:

```bash
newman run MyCollection.json -e staging.postman_environment.json --reporters cli,junit
```

**Alternatives worth knowing by name:** Bruno (open source, git-friendly), Insomnia, Hoppscotch, `curl` for quick one-offs.

### Automation frameworks — pick one and go deep

**If you know Java (Selenium background):** **REST Assured**

```java
given()
    .contentType(ContentType.JSON)
    .header("Authorization", "Bearer " + token)
    .body(newUserPayload)
.when()
    .post("/api/users")
.then()
    .statusCode(201)
    .header("Location", notNullValue())
    .body("id", notNullValue())
    .body("email", equalTo("test@example.com"))
    .body("$", not(hasKey("password")))
    .time(lessThan(500L));
```

The `given/when/then` structure maps naturally onto how you already think about test steps.

**If you know JavaScript/TypeScript (Playwright background):** **Playwright's `APIRequestContext`** — you already have it installed

```javascript
test('creates a user', async ({ request }) => {
  const response = await request.post('/api/users', {
    data: {
      email: `test_${Date.now()}@example.com`,
      password: 'Str0ngP@ss'
    }
  });

  expect(response.status()).toBe(201);
  const body = await response.json();
  expect(body).toHaveProperty('id');
  expect(body).not.toHaveProperty('password');
});
```

**This is the highest-leverage option for you.** Same runner, same assertions, same reporter as your UI tests — and it unlocks the hybrid pattern below.

Alternatives: `supertest` (Node), `pytest` + `requests` (Python), Karate (BDD, low-code).

### The hybrid pattern — where API skills pay off in UI testing

This is the thing to bring up in an interview, because it connects your existing strength to your new skill:

```javascript
test('user sees their new project in the list', async ({ page, request }) => {
  // SETUP via API — fast, reliable, not what we're testing
  const project = await request.post('/api/projects', {
    data: { name: `Proj_${Date.now()}` }
  });
  const { id, name } = await project.json();

  // TEST via UI — this is the actual thing under test
  await page.goto('/projects');
  await expect(page.getByText(name)).toBeVisible();

  // TEARDOWN via API — guaranteed, fast
  await request.delete(`/api/projects/${id}`);
});
```

**Why this is better than clicking through the UI to create the project:**

- Setup takes 80ms instead of 8 seconds
- The test doesn't fail because the *creation* form broke — it isolates one behavior
- Teardown runs via API even if the UI assertion failed
- Solves the Round 3 idempotency problem cleanly

Say this in an interview: *"I use API calls for setup and teardown so UI tests only exercise the thing they're actually asserting on."* That's a mid-level answer.

---

## Part 5 — Automation practices

### Test independence

**Every test must:**
1. Create the data it needs
2. Not depend on any other test having run
3. Clean up after itself
4. Pass when run alone, in any order, and in parallel

**Unique data generation** — never hardcode:

```javascript
const email = `test_${Date.now()}@example.com`;
const email = `test_${crypto.randomUUID()}@example.com`;
```

**Teardown that always runs:**

```javascript
// Playwright
test.afterEach(async ({ request }) => {
  if (createdId) await request.delete(`/api/projects/${createdId}`);
});
```

```java
// JUnit
@AfterEach
void cleanup() { /* delete created resources */ }
```

Cleanup as the last line of the test body **does not run when an assertion throws**. Use the hook.

### Structure

```
tests/
  api/
    users/
      create-user.spec.js
      get-user.spec.js
      delete-user.spec.js
    auth/
      login.spec.js
  helpers/
    api-client.js        ← wraps auth, base URL, common headers
    data-factory.js      ← builds valid payloads with unique values
  fixtures/
    users.json
config/
  dev.env
  staging.env
```

**An API client wrapper** is the API equivalent of a Page Object — same reasoning. Endpoint paths and auth handling live in one place, so a route change is one edit:

```javascript
class UserApi {
  constructor(request, token) { this.request = request; this.token = token; }

  async create(overrides = {}) {
    return this.request.post('/api/users', {
      headers: { Authorization: `Bearer ${this.token}` },
      data: { email: `u_${Date.now()}@test.com`, password: 'Str0ngP@ss', ...overrides }
    });
  }

  async getById(id) { /* ... */ }
  async delete(id) { /* ... */ }
}
```

**And the same rule as POM: no assertions inside the client.** The client returns responses; the test makes claims.

### Config and secrets

- Base URLs in environment config, **never** hardcoded
- Credentials and tokens in **environment variables**, never committed
- One suite, runnable against dev/staging/prod by swapping config

### Schema validation

Rather than asserting field by field, validate the whole response against a **JSON Schema**. This catches type drift and removed fields automatically:

```javascript
const schema = {
  type: 'object',
  required: ['id', 'email', 'createdAt'],
  properties: {
    id: { type: 'number' },
    email: { type: 'string', format: 'email' },
    createdAt: { type: 'string' }
  },
  additionalProperties: false   // catches leaked fields
};
```

`additionalProperties: false` is how you automatically detect a leaked `passwordHash`.

### Mocking and stubbing

Know the vocabulary:

- **Mock server** — fake API returning canned responses. Lets you test the frontend before the backend exists, and lets you simulate a 500 on demand.
- **Stub** — hardcoded response for one call
- **Service virtualization** — simulating a third-party dependency (payment gateway, SMS provider) you can't hit in tests
- **Contract testing** — verifying that consumer and provider agree on the contract. Tool name: **Pact**.

Why it matters: you often *can't* trigger a real 503 from a payment provider. You mock it to test your app's error handling.

### CI integration

API tests belong in the pipeline on every commit — they're fast enough:

```
commit → unit tests → API tests → deploy to staging → UI smoke tests
```

Output must be machine-readable (JUnit XML, Allure) so the CI can display failures.

---

## Part 6 — Interview-ready answers

**"How do you test an API?"**

> Start with the documentation or contract — I need to know the expected status codes, request and response schemas, and auth model. Then I verify the happy path completely: correct status code, response schema and data types, values matching what I sent, no sensitive data leaked, and that the change actually persisted by reading it back with a GET. Then negative testing: missing required fields, wrong types, boundary values, malformed JSON, and duplicate creation. Then authorization — no token, expired token, valid token with insufficient permissions, and accessing another user's resource by ID. I'd automate the whole set and run it in CI on every commit.

**"What's the difference between 401 and 403?"**

> 401 means unauthenticated — the server doesn't know who you are, so no token or a bad token. 403 means authenticated but not authorized — valid credentials, insufficient permission. In practice: no token gets 401, and a regular user trying to hit an admin endpoint gets 403.

**"Why test the API when you already have UI tests?"**

> Speed and isolation. API tests run in milliseconds instead of seconds, they don't break when CSS changes, and when one fails I know it's the backend rather than having to debug which layer broke. They also let me test error handling that's hard to trigger through a UI, and I can start before the frontend exists. I still use API calls for setup and teardown in UI tests so those only exercise the one behavior they're asserting.

**"What's the most serious bug you'd look for in an API?"**

> Broken object-level authorization — IDOR. If I'm logged in as user 42 and I can request `/api/users/43` with my own token and get someone else's data back, that's critical and it's common. Right behind it: mass assignment, where I send a field like `"role": "admin"` in a signup body and the API accepts it.

**"How do you handle test data in API automation?"**

> Every test generates its own unique data — timestamps or UUIDs in emails and names, never hardcoded values — and cleans up in a teardown hook so it runs even when an assertion fails. Tests must pass when run alone, in any order, and in parallel. If a test only passes on a fresh database, it isn't finished.

---

## Part 7 — Practice plan (2 weeks)

Real, free, no-signup APIs you can hit right now:

| API | URL | Good for |
|---|---|---|
| JSONPlaceholder | `jsonplaceholder.typicode.com` | Basic CRUD, first steps |
| ReqRes | `reqres.in` | Auth flows, delayed responses, pagination |
| Restful-Booker | `restful-booker.herokuapp.com` | **Best for practice** — real auth, real bugs |
| The Cat API | `thecatapi.com` | API keys, query params |
| httpbin | `httpbin.org` | Inspecting your own requests, testing any status code |
| Swagger Petstore | `petstore.swagger.io` | Reading OpenAPI docs |

**Restful-Booker is the one to focus on.** It has a real token-based auth flow, full CRUD on bookings, and — deliberately — actual bugs to find. Its docs are at `restful-booker.herokuapp.com/apidoc`.

### Week 1 — Understand

- **Day 1–2:** Memorize the status code tables in Part 1. Write them from memory until you can do it cold. Do the same for method idempotency.
- **Day 3:** Install Postman. Hit every JSONPlaceholder endpoint. GET, POST, PUT, PATCH, DELETE. Watch what comes back.
- **Day 4:** Restful-Booker. Create a token via `POST /auth`, then do authenticated CRUD on a booking. Chain the token with an environment variable.
- **Day 5:** Add assertions in the Tests tab. Status code, schema, types, response time, no leaked fields.
- **Day 6–7:** Break things deliberately. Malformed JSON, missing fields, wrong types, out-of-range values, no auth, someone else's booking ID. **Write down every finding as a proper bug report** — you'll be building interview material.

### Week 2 — Automate

- **Day 8–9:** Build a Postman collection with environments and a full test suite. Run it with Newman.
- **Day 10–12:** Rewrite the same suite in code — Playwright's `request` fixture if you're on JS, REST Assured if you're on Java. Add an API client wrapper and a data factory.
- **Day 13:** Add proper setup/teardown with unique data. Run the suite 20 times in a row. It must pass every time.
- **Day 14:** Build one hybrid test — API setup, UI assertion, API teardown. This is your portfolio piece.

### What "done" looks like

You can explain, without notes:
- Every status code in Part 1 and when to use it
- Which methods are idempotent and why
- 401 vs 403, 400 vs 422, 4xx vs 5xx
- The full happy-path verification checklist
- IDOR and mass assignment
- Why tests need unique data and guaranteed teardown

And you have a **public repo** with an API test suite that runs clean 20 times in a row. That repo is worth more in an interview than any certificate.

---

## Quick reference card

```
IDEMPOTENT:  GET, PUT, DELETE, HEAD, OPTIONS
NOT:         POST, (PATCH usually)
SAFE:        GET, HEAD, OPTIONS

201 = created (POST)       204 = success, no body (DELETE)
400 = can't parse it       422 = parsed it, it's invalid
401 = who are you?         403 = I know you, no
404 = doesn't exist        405 = wrong verb
409 = conflict/duplicate   429 = slow down
500 = server crashed  ← always a bug

EVERY endpoint:  no token → 401
                 wrong role → 403
                 other user's ID → 403/404  ← IDOR
                 malformed JSON → 400, never 500

EVERY success:   right code, right schema, right types,
                 no secrets leaked, GET it back to confirm

EVERY test:      unique data in, guaranteed cleanup out,
                 passes alone / any order / in parallel
```