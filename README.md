# Help at School – LIVE (child tablet → staff tablets)

Files
- `index.html`  child app (your original, patched: school pairing + "Send to an adult"; images recompressed to WebP, 12.5 MB -> 1.9 MB)
- `staff.html`  staff live queue (sign in, realtime alerts, On my way / Resolved)
- `config.js`   Supabase URL + anon key
- `supabase/schema.sql`, `supabase/seed.sql`
- `manifest.webmanifest`, `logo.jpg`

Set-up (about 20 minutes)
1. Supabase: new project in a UK/EU region. Authentication > Providers > enable **Anonymous sign-ins**.
2. SQL editor: run `supabase/schema.sql`, then adapt `supabase/seed.sql` (it returns the school's auto-generated 6-digit **staff PIN**: give it to the school, it is needed to disconnect a child tablet) (create each adult under Authentication > Users, then insert them into `staff` with the display name children will see).
3. Put the project URL and anon key in `config.js`.
4. Push this folder to GitHub, import in Vercel (static site, no build). Put the domain on Cloudflare.
5. Child tablets: open the site, Settings area > "Connect to my school", type the school code, then Add to Home Screen.
6. Staff tablets: open `/staff.html`, sign in, tap "Turn on sound alerts", Add to Home Screen.

Routing
- Child picks an adult (their own list plus the school's staff list) -> that adult is alerted.
- No adult named, or "I need an adult now" -> all staff are alerted.
- Named adult has not responded within 2 minutes -> everyone is alerted ("NOT YET ANSWERED").
- Urgent requests re-sound every 10 s until someone taps On my way.
- The child's screen shows "Mrs Patel is coming to you" when staff acknowledge.

Before using with real pupils
- DPIA and a data-processing agreement with each school; retention job in schema.sql (30 days).
- Rate-limit `/auth/v1/signup` and the pair_device RPC (Cloudflare / Supabase) and keep school codes long and random.
- Make clear to schools this is not an emergency service; staff tablets must stay awake with the page open.
- Disconnecting a child tablet needs the school's staff PIN (checked on the server). Rate-limit the PIN RPC too.
- Child names are first name + year only; the message text can contain anything the pupil types in "own words".
