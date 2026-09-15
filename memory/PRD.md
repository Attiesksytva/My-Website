# PRD — Atties (AI Web Design Studio Landing Page)

## Original Problem Statement
Build a website for an AI web designing startup named "Atties" (logo provided as attachment), styled like mindmarket.com. Include: a 4-step process section (1. Brief — talk about client needs, 2. Generate — AI generates the website, 3. Rehearse — find & fix errors, 4. Launch), a purchase plan section (₹10,000–₹15,000) where clients pay and work begins, and a contact section with email (samakung008@gmail.com), WhatsApp and business number (+917078415026) so clients can book appointments. User later added: WhatsApp business link https://wa.me/918923188447 for direct messaging.

## User Choices (confirmed)
- Purchase plan flow: client clicks a plan → fills short booking form → owner gets the enquiry (no payment gateway)
- Contact submissions: saved in DB + email notification (Emergent-managed Resend)
- Logo: used as-is (cropped transparent mark derived for navbar/footer/hero)
- Art direction: bold neo-brutalist "electric neon" (void black / volt yellow #FACC15 / pulse pink #E879F9), MindMarket-inspired structure, Unbounded + JetBrains Mono

## User Personas
- Small/business owners in India who need a professional website fast, at a fixed low price
- Non-technical clients who prefer WhatsApp/phone over email

## Architecture
- Frontend: React (CRA + craco), Tailwind, framer-motion (masked hero reveal, scroll reveals, parallax), lenis smooth scroll, shadcn Dialog/Select, sonner toasts
- Backend: FastAPI (`/api` prefix), MongoDB (motor) — `enquiries` collection
- Email: Emergent-managed Resend proxy (httpx async, guardrail gate `_assert_safe_email`), owner notification + client confirmation templates
- Assets: `/app/frontend/public/assets/atties-logo.png` (original), `atties-logo-mark.png` (cropped, transparent)

## Implemented (2026-08-26)
- Sticky navbar with logo, anchor links, mobile menu, "Start a project" CTA
- Kinetic hero: masked line-by-line reveal, parallax neon background, floating logo, stats strip
- Slow editorial marquee (Brief ✦ Generate ✦ Rehearse ✦ Launch)
- Process section: asymmetric Tetris grid, 4 numbered steps (01 Brief / 02 Generate / 03 Rehearse / 04 Launch)
- Pricing: 3 plans — Launch Pad ₹10,000 / Signature ₹12,500 (featured, rotating beam border) / Empire ₹15,000
- Booking dialog: name, phone, email, plan selector (pre-fills from plan button), project brief → POST /api/enquiries
- Backend: POST /api/enquiries validates + saves to MongoDB, sends owner notification + client confirmation email
- Contact section: email card, WhatsApp card (wa.me/918923188447), business line +91 70784 15026, appointment CTA
- Footer with logo and contact details; favicon + title + meta updated

## Updates (2026-08-26, round 2)
- Pricing reduced to a single plan: Launch Pad ₹10,000, marked "Most booked", delivery "Live within 7 days"
- Removed business line card and +91 70784 15026 from contact section, footer, and client confirmation email
- Added Reviews section ("Word on the street") with empty state — real reviews to be added later

## Updates (2026-08-26, round 3)
- Contact section replaced with a two-column secure form: left = heading "Ready to launch your restaurant or business site?" + 24h trust text; right = form (Full Name, Business Name, Business WhatsApp Number, description) with "Get Free AI Website Preview" submit
- Owner email changed to attieswebdesign@gmail.com (passes email gateway — notifications now deliver, verified email_sent=true)
- No raw email address or phone number anywhere in public frontend code (verified by grep); footer stripped of contact details
- Enquiry model: email now optional (contact form has no email field); business_name field added; owner notification email includes business + WhatsApp number

## Updates (2026-08-26, round 4)
- Email + password auth added (JWT httpOnly cookies, bcrypt, 5-attempt brute-force lockout)
- Admin account seeded: attieswebdesign@gmail.com (role=admin) → /admin dashboard lists ALL enquiries with status management (new → contacted → preview-sent → in-build → launched)
- Customer accounts: /signup + /login → /dashboard shows the customer's own requests with live status (enquiries auto-link by session or matching email)
- Navbar now has Login/Dashboard link; protected routes via ProtectedRoute; testing playbook at /app/auth_testing.md

## Updates (2026-09-01, round 5)
- Forgot-password flow: /forgot-password emails a single-use 1-hour reset link → /reset-password?token= sets new password; token reuse/expiry blocked; "Forgot password?" link on login page
- Contact form: WhatsApp/phone field removed (backend phone now optional); form is Name + Business Name + description only

## Updates (2026-09-01, round 6)
- Spam shield on enquiry endpoints: hidden honeypot field (bots get fake success, never saved/emailed), time-trap (submissions under 2.5s rejected), per-IP rate limit (5 per 10 min, uses X-Forwarded-For)
- Rate limit applies to both contact form and booking dialog (both send elapsed_ms)

## Known Limitations
- Owner notification email to samakung008@gmail.com is blocked by the email gateway's deliverability filter in the preview environment (returns "undeliverable recipient"). Client confirmation emails send fine (verified 202). Enquiries are always saved in MongoDB regardless. Re-test owner email after deployment.
- No admin dashboard to view saved enquiries yet (data is in MongoDB `enquiries` collection)
- No real payment collection (by user choice — booking enquiry only)

## Backlog (P0/P1/P2)
- P0: Verify owner email delivery post-deployment or swap OWNER_EMAIL if gmail address stays blocked
- P1: Admin page to view/manage enquiries
- P1: Razorpay payment collection for the 50% advance
- P2: Portfolio/work gallery section
- P2: Testimonials carousel
- P2: Blog/SEO content pages

## Next Tasks
1. Re-test email delivery to owner after deploy
2. Add enquiries admin view
3. Optional Razorpay integration
