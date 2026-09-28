# 🎬 LocalMovieSync (LMS)

> **A private, browser-based local movie player and WebRTC watch
> theatre.**

LocalMovieSync is a single-page movie browser and player designed around
one simple idea:

**Your movies stay on your device --- and when you want to watch
together, the host can stream the screening directly to guests.**

It combines a local, VLC-style movie player with a lightweight private
Watch Party Theatre featuring a host-controlled queue, WebRTC video
streaming, chat, reactions, and a responsive theatre UI.

------------------------------------------------------------------------

## 🎬 What LMS Does

### 🖥️ Local Movie Player

Use LMS as a normal local movie player without joining a party.

-   Load an entire movie folder
-   Add individual video files
-   Browse, search, and sort your library
-   Resume local playback
-   Progress/watch history stored locally
-   Scrubbing and playback controls
-   Playback speed control
-   Volume and mute controls
-   Fullscreen playback
-   Keyboard shortcuts
-   External `.srt` / `.vtt` subtitles
-   Embedded MKV subtitle extraction where supported
-   Multiple audio-track support where the browser exposes native tracks
-   Responsive UI for desktop, tablet, and mobile
-   No movie upload is required for normal local playback

### 🎭 Watch Party Theatre

Start a private theatre and invite others with a party code/link.

The Theatre provides:

-   Host and guest profiles
-   Local movie libraries for each participant
-   Host-controlled queue
-   Guest movie requests
-   Accept / reject queue requests
-   Explicit Play action
-   Synchronized cinematic countdown
-   Host-controlled playback
-   WebRTC video/audio streaming to guests
-   Party chat
-   Live reactions
-   Party activity/audit panel
-   Responsive desktop/tablet/mobile layouts
-   Clean theatre entry and exit flows
-   Custom in-app confirmation dialogs instead of browser alerts

------------------------------------------------------------------------

# 🎭 Watch Party Flow

The Watch Party is intentionally different from ordinary local playback.

``` text
HOST
  │
  ├── Load local movies
  │
  ├── Start Watch Party
  │
  ├── Enter Theatre
  │
  ├── Add movie to Queue
  │
  ├── Select approved Queue item
  │
  ├── Press Play
  │
  ├── 4 → 3 → 2 → 1
  │
  └── Screen starts
             │
             ▼
       WebRTC Media Stream
             │
       ┌─────┴─────┐
       ▼           ▼
    GUEST 1     GUEST 2
```

## Host

The host is the playback authority.

The host can:

-   Add local movies to the queue
-   Accept guest requests
-   Reject guest requests
-   Remove queue items
-   Choose which approved queue item to screen
-   Start the screening
-   Play / pause
-   Seek
-   Skip
-   End the theatre

## Guest

Guests can:

-   See the theatre
-   Browse their own local movie library
-   Request one of their local movies from the host
-   Chat
-   React
-   Watch the host's screening

Guests **cannot control the shared screening**.

A guest's private local playback is independent and cannot pause, seek,
or otherwise control the host's screening.

------------------------------------------------------------------------

# 🎞️ Queue Model

The queue is deliberately explicit.

### Host movie

``` text
Library
   ↓
Add to Queue
   ↓
Queue
   ↓
Host presses Play
```

Adding a movie does **not** automatically start playback.

### Guest movie

``` text
Guest Library
      ↓
Request
      ↓
Host Queue
      ↓
Accept / Reject
      ↓
Approved
      ↓
Host presses Play
```

A guest request is only a request.

The host remains in control of what gets screened.

------------------------------------------------------------------------

# 🔒 Local Playback and Party Playback Are Separate

LMS intentionally maintains two different playback contexts.

## Local Playback

``` text
Local File
    ↓
Blob URL
    ↓
HTML5 Video
    ↓
Local Watch History
```

Local playback can resume from a previously watched position.

## Watch Party Playback

