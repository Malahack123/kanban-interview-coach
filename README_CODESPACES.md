# Run the Mobile App in GitHub Codespaces

This repository is pre-configured to run the Expo (React Native) mobile app in GitHub Codespaces without installing Node locally.

## 1) Open in Codespaces

- Push this repo to GitHub (if not already).
- In GitHub UI, click "Code" > "Codespaces" > "Create codespace on main".
- The container will build automatically and run the post-create command to install dependencies for the mobile app.

## 2) Env variables

In the Codespaces terminal:

```
cd _/apps/mobile
cp .env.example .env
# Open the file and paste your Supabase credentials
# EXPO_PUBLIC_SUPABASE_URL=...
# EXPO_PUBLIC_SUPABASE_ANON_KEY=...
```

You can edit the file with:

```
code .env
```

## 3) Supabase schema and Realtime

In your Supabase Dashboard:
- Open SQL editor and paste the contents of `_/apps/mobile/supabase/schema.sql`.
- Run it.
- Enable Realtime for schema `public` and table `jobs`.

## 4) Start the app

In Codespaces terminal from `_/apps/mobile`:

- Web preview (fastest):
```
npx expo start --web
```
Then click the forwarded port (19006) in the Codespaces Ports panel.

- Device preview via Expo Go (tunnel):
```
npx expo start --tunnel
```
Open the QR code in Expo Go on your phone. This uses an internet tunnel so your device can connect.

Note: The `--tunnel` method can be slower than web preview, but it shows a real device experience.

## 5) What to test

- Navigate to `Accueil` and press "Démarrer" or the floating + to open the Kanban board.
- Add a job: a new card is created and persisted to Supabase with AI-generated content.
- Drag & drop cards between columns to change status (persisted to Supabase).
- Open a card and use the "Préparation" tab:
  - Adjust preparation progress with +/-10% buttons (persisted).
  - Tap "Marquer comme prêt" to set status to ready and show the "Prêt pour l’entretien" badge.

## 6) Troubleshooting

- If `@` path alias fails, run `npm install` again and restart the Expo server.
- If Supabase operations fail, ensure `.env` has valid keys and the SQL schema/policies were applied.
- If realtime does not update, make sure Realtime is enabled for `public.jobs`.
