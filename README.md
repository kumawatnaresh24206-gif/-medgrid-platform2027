# MedGrid — Multi-Lab Website Builder & Management Platform

MedGrid is a production-ready, multi-tenant Diagnostic Laboratory Website Builder & SaaS Management Platform built with React, TypeScript, Tailwind CSS, Express, and SQLite (`node:sqlite`).

## Core Architecture

1. **Super Admin Console (`/super-admin`)**:
   - Create and sell independent diagnostic laboratory websites (`/lab/:slug`).
   - Manage subscriptions, selling prices, partial/full payments, renewal reminders, and automated expiry suspension.
   - Generate PDF invoices, download full JSON database backups, and inspect security audit logs.
   - Configure SMTP email credentials and Official Meta WhatsApp Business Cloud API / Twilio credentials securely on the server.
   - Monitor actual email and WhatsApp delivery statuses (`sent`, `failed`, `unconfigured`) with provider responses.

2. **Lab Owner Admin Panel (`/lab-admin`)**:
   - Strictly tenant-isolated API endpoints (`req.auth.labId`) preventing cross-lab data access.
   - Manage test categories (English & Hindi), diagnostic tests, health packages, preparation instructions, and pricing.
   - Manage patient appointments (Accept/Confirm, Sample Collected, Report Ready, Complete, Reject, Cancel).
   - Upload laboratory logo, customize tagline/contact/WhatsApp details, generate scannable QR codes, download PDF receipts, and export Excel (`.xlsx`) reports.

3. **Public Patient Test Booking Websites (`/lab/:slug`)**:
   - Independent branded laboratory websites (e.g. `/lab/healthcare`, `/lab/modern-diagnostics`, `/lab/care-lab`).
   - Instant test search, category tabs, preparation details, Home Sample Collection vs Lab Visit selector, and no-login appointment booking.
   - Auto-generated unique Booking ID (`MG-XXXX-XXXXX`), downloadable PDF receipt, prominent WhatsApp integration, and booking status lookup/cancellation.
   - Full Light/Dark mode and English/Hindi (`EN` / `हिन्दी`) language support.

## Demo Credentials (Pre-Seeded Sample Data)

All demonstration laboratories, tests, and bookings are flagged `is_demo = 1` and separated from newly created live production records (`is_demo = 0`).

- **Super Admin**: `admin@medgrid.io` / `SuperAdmin@2026!`
- **HealthCare Diagnostics (Lab Owner)**: `rajesh@healthcare-demo.in` / `LabOwner@2026!`
- **Modern Diagnostics (Lab Owner)**: `anita@modern-demo.in` / `LabOwner@2026!`
- **Care Lab (Lab Owner)**: `vikram@carelab-demo.in` / `LabOwner@2026!`

## Environment Variables (`.env`)

See `.env.example` for all supported variables:

| Variable | Description |
|---|---|
| `APP_URL` | Public URL of the deployed platform (used in invite links and QR codes) |
| `SESSION_SECRET` | Random 32+ char secret for HS256 JWT session cookies |
| `SUPER_ADMIN_EMAIL` | Default Super Admin email seeded on first boot |
| `SUPER_ADMIN_PASSWORD` | Default Super Admin password seeded on first boot |
| `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `SMTP_FROM` | Optional default SMTP credentials (also configurable in Super Admin UI) |
| `WHATSAPP_PHONE_NUMBER_ID`, `WHATSAPP_ACCESS_TOKEN` | Optional Meta WhatsApp Business Cloud API credentials (also configurable in Super Admin UI) |
| `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_WHATSAPP_FROM` | Optional Twilio WhatsApp credentials |

## Local Development & Production Deployment

```bash
# 1. Install dependencies
npm install

# 2. Start full-stack dev server on port 3000
npm run dev

# 3. Type-check and build frontend bundle
npm run lint
npm run build

# 4. Run production server on port 3000
npm start
```

Data is persisted locally in `data/medgrid.sqlite`. Ensure the `data/` directory is backed up or mounted on a persistent volume in containerized deployments (Render Disk, Railway Volume, Docker, VPS).

## Using with Cloudflare (Important)

- **Why Cloudflare Pages / Workers alone won't run this app:** Cloudflare Pages only serves static frontend files (`dist/`) and edge Workers lack persistent filesystem (`fs`), `node:sqlite`, and raw TCP sockets required by `nodemailer` SMTP.
- **Recommended Cloudflare Setup:** Deploy this Node.js app on **Render** (using the included `render.yaml`), **Railway**, **Docker** (using the included `Dockerfile`), or any **VPS**, then point your **Cloudflare DNS** (`CNAME` / `A` record with Cloudflare Proxy enabled) to your Node server URL. This gives you Cloudflare SSL, CDN, and custom domains with full backend + SQLite + Email + WhatsApp support.