``` text
Queue Item
    ↓
Host starts screening
    ↓
Countdown
    ↓
Host local video
    ↓
captureStream()
    ↓
WebRTC
    ↓
Guests
```

Party playback does **not** reuse local watch-history position.

A movie watched locally for 30 minutes can still begin at `00:00` when
it becomes a new theatre screening.

This separation also prevents a guest watching a private local movie
from affecting the party.

------------------------------------------------------------------------

# 🌐 WebRTC Architecture

LMS uses PeerJS/WebRTC for Watch Party connectivity.

The architecture is a hub-and-spoke model:

``` text
                    ┌───────────────┐
                    │     HOST      │
                    │     HUB       │
                    └───────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              ▼             ▼             ▼
           Guest 1       Guest 2       Guest 3
```

### Data

Party data includes:

-   Chat
-   Reactions
-   Queue requests
-   Queue state
-   Playback synchronization
-   Party state

### Media

The host captures the local `<video>` element and sends its media stream
through WebRTC.

``` text
Host video
    ↓
MediaStream
    ↓
WebRTC
    ↓
Guest video element
```

The movie is **not uploaded to a central movie-storage server**.

However, during a Watch Party the movie media **does leave the host
device**, because it has to be transmitted to the guests.

This is different from normal offline/local playback.

------------------------------------------------------------------------

# 📡 Wi-Fi and Mobile Data

WebRTC can work across:

-   Same Wi-Fi
-   Different Wi-Fi networks
-   Wi-Fi → 4G/5G
-   4G/5G → Wi-Fi
-   4G/5G → 4G/5G

Actual connectivity depends on NAT/firewall/network conditions.

The current architecture uses PeerJS for signaling and WebRTC for the
actual media/data connection.

A future production deployment may add a **TURN server** as a relay
fallback for networks where a direct WebRTC path cannot be established.

------------------------------------------------------------------------

# 💬 Party Chat

Chat is designed to stay available without covering important playback
controls.

Features:

-   Responsive chat card
-   Desktop/tablet/mobile layouts
-   Unread message indicator
-   Enter-to-send
-   Message length limit
-   System activity messages
-   Chat remains accessible in native fullscreen
-   Chat is not stored on a central server

Closing the chat does not remove its access button.

------------------------------------------------------------------------

# 🎉 Reactions

LMS supports quick party reactions such as:

❤️ 😂 🔥 👏 😮 👍 😍 🎉 💯 🤯 🥳 😭 🙌 👀

Reactions are intentionally ephemeral.

A reaction produces a visual burst rather than becoming permanent chat
history.

Conceptually:

``` text
          🎉
      ✨  💥  ❤️
   🎊     💫     🎉
      ✨     💥
          ↑
       reaction
```

------------------------------------------------------------------------

# ⏱️ Synchronized Countdown

Before a host starts a screening, LMS displays a short cinematic
countdown:

``` text
4
3
2
1
```

The countdown uses a shared target timestamp rather than relying on
independent one-second timers.

This helps host and guests start from the same scheduled playback
moment.

The countdown screen is intentionally opaque so the previous movie is
not visible underneath it.

------------------------------------------------------------------------

# 🎬 Player Controls

The local player supports:

-   Play / pause
-   Seek
-   Progress bar
-   ±10 second skipping
-   Volume
-   Mute
-   Playback speed
-   Fullscreen
-   Subtitle selection
-   External subtitle loading
-   Keyboard controls

Playback synchronization is driven from the actual media element for the
host, so playback changes made through:

-   Play button
-   Spacebar
-   Video interaction
-   Seek controls
-   Keyboard controls
-   Other native media changes

can be reflected in the party state.

------------------------------------------------------------------------

# 📝 Subtitles

LMS can work with:

-   `.srt`
-   `.vtt`
-   Embedded MKV subtitle tracks when extraction is available

SRT subtitles are converted to WebVTT for browser playback.

