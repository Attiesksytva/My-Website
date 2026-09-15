# Test Credentials
# Agent writes here when creating/modifying auth credentials (admin accounts, test users).
# Testing agent reads this before auth tests. Fork/continuation agents read on startup.

## Atties — Auth accounts

### Admin (real — owner's account)
- Email: attieswebdesign@gmail.com
- Password: millionare8720
- Role: admin → lands on /admin (all enquiries dashboard)
- Seeded automatically on backend startup from ADMIN_EMAIL/ADMIN_PASSWORD in backend/.env

### Test customer (created for testing — reseed anytime)
- Email: customer.test@example.com
- Password: newpassword456 (changed during password-reset testing)
- Role: customer → lands on /dashboard

### Test customer 2 (created via UI signup)
- Email: demo.client@example.com
- Password: demopass123
- Role: customer

## Endpoints
- POST /api/auth/register {name, email, password(min 8)}
- POST /api/auth/login {email, password} (httpOnly JWT cookies; 5 fails = 15 min lockout)
- GET /api/auth/me · POST /api/auth/logout · POST /api/auth/refresh
- GET /api/enquiries (admin only) · GET /api/enquiries/mine (customer)
- POST /api/auth/forgot-password {email} → emails 1-hour single-use reset link
- POST /api/auth/reset-password {token, password} → updates password, invalidates token
- PATCH /api/enquiries/{enquiry_id} {status} (admin only)
- POST /api/enquiries (public contact/booking form; email optional)

## Notes
- No raw email/phone shown on the public site by design (privacy requirement).
- Owner notification inbox: attieswebdesign@gmail.com (passes email gateway).
- For email-send tests use delivered@resend.dev as the recipient.
