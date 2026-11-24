# Push this project to GitHub manually

This ZIP is a clean, GitHub-ready copy of **GSA — Global Sports Academy**:

- Full source (React + Vite frontend, Express API, Capacitor Android project)
- `README.md` with setup, logins, themes, contact details and APK notes
- Both APK copies: `apk/GSA-Academy.apk` and `public/GSA-Academy.apk`
- `.gitignore` excluding `node_modules`, `dist`, SQLite databases, Android build output and local SDK config

Regenerable caches, the local database and Android build folders are intentionally omitted; `npm install`, `npm run build` and `npm run apk` recreate them.

## Steps

1. Extract `gsa-academy-github.zip` into an empty folder.
2. Open a terminal in the extracted `gsa-academy` folder.
3. Initialise and commit:

   ```bash
   git init
   git add .
   git commit -m "Initial GSA Academy release"
   ```

4. Create an **empty public repository** on GitHub. Do **not** add a README, license or `.gitignore` in GitHub, or the first push may conflict.
5. Copy the repository URL from GitHub, then run:

   ```bash
   git remote add origin https://github.com/<YOUR_USERNAME>/<YOUR_REPO>.git
   git branch -M main
   git push -u origin main
   ```

   Git Credential Manager will ask for your GitHub sign-in the first time.

6. Confirm on GitHub that these files exist:

   ```text
   README.md
   apk/GSA-Academy.apk
   public/GSA-Academy.apk
   src/
   server/
   android/
   ```

## Security note for a public repo

The demo owner login (`owner` / `9042999507`), staff/student demo passwords and the development JWT secret are visible in the source because they were required for this build. Before exposing a real deployment, move credentials to secrets/environment variables, set `GSA_JWT_SECRET`, change demo passwords, enforce HTTPS and add rate limiting. See the security note in `README.md`.

## After pushing

To run from a fresh clone:

```bash
npm install
npm run dev
# Web: http://localhost:5174
# API: http://localhost:4000/api/health
```

To rebuild the Android APK after frontend changes:

```bash
npm run apk
Copy-Item android\app\build\outputs\apk\debug\app-debug.apk apk\GSA-Academy.apk
Copy-Item apk\GSA-Academy.apk public\GSA-Academy.apk
```
