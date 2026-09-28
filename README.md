# 🎬 LocalMovieSync (LMS)

<p align="center">
  <strong>A private browser cinema for your local movies — with a real-time P2P Watch Party.</strong>
</p>

<p align="center">
  <a href="https://r2dapps.github.io/LocalMovieSync/">
    <img src="https://img.shields.io/badge/🚀%20Live%20Demo-LocalMovieSync-ef4444?style=for-the-badge" alt="Live Demo">
  </a>
  <img src="https://img.shields.io/badge/HTML5-Single%20File-e34f26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5">
  <img src="https://img.shields.io/badge/JavaScript-Vanilla-f7df1e?style=for-the-badge&logo=javascript&logoColor=111" alt="JavaScript">
  <img src="https://img.shields.io/badge/WebRTC-P2P-4285f4?style=for-the-badge" alt="WebRTC">
  <img src="https://img.shields.io/badge/PeerJS-Signaling-8b5cf6?style=for-the-badge" alt="PeerJS">
</p>

<p align="center">
  <a href="https://r2dapps.github.io/LocalMovieSync/">🌐 Open Live Demo</a>
  ·
  <a href="#-features">Features</a>
  ·
  <a href="#-watch-party">Watch Party</a>
  ·
  <a href="#-privacy--data-flow">Privacy</a>
  ·
  <a href="#-limitations">Limitations</a>
</p>

---

## ✨ What is LocalMovieSync?

**LocalMovieSync (LMS)** is a browser-based personal cinema for video files already on your device.

It combines two modes:

- 🎞️ **Private Local Cinema** — browse and play your own movie collection directly in the browser.
- 🍿 **Watch Party Theatre** — create a room where a host streams the currently playing movie to guests through WebRTC, with synchronized playback, queue management, live chat, reactions and a cinematic countdown.

There is **no movie-storage backend, database, account system, or upload pipeline**.

The application is intentionally delivered as a **single HTML file**.

> **Important:** Watch Party is not the same as ordinary local playback. During a Watch Party, the host's playing media is captured from the browser and sent to connected guests through WebRTC.

---

## 🚀 Live Demo

### 👉 https://r2dapps.github.io/LocalMovieSync/

Open it in a modern browser and load a few local video files.

**Note:** The GitHub Pages URL above is the live-demo URL supplied for this project. The page could not be fetched from my current web environment, so I am not claiming a fresh external verification of its current deployment state.

---

## 🏷️ Project Tags

`#LocalMoviePlayer` `#WatchParty` `#WebRTC` `#PeerJS` `#P2PStreaming` `#LocalFirst` `#BrowserCinema` `#HTML5Video` `#MKV` `#Subtitles` `#LiveChat` `#Reactions` `#JavaScript` `#GitHubPages` `#NoBackend`

---

# 🎥 Features

## 🏠 Local Cinema

- Load an entire movie folder using the browser File API.
- Add individual video files without replacing the existing library.
- Search movies by title.
- Filter by video format.
- Sort the library.
- Generate video thumbnails in the browser.
- Display movie duration and file information.
- Remember local playback position.
- Resume movies from where you stopped.
- No movie file needs to be uploaded to a server for normal local playback.

### Supported formats

The application is designed around common browser-playable formats such as:

- MP4
- MKV
- WebM
- MOV
- OGG
- AVI
- M4V

Actual playback depends on the browser's codec support.

---

# 🎬 Cinema Player

The player is designed as a custom cinema-style interface rather than relying on the browser's default video controls.

### Playback controls

- Play / pause
- ±10 second seek
- Timeline scrubbing
- Volume control
- Mute / unmute
- Playback speed
- Fullscreen
- Responsive controls
- Keyboard shortcuts
- Mobile-friendly touch interaction
- Auto-hiding player controls
- Host-only controls during remote Party playback

The current player also includes a **seek-lock control** intended to reduce accidental timeline touches.

---

# 💬 Subtitles

LMS supports subtitle workflows directly in the browser.

### External subtitles

Load:

- `.srt`
- `.vtt`

SRT files are converted to WebVTT in the browser before being attached to the video element.

### Embedded MKV subtitles

For MKV files, LMS can extract supported embedded subtitle tracks client-side using the Matroska subtitle parser.

No FFmpeg server is required for this subtitle extraction path.

---

# 🍿 Watch Party Theatre

This is the part that makes LMS more than a local video player.

A Watch Party has a simple authority model:

```text
                    ┌─────────────────────┐
                    │       HOST          │
                    │                     │
                    │ Local movie file    │
                    │ Playback authority  │
                    │ Queue authority     │
                    └──────────┬──────────┘
                               │
                     WebRTC media + data
                               │
              ┌────────────────┼────────────────┐
              │                │                │
        ┌─────▼─────┐    ┌─────▼─────┐    ┌─────▼─────┐
        │  GUEST 1  │    │  GUEST 2  │    │  GUEST 3  │
        │            │    │            │    │            │
        │ Video      │    │ Video      │    │ Video      │
        │ Chat       │    │ Chat       │    │ Chat       │
        │ Reactions  │    │ Reactions  │    │ Reactions  │
        └────────────┘    └────────────┘    └────────────┘
```

### Host

The host:

- Creates the party.
- Receives a 6-character party code.
- Shares a party link/code.
- Maintains the queue.
- Accepts or rejects guest movie requests.
- Decides what actually plays.
- Controls play/pause/seek.
- Streams the current video to guests.
- Ends the party.

### Guest

Guests can:

- Join using the party code/link.
- Browse their own local movie library.
- Request a movie.
- Cancel their own request.
- Chat with everyone.
- Send reactions.
- Watch the host's actual stream.

Guests do **not** control the host's playback.

---

# 📋 Queue System

The queue deliberately separates:

**Request → Approval → Play**

A movie being requested does **not** automatically start playback.

### Host workflow

```text
Guest requests movie
        ↓
Request appears in queue
        ↓
Host accepts
        ↓
Queue item becomes Approved
        ↓
Host presses Play
        ↓
Cinematic countdown
        ↓
Screening starts
```

This prevents a guest from unexpectedly taking over the theatre.

Queue actions include:

- Add to Queue
- Request
- Accept
- Reject
- Remove
- Cancel own request
- Play approved item

---

# ⏱️ Cinematic Countdown

Before a Party screening begins, LMS presents a cinematic countdown.

The purpose is to give everyone a common starting moment rather than abruptly starting the video.

The countdown is separate from normal local playback state.

---

# 📡 WebRTC Video Streaming

The host's playing `<video>` element can be captured with the browser's MediaStream API.

Conceptually:

```text
Local movie file
      ↓
Host <video>
      ↓
captureStream()
      ↓
MediaStream
      ↓
WebRTC
      ↓
Guest browser
```

This means a guest can watch the host's stream without having the same movie file locally.

The implementation uses **PeerJS** for browser-to-browser connection setup/signaling and WebRTC for the actual peer connection.

---

# 💬 Live Party Chat

Party chat is available while inside the theatre.

The current UI is designed so that closing the chat does **not** permanently remove the ability to reopen it.

During movie playback, the chat control is placed with the player toolbar so it remains discoverable alongside the media controls.

On smaller screens, the party player uses a stacked layout with the video above and chat below.

---

# ❤️ Reactions

Quick reactions can be sent during a screening.

The UI supports a horizontally scrollable reaction bar so a large set of reactions does not destroy the layout on smaller screens.

Reactions also have visual effects such as:

- Floating reactions
- Burst particles
- Reaction rings

These are visual session effects rather than persistent social data.

---

# 🖥️ Responsive Theatre UI

The Theatre and player are designed around multiple screen classes:

- Large desktop
- Desktop
- Tablet
- Mobile portrait
- Mobile landscape
- Fullscreen playback

The player switches from a side-by-side cinema layout on larger screens to a stacked video/chat layout on smaller screens.

The current design also uses:

- Glass panels
- Responsive controls
- Compact mobile buttons
- Scrollable queues
- Touch-friendly interaction
- Fullscreen-specific layout handling

---

# 🔐 Privacy & Data Flow

LMS is designed around local-first media handling.

## Normal playback

```text
Your movie
   ↓
Browser File API
   ↓
Local Blob URL
   ↓
HTML5 <video>
```

The movie does not need to be uploaded.

## Watch Party

For a Watch Party:

```text
Host movie
   ↓
Host browser <video>
   ↓
MediaStream
   ↓
WebRTC
   ↓
Guests
```

So there is an important distinction:

> **Normal playback is local. Watch Party playback intentionally sends the host's currently playing media to connected guests.**

The application does not use a central movie-storage server.

Chat, queue state, reactions and playback-control messages are exchanged through the party's peer connections.

---

# 🌐 Internet Requirements

The local player and the Watch Party are different in this respect.

The current HTML references external browser libraries/CDN assets including:

- Tailwind CSS CDN
- Font Awesome CDN
- Matroska subtitle library
- PeerJS

Therefore, **the current deployed page is not a completely self-contained offline-first web bundle on first load**.

Once the required assets are available, normal movie playback itself uses the local files selected by the user.

Watch Party additionally needs network connectivity for peer discovery/signaling and WebRTC connectivity.

