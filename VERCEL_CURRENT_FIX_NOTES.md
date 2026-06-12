# Vercel deployment fixes applied

This package fixes the build/deployment issues from the uploaded `biotech-agro (1).zip` project.

## Main fixes

1. Fixed TypeScript duplicate object keys in `src/App.tsx`.
   - The Arabic and French fallback objects had duplicate `title` and `subtitle` keys.
   - This caused Vercel/TypeScript build errors and also made translations choose the wrong text.

2. Changed the Vercel build script back to a static Vite build.
   - `package.json` now uses `"build": "vite build"`.
   - The old script tried to bundle `server.ts` as CommonJS and caused `import.meta`/server issues.

3. Added Vercel API functions.
   - Added `api/[...path].ts` and `api/seed-db.ts` so `/api/content`, `/api/auth/login`, `/api/messages`, products, services, and admin routes work on Vercel.

4. Restored a valid database seed.
   - The uploaded project had `src/db/db.json` as an empty file.
   - That made `/api/content` fail, which caused missing text, products, services, and images.

5. Fixed production image paths.
   - Copied `src/assets/images/*` to `public/assets/images/*`.
   - Replaced `/src/assets/images/...` with `/assets/images/...` where needed.

6. Added `vercel.json`.
   - Vercel is configured for Vite with `dist` as output.

## Vercel settings

Use:

- Framework Preset: Vite
- Build Command: npm run build
- Output Directory: dist
- Node.js Version: 22.x

## Admin login on Vercel

For stable production login, add these Vercel environment variables:

- SESSION_SECRET=make_a_long_random_secret_here
- ADMIN_USERNAME=admin
- ADMIN_PASSWORD=your_strong_password_here
- ADMIN_EMAIL=biotechagro.digital@gmail.com
- RESET_CODE_SCREEN_FALLBACK=true

Then redeploy.

## Important storage note

This fixed project restores the JSON data and makes the public site load correctly, but admin edits are still temporary on Vercel unless you move the database/media to Vercel Blob, Supabase, or another permanent storage service.