MKV subtitle processing is performed locally in the browser.

Active MKV scanning is cancelled when appropriate so switching movies
does not leave large background readers running.

------------------------------------------------------------------------

# 🔊 Audio Tracks

Browser-native audio-track support is used where available.

Arbitrary MKV multi-audio extraction is a separate future enhancement.

A possible future architecture is:

``` text
MKV
 │
 ├── Video
 ├── Audio Track 1
 ├── Audio Track 2
 └── Audio Track 3
          │
          ▼
    FFmpeg WebAssembly
          │
          ▼
   Selectable audio tracks
```

FFmpeg/WASM is intentionally not part of the current lightweight core
because it would significantly increase the application payload and
complexity.

------------------------------------------------------------------------

# 📱 Responsive Design

The Theatre is designed for:

-   Desktop monitors
-   Laptops
-   Tablets
-   Mobile phones

The interface adapts its layout rather than simply shrinking desktop
controls.

### Desktop

``` text
┌──────────────────────────────────────────────┐
│ Theatre Header                               │
├──────────────────────┬───────────┬───────────┤
│                      │           │           │
│      Library         │   Queue   │   Chat    │
│                      │           │           │
│  □  □  □  □          │           │           │
│  □  □  □  □          │           │           │
│                      │           │           │
└──────────────────────┴───────────┴───────────┘
```

### Mobile

``` text
┌──────────────────────┐
│ Theatre Header       │
├──────────────────────┤
│                      │
│      Library         │
│                      │
│  □  □               │
│  □  □               │
│  □  □               │
│                      │
├──────────────────────┤
│       Queue          │
│                      │
│  Movie A             │
│  Movie B             │
└──────────────────────┘
```

The Theatre owns its scrolling context so the underlying LMS page does
not create competing scrollbars.

------------------------------------------------------------------------

# 🛡️ UX and Safety Details

LMS avoids browser-native disruptive dialogs inside the application.

Instead of:

``` javascript
alert(...)
confirm(...)
```

the application uses:

-   In-app notifications
-   Custom confirmation windows
-   Responsive dialogs
-   Activity messages

Examples include:

-   Close player?
-   Leave theatre?
-   End watch party?
-   Clear watch history?

------------------------------------------------------------------------

# 🧠 Current Architecture

The current project is intentionally a single HTML application.

``` text
LocalMovieSync
│
├── Movie Library
│   ├── Folder loading
│   ├── Individual files
│   ├── Search
│   └── Sorting
│
├── Local Player
│   ├── HTML5 Video
│   ├── Playback controls
│   ├── Watch history
│   └── Subtitles
│
├── Watch Party Theatre
│   ├── Profile name
│   ├── Queue
│   ├── Requests
│   ├── Host controls
│   ├── Countdown
│   └── Activity
│
├── WebRTC
│   ├── PeerJS signaling
│   ├── Data connections
│   └── Media connections
│
├── Party Chat
│   ├── Messages
│   └── Reactions
│
└── Responsive UI
    ├── Desktop
    ├── Tablet
    └── Mobile
```

------------------------------------------------------------------------

# 🚀 Running LMS

LMS is designed to work as a static HTML application.

You can open the HTML locally for normal local playback.

For Watch Party functionality, the browser needs internet access to load
the required online libraries/signaling infrastructure.

For a more reliable deployment, host the HTML from:

-   GitHub Pages
-   Any static web host
-   A local web server
-   Another HTTPS static hosting service

No application backend is required for the core architecture.

------------------------------------------------------------------------

# 🌍 GitHub Pages Deployment

1.  Create a repository.
2.  Add the final HTML file.
3.  Rename it to:

``` text
index.html
```

4.  Commit and push.
5.  Open:

**Settings → Pages** 6. Select:

``` text
Deploy from branch
main
/
```

7.  Save.

GitHub Pages will provide the hosted URL.

------------------------------------------------------------------------

# 🔐 Privacy Model

