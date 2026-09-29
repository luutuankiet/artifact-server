# The filesystem is the database

Artifacts are stored as `artifacts/<slug>.html` with a sidecar
`artifacts/.meta/<slug>.json`, and nothing else. There is no SQLite or other
store. The whole inventory is `ls artifacts/`, backup is copying one directory,
and an HTML file dropped in by hand shows up in the gallery. SQLite was
considered and rejected as overhead for what is only ever a file listing.

## Consequences

- Listing reads and parses every sidecar on every gallery load. Fine at hundreds
  of artifacts, not at hundreds of thousands.
- Slugs are filenames, so they are the only identity an artifact has: publishing
  twice with the same slug overwrites without warning.
- Default slugs start with `YYYY-MM-DD-` so a plain directory listing sorts by date.
