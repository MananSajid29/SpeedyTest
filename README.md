# TypeRacer 4X

Developed by **Manan Sajid**, CS student at **COMSATS Lahore**.

A head-to-head typing race against Nova, an independent computer driver. The first driver to finish wins. The TypeRacer 4X brand remains, but current races have two cars.

## Development

```sh
pnpm install
pnpm dev
pnpm test
pnpm typecheck
pnpm build
```

Open http://localhost:3000. Deploy the Next.js project to Vercel with default settings.

## Race rules

Sprint, Circuit and Endurance select approximately 150, 300 and 600 characters. The internal `duration` setting is retained for compatibility, but no longer determines race duration. Both cars start from the grid and move at independent rates toward the same finish line.

Your distance equals the furthest correctly completed prefix of the passage divided by its length. Wrong characters hold your car until corrected. Backspace does not move your car backward, and retyping old text cannot inflate its distance. Stopping typing stops movement immediately. The interface interpolates car positions for smooth visual movement; there is no simulated coasting. Live speed measures newly completed characters over the last three seconds.

Nova simulates typed characters using a seeded WPM target, accuracy profile, and deterministic pace variations. Easy is 15–25 target WPM, Medium 25–40, Hard 40–60, and Expert 60–85 before pace variation and accuracy adjustment. Nova's movement is independent of your input. The race ends when either driver completes the passage; the other driver's completed distance is recorded. An idle session has a ten-minute safety limit. Exact ties use lane order.

Gross WPM = current typed characters / 5 / elapsed minutes. Net WPM = max(0, gross WPM - uncorrected errors / elapsed minutes). Accuracy = correct character attempts / all character attempts. Spaces and punctuation count. Backspace removes text but preserves attempt history; correcting an error therefore does not erase its effect on accuracy. Competitive paste/drop is disabled. Hidden tabs pause the race. Reduced-motion preferences disable car interpolation.

## Supabase authentication

Copy `.env.example` to `.env.local` and set your Supabase Project URL, public publishable key, and site URL. Configure the same values in Vercel and redeploy. Never expose a service-role key.

Email registration, login, logout, email confirmation, password reset, cookie session refresh and the server-protected `/account` route are implemented. Display names use Supabase Auth metadata. No database migration is needed for these auth features.

In Supabase → Authentication → URL Configuration:

- Site URL: `https://typeracer-4x.vercel.app`
- Redirect URLs: `https://typeracer-4x.vercel.app/auth/callback` and `https://typeracer-4x.vercel.app/auth/callback?next=/update-password`
- For development, add the equivalent `http://localhost:3000` callback URLs.

Keep email verification enabled and configure SMTP for production email delivery. The default confirmation/reset templates use `{{ .ConfirmationURL }}`; open PKCE email links in the browser that requested them. For cross-browser links, set Confirm signup to `{{ .SiteURL }}/auth/confirm?token_hash={{ .TokenHash }}&type=signup` and Reset password to `{{ .SiteURL }}/auth/confirm?token_hash={{ .TokenHash }}&type=recovery`.

## Verification

The engine tests cover scoring, corrections, player progress, no idle coasting, wrong-text rejection, both victory paths, independent AI pace, deterministic replay, frame-rate independence, and backspace distance protection.

Manually test each distance/difficulty, complete a passage before Nova, allow Nova to win, pause/resume, restart, correct errors, and check narrow screens and keyboard focus. Verify history survives reload. Check that signed-out `/account` redirects to login, then register with an email you own, confirm it, log in, refresh, log out, and test a password reset.

## Remaining scope

Race history and statistics are currently stored only in the browser, including when logged in. Cloud race persistence, database migrations/RLS, detailed analytics, achievements, and a public leaderboard are unfinished. Old four-car guest results keep their original positions. These browser-measured races are not verified competitive scores. Storage errors are shown in the result screen. Clearing browser storage removes guest results.

## Timed races and driving view

Timed race is the default. Select 30, 60, or 120 seconds. Text automatically extends before its end; passing the lap line starts another lap and never ends a timed race early. The winner is the driver with the greatest correct-text distance when the clock reaches zero. Car positions show their current lap, and lap counters distinguish different completed distances. Final car positions are normalized to the winning distance. Finish-line mode remains available with the original first-to-complete rule.

During countdown, racing and pause, the app uses a viewport-sized driving layout that keeps the road, telemetry, passage and typing input together. It adapts to visual viewport changes when a mobile keyboard opens. Wheel rotation, subtle suspension movement and slipstreams respond to simulated/live speed and honor reduced-motion preferences.
