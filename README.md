# GSA — Global Sports Academy

A full-stack, motion-led website for **Global Sports Academy (GSA)** with three role-based portals — Owner, Staff and Student — a live announcement board, biometric-style attendance simulation, fee/billing and finance tracking, equipment stock, achievements, role-based access control, and a multilingual (English / Tamil / Hindi) assistant that can generate PDF and Excel reports.

The existing developer portfolio in the parent folder was left untouched; this is a separate Vite + Express application.

---

## Run locally

```bash
cd gsa-academy
npm install
npm run dev
```

- Web: http://localhost:5174
- API: http://localhost:4000/api/health
- Vite proxies `/api` to the Express server.

Production build (Express serves the built site):

```bash
npm run build
npm start
# open http://localhost:4000
```

## Android APK

The ready-to-install APK is at:

```text
gsa-academy/apk/GSA-Academy.apk
```

Install it on an Android phone (Android 5.1 / API 22 or newer), then:

1. Keep the computer running with `npm run dev` (or `npm start` after `npm run build`).
2. Connect the phone and computer to the **same Wi-Fi**.
3. Allow the PC firewall to accept port **4000** (private networks).
4. Open the app and sign in with the demo accounts below.

The APK has a **phone-first layout**: a five-button bottom navigation bar in every portal, compact top bars, safe-area padding for notched phones, full-screen chat sheet, keyboard-aware forms, and single-column cards/tables.

The backend address is baked into the APK as `http://10.161.243.49:4000` (the Wi-Fi IP of the build machine). If your computer's IP changes, open the **login screen → "Server: … – change"** and enter the new address such as `http://192.168.1.25:4000`, then tap **Save & reload**. This setting is stored on the phone.

To rebuild the APK after any frontend change:

```bash
npm run apk
# output: android/app/build/outputs/apk/debug/app-debug.apk
# copy it to apk/GSA-Academy.apk if you want the stable location
```

This is a debug-signed APK (easy to install and test). For Play Store distribution, generate a release keystore, build a signed release APK/AAB, deploy the backend to a public HTTPS host, and replace the LAN address in `public/gsa-config.js`.

SQLite data is created automatically on first boot at `data/gsa.db` and seeded with realistic demo data (14 athletes, 6 coaches, 8 sports, medals, fees, income, stock, attendance, announcements and notifications). Delete `data/gsa.db` to regenerate the seed.

---

## Sign in

| Portal | Username / email | Password |
| --- | --- | --- |
| Owner | `owner` (exact username, no other username is accepted) | `9042999507` |
| Staff | `ravi.kumar@gsa.ac.in` | `Coach@123` |
| Student | `player1@gsa.ac.in` | `Player@123` |

Email is the username for staff and students. Passwords must contain 8+ characters with upper case, lower case, a number and a special character; the login form has a show/hide toggle and a full forgot-password (demo code) flow.

> **Security note:** the owner credential above is hard-coded in the API because it was explicitly requested. For a public deployment, move it to a secret store / database with a hash, set `GSA_JWT_SECRET`, enforce HTTPS, add rate limiting and change all demo passwords.

---

## What is included

### Public website
- Mobile-first public site modelled on `globalsportsacademy.in`: admissions bar, compact nav, single-column cards on phones, age programmes, competition pathway, app feature grid, gallery, FAQ and enquiry form.
- Animated hero with orbiting rings, floating chips, scroll-linked motion, a liquid-glass orb and refracted gradient headings.
- ReactDrops-style motion: flowing topographic background, liquid-glass logo blobs, chromatic buttons and an animated ambient orb.
- Three animated themes — **Lavender / Blue**, **Orange** and **Professional White** (clean white/slate/blue) — with glassmorphism surfaces throughout; the choice persists locally and the toggle cycles through all three.
- Sports catalogue with all eight disciplines, their types and Fencing as the flagship programme.
- Mission and pathway sections: classes 10–12 → district/state/national medals → sports-quota admissions (including Anna University and top private colleges) → government jobs with sports-reserved seats.
- Facilities: 24/7 CCTV with 360° view, biometric attendance, professional equipment and safety, athlete-only strength and conditioning zone.
- Contact: phone, WhatsApp, email, academy address, trial enquiry form (opens WhatsApp), exact Google Maps link (`https://maps.app.goo.gl/diXTb2Q2biaQ7ePq6`) and a coordinate-accurate embedded map; FAQ and footer portals.
- Android APK is served from `/GSA-Academy.apk` for direct download from the app section.