## Local playback

Movie files are read locally using the browser File API.

They are not uploaded as part of normal playback.

## Watch Party

During a Watch Party:

-   Host video is transmitted to connected guests through WebRTC.
-   Chat travels through the party's peer connections.
-   Reactions travel through the party's peer connections.
-   Queue state travels through the party's peer connections.
-   The signaling service is used to establish connections.
-   There is no central movie library.
-   There is no application database storing movie files.

### Important distinction

**"No upload" does not mean "the movie never leaves the host."**

For Watch Party streaming, the movie necessarily travels from:

``` text
Host → WebRTC → Guest
```

It is not being uploaded into a permanent movie-storage service.

------------------------------------------------------------------------

# ⚠️ Current Limitations

### WebRTC NAT traversal

Some networks may prevent a direct peer-to-peer connection.

A TURN relay can be added later for stronger connectivity across
restrictive networks.

### Browser codec support

The browser still determines which video/audio codecs it can decode.

Some MKV/AVI files may work in VLC but not in Chrome/Edge.

### Multi-audio MKV extraction

Full arbitrary MKV audio-track extraction is not yet implemented through
FFmpeg/WASM.

### Party history

Party chat and reactions are ephemeral.

A user joining later does not receive messages that were sent before
they joined.

------------------------------------------------------------------------

# 🧪 Recommended Test Flow

Before considering a deployment stable, test:

### Local Player

-   [ ] Load folder
-   [ ] Add files
-   [ ] Search
-   [ ] Sort
-   [ ] Play
-   [ ] Pause
-   [ ] Seek
-   [ ] ±10 seconds
-   [ ] Volume
-   [ ] Fullscreen
-   [ ] Subtitle
-   [ ] Switch movies
-   [ ] Resume local playback

### Watch Party

-   [ ] Host starts party
-   [ ] Guest joins
-   [ ] Guest profile appears
-   [ ] Host sees guest
-   [ ] Guest requests movie
-   [ ] Host accepts
-   [ ] Host rejects another request
-   [ ] Queue updates correctly
-   [ ] Host starts screening
-   [ ] Countdown appears
-   [ ] Guest receives video
-   [ ] Host pauses
-   [ ] Guest pauses
-   [ ] Host seeks
-   [ ] Guest follows
-   [ ] Host screens second movie
-   [ ] Chat works during playback
-   [ ] Reactions work
-   [ ] Guest leaves
-   [ ] Host ends party
-   [ ] Guests are returned from the Theatre

### Network

-   [ ] Same Wi-Fi
-   [ ] Different Wi-Fi
-   [ ] Wi-Fi → mobile data
-   [ ] Mobile data → Wi-Fi
-   [ ] Android guest
-   [ ] Desktop guest

------------------------------------------------------------------------

# 🛠️ Future Roadmap

Potential future additions:

-   [ ] FFmpeg WASM multi-audio extraction
-   [ ] TURN server fallback
-   [ ] Persistent party history (optional)
-   [ ] Better theatre ambience
-   [ ] Poster/artwork extraction
-   [ ] Playlist persistence
-   [ ] More advanced queue management
-   [ ] Host handoff
-   [ ] Reconnect/resume after temporary network loss
-   [ ] Better WebRTC quality adaptation
-   [ ] Optional room password
-   [ ] Theatre themes
-   [ ] Per-user avatars

------------------------------------------------------------------------

# 📄 Project Layout

The current application can remain a single-file project:

``` text
LocalMovieSync/
│
├── index.html
└── README.md
```

The HTML contains the application UI, player logic, Theatre, queue,
WebRTC party logic, chat, reactions, and responsive styling.

------------------------------------------------------------------------

# 📜 License

MIT

------------------------------------------------------------------------

## 🎬 LocalMovieSync

**Your library. Your device. Your theatre.**

Built around local-first playback with an optional peer-to-peer Watch
Party experience.
