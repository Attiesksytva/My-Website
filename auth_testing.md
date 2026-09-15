# Auth Testing Playbook — Atties

## Stack
FastAPI + MongoDB (motor), JWT access token (60 min) + refresh token (7 days) as httpOnly cookies (`samesite=none`, `secure`). bcrypt password hashing. Brute-force lockout: 5 failed attempts per `ip:email` = 15 min lock.

## Endpoints
- POST /api/auth/register {name, email, password(min 8)} → 201 + user, sets cookies
- POST /api/auth/login {email, password} → user + cookies (401 invalid, 429 locked)
- POST /api/auth/logout → clears cookies
- GET /api/auth/me → current user (cookie or Bearer)
- POST /api/auth/refresh → new access cookie from refresh cookie
- GET /api/enquiries → admin only
- GET /api/enquiries/mine → logged-in customer (matches user_id or email)
- PATCH /api/enquiries/{enquiry_id} {status: new|contacted|preview-sent|in-build|launched} → admin only
- POST /api/enquiries → public; links to user when cookie present or email matches an account

## MongoDB verification
```
mongosh
use test_database
db.users.find({role: "admin"}).pretty()   # password_hash starts with $2b$
db.users.getIndexes()                     # unique index on email
```

## API testing (cookies)
```
curl -c /tmp/c.txt -X POST $API/api/auth/login -H "Content-Type: application/json" -d '{"email":"...","password":"..."}'
curl -b /tmp/c.txt $API/api/auth/me
```

## Frontend routes
/login, /signup, /dashboard (customer, protected), /admin (admin only, protected). Admin login redirects to /admin; customer to /dashboard.
