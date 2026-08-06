# OSF Backupper

A static, single-page tool that lists everything a public [OSF](https://osf.io) account has shared, then gives you structured downloads of it. No backend, no build step, no dependencies: it's just `index.html`, meant to be hosted on GitHub Pages.

**Live tool:** enable GitHub Pages for this repo (Settings > Pages > Deploy from branch `main` / root) and it'll be at `https://lukasroeseler.github.io/osfbackup/`.

## How it works

1. Paste an OSF personal access token, then enter an OSF id or profile link (`tqx69`, `osf.io/tqx69`, or the full URL). The app resolves the account and walks every public project and component it has, showing the whole tree with contributors, descriptions, and wiki pages.
2. For each project/component, click "Get download links" to reveal a direct `.zip` download (OSF's own storage-export endpoint, a real file straight from OSF's servers), any wiki pages, and a `manifest.json` with that node's metadata. If a zip export ever fails for a node, use "List individual file links" instead: a plain list of direct download links, one per file.

You can also select multiple projects/components with the checkboxes and use "Download selected" to queue them all up.

## Why a token is required

OSF's API only sends browser CORS headers back to requests from `osf.io` itself, unless the request carries an `Authorization` header, in which case it allows any origin. So a personal access token isn't just about the rate limit: it's what lets this page talk to the API at all. [Create one here](https://osf.io/settings/tokens/); read scope is enough. It also raises the rate limit from 100 requests/hour (anonymous) to 10,000/day.

The token stays in your browser: it's sent straight to `api.osf.io`, stored only in your browser's local storage, and never passed through any third party.

Actual file *downloads* don't need the token at all: they're generated as plain links straight to `files.osf.io`, which the browser treats as a normal download, not an API call.

## Limitations

- Only reads **public** projects/components/wikis; there's no OSF login flow beyond the token, by design.
- Storage add-ons other than OSF Storage (linked GitHub/Dropbox/Google Drive) aren't included in the zip export; use the individual-file fallback or the OSF page link for those.
- The suggested nested-folder filenames (`download="OSF-Backup/Project/Component.zip"`) are honored by Chromium-based browsers; Firefox/Safari will save them as flat filenames in your default downloads folder instead.
- Triggering many downloads in a row may hit your browser's "multiple downloads" prompt; allow it for this site if asked.

## License

MIT, see [LICENSE](LICENSE). Not affiliated with the Center for Open Science.
