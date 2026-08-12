# OSF Backupper

A static, single-page tool that lists everything a public [OSF](https://osf.io) account has shared, then gives you download links for all of it. No backend, no build step, no dependencies: it's just `index.html`, meant to be hosted on GitHub Pages.

**Live tool:** enable GitHub Pages for this repo (Settings > Pages > Deploy from branch `main` / root) and it'll be at `https://lukasroeseler.github.io/osfbackup/`.

## How it works

1. Enter an OSF id or profile link (`tqx69`, `osf.io/tqx69`, or the full URL) and an optional OSF personal access token. The app resolves the account and walks every public project and component it has, showing the whole tree with contributors, descriptions, and wiki pages.
2. For each project/component, click "Get download links" to reveal a direct `.zip` download (OSF's own storage-export endpoint, a real file straight from OSF's servers), any wiki pages, and a `manifest.json` with that node's metadata. If a zip export ever fails for a node, use "List individual file links" instead: a plain list of direct download links, one per file.

You can also select multiple projects/components with the checkboxes and use "Download selected" to queue them all up.

**Use "Save project list" too.** Browsers ignore a suggested filename for cross-origin downloads, so every zip arrives under the name OSF gives it (`<osf-id>-osfstorage-archive.zip`), not under its project title. `project-list.json` maps each OSF id to its title, its place in the component tree, and the exact zip filename it will arrive as, which is what lets you match a folder full of `nvy38-osfstorage-archive.zip` files back to real projects.

### Components listed at the top level

`/users/<id>/nodes/` only returns nodes you are a contributor on. If you contribute to a component but not to its parent project (or the parent is private), nothing in the tree can reach that component through a parent, so it is listed at the top level with a "parent not accessible" marker instead of being dropped. On the author's own account this recovers 9 components that would otherwise be invisible.

## About the access token

OSF's API only sends browser CORS headers back to requests from `osf.io` itself, unless the request carries an `Authorization` header, in which case it allows any origin. Without a token, this page relays API calls through a public CORS proxy instead. A token skips the proxy entirely (talking to `api.osf.io` directly) and raises the rate limit from 100 requests/hour to 10,000/day. [Create one here](https://osf.io/settings/tokens/); read scope is enough.

If you do use a token, it's sent straight to `api.osf.io` or the configured proxy, and is stored only in your browser's local storage.

Actual file *downloads* never need a token: they're generated as plain links straight to `files.osf.io`, which the browser treats as a normal download, not an API call.

## Limitations

- Only reads **public** projects/components/wikis; there's no OSF login flow beyond the optional token, by design.
- Without a token, requests go through a public third-party CORS proxy (`proxy.cors.sh`), which can be slower or occasionally unavailable; a token avoids this entirely.
- Storage add-ons other than OSF Storage (linked GitHub/Dropbox/Google Drive) aren't included in the zip export; use the individual-file fallback or the OSF page link for those.
- OSF's own zip-export endpoint can occasionally return a structurally invalid zip (confirmed independently of this app, via direct download + `unzip -t`), seemingly more often for larger projects and/or filenames with non-ASCII characters. This is a server-side limitation, not something this app can fix. Always open a downloaded zip to confirm it's intact; if it isn't, use "List individual file links" for that project/component instead.
- The suggested `OSF-Backup/<account>/<title> (<id>)/…` filenames only apply to the small files this page generates itself (`project-list.json`, `manifest.json`, wiki `.md`). Zips and individual files come from `files.osf.io`, and browsers ignore a suggested filename for cross-origin downloads, so those land flat under OSF's own names. Use `project-list.json` to map them back.
- Triggering many downloads in a row may hit your browser's "multiple downloads" prompt; allow it for this site if asked.

## License

MIT, see [LICENSE](LICENSE). Not affiliated with the Center for Open Science.
