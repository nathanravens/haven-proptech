# Haven — South African property marketplace

A Next.js + Supabase starter for sale, long-term rental and short-stay listings. Designed for dashboard-based deployment: no terminal is needed to create the Supabase project or deploy on Vercel. The code repository is supplied as a ZIP; to connect it to Vercel, upload/extract it into a GitHub repository using GitHub's website, then import that repository in Vercel.

## What is included
- Responsive property discovery page with Buy / Rent / Short stay tabs, location suggestions, type, bedroom and maximum-price filters.
- Email/password sign-up, login and password-reset flow through Supabase Auth.
- Three-stage listing creation with property attributes, amenities and multi-photo uploads.
- Public listing records and public image delivery; row-level security protects owner edits and admin moderation.
- Listing detail overlay with direct WhatsApp and email contact actions.
- SQL schema, storage bucket and access policies in `supabase/setup.sql`.

## Important scope notes before public launch
This is a functional MVP foundation, not a fully audited production marketplace. The first release includes listing creation and browsing, but the visual My Listings control currently filters only active rows; full edit/pause/delete controls, admin moderation UI, cover-photo reordering, a true rich-text editor, server-side image processing, CAPTCHA/rate limiting, email verification branding, and production-grade search pagination should be added before a commercial launch. Admin role is assigned manually in Supabase Studio. Do not expose a Supabase service-role key in Vercel's `NEXT_PUBLIC_` variables. The public anonymous key is designed to be used with the included RLS policies.

## 1. Create Supabase (visual setup)
1. Open [Supabase](https://supabase.com/) and create a free account, then choose **New project**.
2. Give the project a name (for example `haven-property`), create a strong database password and select a nearby region. Save the database password somewhere safe.
3. Once the project is ready, open **SQL Editor** in the left menu, choose **New query**, open `supabase/setup.sql` from this project, copy the entire file, paste it into the editor and click **Run**. This creates the tables, access rules and `property-images` public storage bucket.
4. To confirm the bucket, open **Storage**. You should see `property-images`. It is public for image display; uploads are restricted to signed-in users' own folder.
5. Open **Project Settings → API** (or **API Keys**, depending on the current dashboard layout). Copy the **Project URL** and the **publishable/anon key**. Use only the publishable/anon key in the browser app. Never use the `service_role` / secret key.
6. Open **Authentication → URL Configuration**. After Vercel deploys, add your Vercel site address to **Site URL** and **Redirect URLs**. For local/preview testing you can also add `http://localhost:3000/**` and your Vercel preview domain if needed.

## 2. Put the project in GitHub without a terminal
1. Sign in at [GitHub](https://github.com/) and choose **New repository**. Name it `haven-property`, choose **Private** or **Public**, then create it.
2. On the empty repository page choose **Add file → Upload files**.
3. Unzip this downloaded project on your computer. Open the extracted `proptech-sa` folder, select all files and folders inside it (including `app`, `components`, `lib`, `supabase`, and `package.json`) and drag them into GitHub's upload area. Do not upload the ZIP itself and do not nest an extra `proptech-sa` folder inside the repository.
4. Wait for the upload to finish, enter a commit message such as `Initial Haven app`, then click **Commit changes**.

## 3. Deploy on Vercel (dashboard only)
1. Open [Vercel](https://vercel.com/) and sign in with GitHub.
2. Choose **Add New → Project**, find `haven-property` under Import Git Repository, and click **Import**.
3. Framework should be detected as **Next.js**. Leave the build command and output settings at their defaults.
4. Expand **Environment Variables** before deploying. Add these two entries exactly:
   - Name: `NEXT_PUBLIC_SUPABASE_URL` — Value: paste the Project URL copied from Supabase.
   - Name: `NEXT_PUBLIC_SUPABASE_ANON_KEY` — Value: paste the publishable/anon key copied from Supabase.
   Select all environments (Production, Preview and Development) if Vercel offers the checkboxes.
5. Click **Deploy**. Vercel installs dependencies and builds the site. When complete, click **Visit** to open your live site.
6. Copy the Vercel domain (for example `haven-property.vercel.app`) and add it to Supabase **Authentication → URL Configuration** as described above. Redeploy if you change environment variables later: Vercel → Project → Deployments → latest deployment menu → **Redeploy**.

## 4. First-use checks
1. Open the live site and create a user account with an email address you can access.
2. Confirm the email if Supabase email confirmation is enabled. Sign in.
3. Click **List property**, complete the three steps and publish. Try uploading a few JPG, PNG or WebP photos under 5 MB each.
4. Sign out and confirm the active listing remains visible to visitors.
5. Check that contact links open WhatsApp or the user's email application.

## 5. Admin and moderation
The SQL creates a `profiles` row with role `user` for each new signup. To make a trusted account an admin: in Supabase **Table Editor → profiles**, find the account's UUID and change its `role` value from `user` to `admin`. Admins can then moderate records directly in **Table Editor → listings**. Do not grant admin access to untrusted users. The starter's frontend does not yet include a dedicated admin console.

## 6. South African location list
Suggestions are currently supplied by `lib/locations.ts`. A non-technical content update can be made in GitHub's browser editor: open that file, add a new quoted location in the `locations` array using `Province, City, Suburb` format, save/commit, and Vercel automatically redeploys.

## 7. Costs and operational notes
Supabase and Vercel both offer free tiers subject to quotas and changing terms. Check their current pricing before launch. Public user-generated listings require moderation, clear prohibited-content rules, privacy/POPIA notices, terms of use, and a process for reporting scams or inaccurate listings. Confirm image rights and obtain consent before publishing contact details. Configure backups and monitor usage as traffic grows.

## 8. Troubleshooting
- **“Add your Supabase environment variables”**: check both variable names and values in Vercel Project Settings → Environment Variables, then redeploy.
- **Sign-up works but email never arrives**: check Supabase Authentication email settings, spam folder and provider limits. Configure custom SMTP before public launch.
- **Photo upload fails**: ensure SQL ran successfully, the bucket is named exactly `property-images`, the image is JPG/PNG/WebP and is no larger than 5 MB.
- **New listing does not show**: confirm it was published with status `active`; check Supabase Table Editor → listings and the browser console for policy errors.
- **Auth redirects to an error page**: add the exact deployed Vercel domain to Supabase Authentication → URL Configuration.
