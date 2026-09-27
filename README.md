# LocalMovieSync (LMS)

A local movie browser, VLC-style player, and watch party — for movies already on your own device. No uploads, no backend, no account.

This is a single static HTML file. Everything — browsing, playback, subtitles, the watch party, chat, and reactions — runs entirely in the browser.

---

## How it works

1. Open the page and **Load Movie Folder** (or Add Files) to point it at your local movies.
2. Browse, search, sort, and hit play — full VLC-grade controls: scrubbing, speed, volume, fullscreen, keyboard shortcuts, subtitle upload (.srt/.vtt), and automatic subtitle extraction from embedded MKV tracks.
3. To watch together: click **Watch Party** → **Start a Watch Party**. You get a 6-character code and a shareable link.
4. Everyone joining needs **their own copy of the same movie file** already loaded in their own tab — LMS syncs playback, not the video itself.
5. The host's play/pause/seek syncs to everyone. Chat and quick reactions (❤️ 😂 🔥 👏 😮 👍) work for anyone in the party.
6. Close the host's tab and the party ends immediately for everyone. Nothing lingers anywhere.

---

## Watch Party: the technical honest version

- Connections are peer-to-peer, using **WebRTC** via the [PeerJS](https://peerjs.com) library and its free public broker.
- The broker's only job is introducing two browsers to each other (signaling). Once connected, chat, reactions, and sync messages travel **directly** between browsers — nothing is uploaded to, or stored on, any server.
- The host's browser tab is the hub: every guest connects to the host, and the host relays chat/reactions/sync to everyone else. No Firebase, no database, no persistence, no account — this is intentionally separate from any other backend.
- Requires internet on first load (to fetch PeerJS from its CDN and reach the broker). Playback itself never leaves your device.

**Limits worth knowing:**
- If a guest disconnects and rejoins, they don't see chat/reactions they missed — nothing is saved anywhere.
- Only the host's play/pause/seek broadcasts out, so guests don't fight each other for control.
- Video quality/sync depends on everyone's local file being the same cut/encode — LMS can't verify that, it just assumes it.

---

## Deploying on GitHub Pages

This is one self-contained `index.html` — no build step, no dependencies to install.

1. Create a new GitHub repo (any name), **Public**.
2. Upload `index.html` (Add file → Upload files, or `git push`).
3. **Settings → Pages** → Source: `Deploy from a branch` → Branch `main`, folder `/ (root)` → Save.
4. Live in about a minute at `https://<your-username>.github.io/<repo-name>/`.

No config files, no environment variables, no API keys — the whole app ships in the one file.

---

## Project layout

| Path | Role |
| :--- | :--- |
| `index.html` | Everything — UI, player, watch party, chat, reactions |

---

## Privacy notes

- Movie files never leave your device — playback is read straight from disk via the File API.
- Watch Party data (chat, reactions, sync) travels peer-to-peer; the signaling broker sees connection metadata only, not content.
- Nothing is logged, stored, or sent anywhere once the host's tab closes.

---

## License

MIT.
