# Planning notes replaced by docs/ and a small AGENTS.md

The repository started with a directory of agent planning notes: a project
brief, an architecture sketch and a session work log, all written before any
code existed, plus the agent and command definitions that maintained them. By
the time the server was built most of that no longer described it: the sketch
listed `src/mcp/`, `src/build/` and `templates/` directories that were never
created, and planned a server-side Vite build and pre-bundled libraries that
were both dropped. We rewrote what was still true against the running code into
`AGENTS.md` and `docs/`, and deleted the notes. Git history keeps the originals.

## What survived, and where

- The brief's purpose and constraints → the top of `AGENTS.md`.
- The architecture sketch → three pages under `docs/architecture/`, re-derived
  from the code rather than the sketch.
- The design decisions that still hold and pass the admission tests →
  `0001`, `0002`, `0003`.
- The library-parity goal → `docs/reference/cdn-library-manifest.md`.

## What was dropped

- The work log's research narrative (how the idea arose, a survey of similar
  projects) and its task list. History, not documentation.
- Deployment specifics: hostnames, DNS and proxy wiring. That is the state of
  one deployment, not the shape of the repository.

## Considered options

- **Keep the notes alongside `docs/`.** Rejected: two stores of truth, and the
  notes were already the stale one.
