# One-Page Portfolio Template with Admin Backend

A professional one-page portfolio website. Everything on the page (text, colours, fonts, sections, work samples) is edited from a secure dashboard at `/admin`.

**Roles**

| Role | Can edit content and work samples | Can read messages | Can add or remove admins |
|---|---|---|---|
| Super Admin | Yes | Yes | Yes |
| Admin | Yes | Yes | No |

**Stack:** plain HTML, CSS and JavaScript on Vercel, with Supabase (free plan is enough) for login, database and image storage. There is no build step.

---

## Step 1. Create the Supabase project (5 minutes)

1. Go to supabase.com, create an account and click **New project**. Choose a strong database password and save it.
2. Open **SQL Editor > New query**, paste the whole content of `supabase/schema.sql`, and click **Run**.
3. Open **Authentication > Providers > Email** and turn **off** "Allow new users to sign up" (so only the Super Admin can create logins).
4. Open **Authentication > Users > Add user > Create new user**. Enter your email and a strong password, and tick **Auto Confirm User**. This will be the Super Admin login.
5. Back in **SQL Editor**, run this (replace the email with the one you just used and your name):

```sql
insert into public.profiles (id, email, full_name, role)
select id, email, 'Your Full Name', 'super_admin'
from auth.users where email = 'YOUR_EMAIL_HERE';
```

6. Open **Project Settings > API** and copy three values: the **Project URL**, the **anon public key**, and the **service_role key**.

## Step 2. Add the public keys to the project

Open `config.js` and replace the two placeholders with the Project URL and the **anon** key. These two are safe to be public.

```js
window.APP_CONFIG = {
  SUPABASE_URL: "https://xxxx.supabase.co",
  SUPABASE_ANON_KEY: "eyJ..."
};
```

Never put the service_role key in this file.

## Step 3. Deploy on Vercel

1. Upload this folder to a GitHub repository (or use the Vercel CLI: run `vercel` inside the folder).
2. On vercel.com click **Add New > Project**, import the repository, and set **Framework Preset** to **Other**. Leave build settings empty.
3. Before deploying, open **Environment Variables** and add:
   - `SUPABASE_URL` = your Project URL
   - `SUPABASE_SERVICE_ROLE_KEY` = your service_role key
4. Click **Deploy**.

Your website is at your Vercel address. The dashboard is at `/admin`.

## Step 4. Use the dashboard

1. Go to `yoursite.com/admin` and sign in with the Super Admin email and password.
2. **Website content**: edit each section, then click **Save changes**. The live site updates immediately.
3. **Work samples**: add designs with an image upload, title, category and description. Use the arrows to reorder. Untick "Show on the website" to hide a piece without deleting it.
4. **Team** (Super Admin only): add an admin by entering name, email and a temporary password, then send those details to them. You can also reset a password or remove an admin.
5. **Messages**: enquiries from the contact form arrive here.

## How the template idea works

Every text, colour, font, link and section toggle is stored as data, not code. To reuse this for another client, deploy a copy with a new Supabase project, sign in, and replace the content. To add a new editable field, add one line to `SECTIONS` in `admin/admin.js` and use it in `assets/site.js`.

## Security notes

- Row Level Security is enabled on every table. Visitors can only read published content and send messages.
- Admin creation and removal run in `api/admins.js` on the server, which checks that the caller is a Super Admin.
- If the Super Admin forgets their own password, reset it in Supabase under Authentication > Users.
- Keep the Super Admin password strong and do not share the service_role key.

## Files

```
index.html            public one-page site
config.js             public Supabase URL and anon key
assets/site.css       site design
assets/site.js        site rendering and contact form
assets/shared.js      template defaults and helpers
admin/index.html      dashboard page
admin/admin.js        dashboard logic (edit SECTIONS to add fields)
admin/admin.css       dashboard design
api/admins.js         secure server function for adding and removing admins
supabase/schema.sql   database, roles, security rules, image storage
vercel.json           security headers
```

## Try it before connecting Supabase

Until `config.js` is filled in, the public page shows the template content with sample work tiles, so you can preview the design. The dashboard needs Supabase to sign in.
