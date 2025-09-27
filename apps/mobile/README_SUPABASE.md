# Supabase setup for Mobile Kanban

This app uses Supabase for persisting Kanban job cards with realtime updates and per-user Row Level Security (RLS).

## 1) Environment variables

Create an `.env` file in `apps/mobile/` based on `.env.example` and fill in your project credentials:

```
EXPO_PUBLIC_SUPABASE_URL=https://YOUR-PROJECT.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=YOUR-ANON-KEY
```

Expo reads `EXPO_PUBLIC_*` keys at runtime in the client.

## 2) Install dependencies

From the `apps/mobile/` directory:

```
npm install
```

## 3) Database schema and policies

Open the Supabase Dashboard for your project, go to SQL, and run the SQL file:

- `apps/mobile/supabase/schema.sql`

This will:
- Create the `public.jobs` table.
- Enable Row Level Security.
- Add per-user policies (select/insert/update/delete using `user_id = auth.uid()`).
- Create a trigger to automatically set `user_id` on insert.

## 4) Realtime

Enable Realtime for the `public` schema and `jobs` table in the Supabase Dashboard.

## 5) Alias configuration

This app uses `@` alias to refer to `src/` paths in the mobile app. We configured Babel with `babel-plugin-module-resolver`.
If you still see resolution issues, make sure `babel.config.js` exists in `apps/mobile/` and that you ran `npm install`.

## 6) Authentication

RLS policies rely on `auth.uid()`. Ensure the user is authenticated in the app before using the Kanban. Otherwise, list/create/update may be blocked.

## 7) Troubleshooting

- If you get module resolution errors for `@/` paths, run `npm install` and restart the bundler.
- If updates fail due to RLS, confirm you are authenticated and that `schema.sql` policies executed successfully.
- If new rows don’t appear automatically, check that Realtime is enabled for the `jobs` table.

## 8) Demo Preview (GitHub Pages)

For a public preview on GitHub Pages (no auth), you can temporarily allow anonymous access via RLS policies.

1. Add repository secrets in GitHub (Settings → Secrets and variables → Actions)

```
EXPO_PUBLIC_SUPABASE_URL
EXPO_PUBLIC_SUPABASE_ANON_KEY
```

2. Enable GitHub Pages (Settings → Pages → Source: GitHub Actions) and push to `main`.

3. In Supabase Dashboard → SQL editor, choose ONE of the following preview policy sets:

Read-only preview (recommended):

```sql
create policy "Preview anon select" on public.jobs
for select to anon using (true);
```

Full preview (write-enabled):

```sql
create policy "Preview anon select" on public.jobs
for select to anon using (true);

create policy "Preview anon insert" on public.jobs
for insert to anon with check (true);

create policy "Preview anon update" on public.jobs
for update to anon using (true) with check (true);

create policy "Preview anon delete" on public.jobs
for delete to anon using (true);
```

4. Add your Pages domain to Supabase CORS Allowed Origins:

```
https://<your-gh-username>.github.io
```

5. Your Pages URL will be:

```
https://<your-gh-username>.github.io/<your-repo-name>
```

Reverting after the demo:

```sql
drop policy if exists "Preview anon select" on public.jobs;
drop policy if exists "Preview anon insert" on public.jobs;
drop policy if exists "Preview anon update" on public.jobs;
drop policy if exists "Preview anon delete" on public.jobs;
```