---

# ⚠️ Limitations

## Browser codec support

HTML5 video playback ultimately depends on the browser's codec support.

A file extension being supported does not guarantee that every codec inside that container is supported.

For problematic files, conversion to a browser-friendly combination such as H.264/AAC MP4 may be necessary.

## WebRTC connectivity

Peer-to-peer connections can work across:

- Same Wi-Fi
- Different Wi-Fi networks
- Wi-Fi ↔ mobile data
- 4G/5G

However, restrictive NAT/firewall environments can prevent direct peer connections.

A production-scale deployment could add a **TURN relay fallback** for difficult network conditions.

## No persistent party history

Party chat and reactions are session-oriented.

If someone joins later, they do not receive a historical replay of everything that happened before joining.

## Host dependency

The host is the authority for the screening.

If the host closes the party/browser or loses the required connection, the screening cannot continue normally.

---

# 🧠 Architecture

LMS intentionally keeps the application simple:

```text
┌──────────────────────────────────────────┐
│              LocalMovieSync              │
│              Single HTML App             │
├──────────────────────────────────────────┤
│                                          │
│  Local Library                           │
│  ├── File API                            │
│  ├── Blob URLs                           │
│  ├── Canvas thumbnails                   │
│  └── localStorage watch history          │
│                                          │
│  Player                                  │
│  ├── HTML5 Video                         │
│  ├── Custom Controls                     │
│  ├── Subtitles                           │
│  └── Fullscreen                          │
│                                          │
│  Watch Party                             │
│  ├── PeerJS                              │
│  ├── WebRTC MediaStream                  │
│  ├── Data Connections                    │
│  ├── Queue                               │
│  ├── Chat                                │
│  ├── Reactions                           │
│  └── Playback Synchronization            │
│                                          │
└──────────────────────────────────────────┘
```

---

# 🧪 Current Build Quality

The current iteration has moved well beyond the original prototype.

The player has been hardened around several practical browser issues, including:

- MKV subtitle reader cancellation
- stale party media call cleanup
- remote live-stream duration handling
- guest playback isolation
- guest autoplay recovery
- movie-library append behavior
- mobile control interaction
- tablet chat layout
- fullscreen state handling
- responsive Theatre layout
- custom confirmation dialogs
- native alert avoidance
- subtitle conversion
- reaction overflow handling
- party queue authorization

The JavaScript in the supplied current build also passes a standalone syntax check.

---

# 🎨 Design Direction

The latest UI intentionally moves away from a basic Netflix clone and toward a **private digital cinema / modern theatre** aesthetic.

Visual language includes:

- Deep navy-black cinema background
- Crimson/rose accent lighting
- Glassmorphism panels
- Soft red glow
- Rounded cinema cards
- Compact metadata typography
- JetBrains Mono for technical values
- Outfit for the primary interface
- Cinematic fullscreen presentation

The result is intended to feel like an actual small product rather than a developer demo.

---

# 🚀 Running Locally

Because LMS is a static HTML application, there is no build system required.

### Option 1 — Open directly

Open the HTML file in a modern browser.

### Option 2 — Local HTTP server

For more consistent browser behavior:

```bash
python -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

---

# ☁️ GitHub Pages Deployment

LMS is suitable for GitHub Pages because it does not require a traditional backend.

1. Create a GitHub repository.
2. Put the application in `index.html`.
3. Push the repository.
4. Open **Settings → Pages**.
5. Deploy from the desired branch.
6. Open the generated GitHub Pages URL.

Current project demo:

**https://r2dapps.github.io/LocalMovieSync/**

---

# 🗺️ Roadmap

Potential next-generation improvements:

- [ ] TURN relay fallback for difficult WebRTC networks
- [ ] Better browser compatibility detection
- [ ] PWA/offline asset packaging
- [ ] More robust audio-track selection
- [ ] Better media metadata extraction
- [ ] Persistent IndexedDB library metadata
- [ ] Drag-and-drop queue management
- [ ] Theatre themes
- [ ] Guest connection quality indicators
- [ ] Host bandwidth/connection diagnostics
- [ ] Better mobile fullscreen controls
- [ ] Optional local FFmpeg/WASM media tooling

---

# 🏆 Project Philosophy

LocalMovieSync is deliberately built around a simple idea:

> **Your movies should remain yours.**

The browser becomes the cinema.

The host becomes the screening room.

WebRTC becomes the connection between viewers.

And the project stays small enough to understand, modify and deploy without a backend stack.

---

## 📄 License

MIT License.

---

<p align="center">
  <strong>LocalMovieSync — Your files. Your browser. Your theatre.</strong>
</p>