### Owner portal
- Executive dashboard: KPI cards, 12-month income/expense chart, income-source split, sport performance, low-stock and fee alerts, recent medals, announcement board.
- Full CRUD for students, staff, sports, fees, income, stock, equipment transactions, announcements and achievements.
- Student records include identity, family, address, academics, school/college, sport, coach, medical history, damage, equipment, pending fees, remarks and prize-photo links.
- Finance: fee collection (scanner/cash entry), entry fees, cash-prize entries, equipment sales, expenses by sport, monthly/annual growth.
- Equipment stock: per-sport items, reorder alerts, purchase/sale log and amount earned.
- Reports & exports: PDF and Excel for students, staff, fees, income, stock, attendance, achievements and announcements.
- Role-based access matrix: per-module **view** / **edit** toggles for every staff member; changes apply immediately to the staff API and assistant.

### Staff portal
- Coach dashboard with assigned athletes, medal count and attendance for the day.
- View-only student records, filtered server-side by the coach's sports, assigned students and owner permissions.
- Attendance recording/scanner (only with `attendance` edit permission), announcements, achievement records, leave approvals and queries.
- Assistant replies are scoped by the same permission map — a coach cannot ask it for finance data they cannot see.

### Student portal
- Athlete passport with GSA ID, sport, coach, years, school/college and progress.
- Medals with level/category, match details and downloadable prize photos.
- Biometric attendance history, fee bills and dues, leave requests, announcements and notifications.
- Queries, suggestions and help; multilingual assistant with voice output.

### Assistant
- Rule-based multilingual intents for fees, attendance, medals, stock, finance, announcements, counts, reports and help.
- Field-aware answers: asking for `student name, sport and coach` returns only those three columns in a compact table, not the whole database.
- Value filtering: `Aarav Sharma sport and coach` or `Fencing students name and coach` narrows the rows as well as the columns.
- Real Tamil and Hindi response strings; browser speech synthesis (`ta-IN`, `hi-IN`, `en-IN`) and speech recognition where supported.
- Report requests return an inline **Download** button; files are produced server-side with role-scoped data and carry the same field/row filters used in the chat.

---

## Architecture

```
gsa-academy/
├── server/
│   ├── index.js      Express app, static hosting, SPA fallback
│   ├── db.js         SQLite schema + deterministic demo seed
│   ├── auth.js       scrypt password hashing, HMAC tokens, RBAC middleware
│   └── routes.js     68 REST endpoints, dashboards, chatbot, exports
├── src/
│   ├── components/   Background, chrome, layout, tables, chatbot, UI kit
│   ├── context/      Auth, theme, notifications, dashboard state
│   ├── pages/public/ Home + Login
│   ├── pages/dash/   Owner, shared operations and role pages
│   ├── lib/api.js    Fetch wrapper, downloads, formatting helpers
│   └── styles.css    Design system (lavender/blue, orange, professional white; CSS blur, motion)
├── data/             SQLite database (generated)
└── dist/             Production build (generated)
```

Authentication uses signed bearer tokens stored in `localStorage`; all list endpoints are scoped in SQL by role, assigned sport/coach and permission flags. Mutating endpoints are owner-only unless explicitly allowed. Passwords use `scrypt` with a per-user salt.

---

## Demo / integration boundaries

- **CCTV and 360° view** are described and represented in the UI; live camera streaming requires real cameras and a streaming service.
- **Biometric device and fee scanner** are simulated through `POST /api/attendance/scan` (student code `GSA-1001` or phone) and `POST /api/fees/pay`. Attendance and fee events create real notifications in the website. Mobile push requires a native app with FCM/APNs.
- Forgot-password codes are returned in the UI demo (no SMTP is configured).
- The owner credential and demo passwords are for demonstration only — see the security note above.

---

## Contact

Phone / WhatsApp: **9042999507** · Email: **globalsportsnkl@gmail.com** · Google Maps: https://maps.app.goo.gl/diXTb2Q2biaQ7ePq6 · Training zone access: students currently training at GSA only.
