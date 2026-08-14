# All Good Things — process capture

Two static pages behind per-person links. Deployed to Netlify as **agtsop**; talks straight
to Supabase over its RPC endpoints, with no server of our own.

| Page | Who opens it |
|---|---|
| `index.html?t=…` | Kristyn and Jess, checking the subtask lists before writing starts |
| `doc.html?t=…` | everyone — the write-up form, which adapts to author or reviewer |

## Deploy

Connected to Netlify by repo. There is no framework and no bundler: the only build step
writes `config.js` from the environment, so keys stay out of git.

Set in **Site configuration → Environment variables**:

| Variable | Value |
|---|---|
| `SUPABASE_URL` | `https://clhsksotuxueqankqibf.supabase.co` |
| `SUPABASE_ANON_KEY` | the project's anon / publishable key |

The anon key is public by design — it is served to every visitor. It grants `execute` on six
`sop_*` functions and nothing else; every table is RLS-locked with all grants revoked, so a
leaked link exposes one document rather than the shape of everything.

## Where the rest lives

The schema, seed, assignment logic and message-pack generator are **not** in this repo —
they hold staff names, ratings and links, and none of it needs to be deployed. They live in
the working repo under `sop/`:

| | |
|---|---|
| `sop/supabase/*.sql` | schema, the six RPCs, generated seed |
| `sop/assign.py` | who writes and who checks each of the 175 documents |
| `sop/seed.py` | regenerates the seed; derives the link tokens |
| `sop/build_send_pack.py` | per-person message packs |
| `sop/mock_rpc.py` | local Supabase stand-in for developing these pages |

To work on these pages locally, run the mock against a local Postgres rather than pointing
at production:

```bash
python3 sop/mock_rpc.py --db agtsop --port 8788
```
