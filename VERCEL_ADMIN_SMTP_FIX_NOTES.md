# Vercel Admin Login + SMTP Reset Fix Notes

## What changed

1. The public website now has a bundled fallback copy of `src/db/db.json`.
   - If `/api/content` fails on Vercel, the website still renders text, products, services and images instead of looking empty/broken.

2. Admin login is more reliable on Vercel.
   - It accepts `ADMIN_USERNAME`, `admin`, or the configured `ADMIN_EMAIL` as the username.
   - It accepts `ADMIN_PASSWORD` from Vercel Environment Variables as the most reliable permanent password.
   - Default `admin / admin` still works for first deployment if no custom password is configured.

3. Password reset no longer depends only on `/tmp` temporary storage.
   - The reset request creates a signed reset token that the browser sends back with the reset code.
   - This prevents reset failure when Vercel sends the request-code and verify-code calls to different serverless instances.

4. Gmail SMTP configuration is safer.
   - Spaces are automatically removed from Gmail App Passwords.
   - The API logs masked SMTP diagnostics, not the secret password.

5. Added a safe diagnostic endpoint:
   - `/api/health`
   - It shows whether API routes work, whether admin env password is visible, whether SMTP is configured, product/service counts, and runtime.
   - It does not expose passwords or secret values.

## Vercel environment variables to add

Add these in Vercel Dashboard > Your Project > Settings > Environment Variables.
Apply them to Production. Also add to Preview if you test preview deployments.

ADMIN_USERNAME=admin
ADMIN_PASSWORD=your_strong_admin_password
ADMIN_EMAIL=biotechagro.digital@gmail.com
SESSION_SECRET=your_long_random_secret_at_least_32_chars
RESET_CODE_SCREEN_FALLBACK=true

SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=biotechagro.digital@gmail.com
SMTP_PASS=your_16_character_google_app_password_without_spaces
SMTP_SENDER_NAME=Biotech Agro Admin Control

Optional:
GEMINI_API_KEY=your_gemini_api_key

## Important

If `ADMIN_PASSWORD` is set on Vercel, use that password to log in. Password changes inside the admin panel are still stored in temporary JSON unless a permanent database or Vercel Blob DB is connected.

After adding or editing Vercel Environment Variables, redeploy the project.

## Test after deploy

Open:
https://YOUR_DOMAIN/api/health

Check:
- `ok` should be true
- `database.hasSiteContent` should be true
- `database.productCount` should be greater than 0
- `admin.hasAdminPasswordEnv` should be true if you added ADMIN_PASSWORD
- `smtp.configured` should be true if you added SMTP_USER and SMTP_PASS
