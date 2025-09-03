## Nexus Visualization (Dual-Core Particle Experience)

A visual, interactive "nexus" linking two cores with flowing particle filaments, glows, and energy arcs.

- Desktop: two browser windows collaborate to render one continuous, cross-window nexus connection
- Mobile: two phones connect peer-to-peer (WebRTC) using Ably Channels for signaling (with manual fallback), each acting as one core


## Features
- Dual-core particle effects with wavy, organic filaments and merge states
- Desktop multi-window mode using BroadcastChannel (no server required)
- Mobile P2P mode (WebRTC DataChannel) with Ably Channels signaling
- Touch/drag to move your core on mobile
- Device-pixel-aware canvas rendering (HiDPI) and performance safeguards


## Quick Demo
- Desktop (GitHub Pages): open nexus_one.html, it will open the second window (nexus_two.html). Move/overlap windows and watch filaments span between them.
- Mobile: open nexus_one.html on two phones, create a room on one and join with the code on the other.

Optionally add screenshots or a GIF here:
- /docs/desktop.jpg
- /docs/mobile.gif


## Setup Instructions

### Prerequisites
- Modern browser (Chrome/Edge/Safari/Firefox)
- Desktop: can be served from local HTTP; ES modules require http/https (not file://)
- Mobile: must be served over HTTPS (WebRTC and some device APIs require secure origin)

### Clone / Download
```bash
# clone
git clone https://github.com/<your-username>/dynamic-multi-window-experience.git
cd dynamic-multi-window-experience
```

### Serve Locally (Desktop & quick testing)
Use any static server. Examples:

Python 3
```bash
python -m http.server 5173
# then visit http://localhost:5173/nexus_one.html
```

Node (npx serve)
```bash
npx serve -l 5173 .
# then visit http://localhost:5173/nexus_one.html
```

VS Code Live Server
- Open folder in VS Code
- Right-click nexus_one.html → "Open with Live Server"

### Configure Ably (Mobile P2P Signaling)
For quick start on the free plan you can embed your Ably API key (testing only). In nexus_one.html, look for:
```html
<script>
  // Quick-start: paste your Ably API key here for testing only.
  // Example format: "APP_KEY:APP_SECRET". For production, use Token Auth via a small server.
  window.ABLY_API_KEY = '';
</script>
<script src="https://cdn.ably.com/lib/ably.min-1.js"></script>
```
Set `window.ABLY_API_KEY` to the key from your Ably dashboard (see guidance below). Deploy or serve over HTTPS.

Production recommendation: Do NOT ship your raw key. Use Ably Token Authentication with a tiny server endpoint that issues short-lived tokens, and initialize the client with `authUrl` or `authCallback`.

### Manual Signaling Fallback
If Ably isn’t configured or fails, a "Manual Fallback" section appears in the mobile panel. You can exchange Offer/Answer by copy/paste to establish WebRTC between two devices.


## Usage Guide

### Desktop Mode
1) Open nexus_one.html in a desktop browser
2) The second window (nexus_two.html) opens automatically (if blocked, click "Launch Nexus 2")
3) Move and resize the two windows; stack/overlap them to see continuous filaments across the boundary
4) The partner core is outlined when overlapping; energy arcs appear in attraction range; cores merge when close

### Mobile P2P Mode
1) Open nexus_one.html on Phone A and Phone B (over HTTPS)
2) On Phone A: in the Mobile P2P panel, tap "Create Room" → share the 6‑character code
3) On Phone B: enter the code and tap "Join"
4) After a moment, status changes to Connected and both phones render each other’s cores

### Touch Controls
- Drag your core: touch and move to reposition your core
- Optional gestures (built-in: drag; extendable: pinch to modulate effects)

### Visual Effects
- Filaments: wavy, directional particles flowing between cores
- Energy arcs: glowing arcs curve between cores in attraction range
- Merge state: when distance < threshold, cores pulse and blend


## Technical Architecture

### File Structure
- nexus_one.html — main experience (desktop controller; mobile P2P UI)
- nexus_two.html — partner window for desktop mode
- nexus_shared.js — shared rendering, particle physics, utilities

### Desktop Inter-Window Link
- Uses BroadcastChannel to exchange window positions, ghost particles, and particle transfer events across windows
- Coordinates are normalized to client-area screen space for seamless boundaries

### Mobile P2P Link
- WebRTC DataChannel for realtime peer-to-peer data (core positions, etc.)
- Ably Channels for signaling (offer, answer, ICE). Channel name: `nexus-<ROOM>` where ROOM is a 6-character code (e.g., AB3F7K)
- Manual fallback signaling if Ably isn’t available

### Shared Rendering & Physics
- HiDPI-aware canvas with CSS-pixel drawing and devicePixelRatio transforms
- Particle system adds turbulence and slight spiral bias for organic motion
- drawCore/drawMergeArcs provide glow and energy ring visuals


## Configuration Options

### Mobile Mode Detection
- Enabled when `matchMedia('(pointer: coarse)')` is true or viewport width is small (e.g., < 850px)

### Performance Settings
- Particle count (default ~120 desktop; mobile uses fewer spawns each frame)
- DPR cap on mobile (e.g., 1.5) for better performance
- Reduce glow passes and line width if needed

### Signaling Options
- Ably: recommended for ease of use and reliability (HTTPS required)
- Manual fallback: always available if Ably is not configured/available


## Troubleshooting

### Blank Screen / CORS on desktop
- Don’t open files with `file://`. Serve over http(s) (see local server instructions). ES modules require http(s).

### Pop-up Blocked (desktop)
- If the second window doesn’t open, click the "Launch" button or allow pop-ups for the site

### Ably Connection Issues
- Ensure you set `window.ABLY_API_KEY` and included the Ably CDN script
- Must be over HTTPS on mobile
- Free plan rate limits may apply; try again after a short while
- If Ably fails, use the manual fallback section to connect

### WebRTC Fails to Connect
- Some networks require TURN for WebRTC. This demo does not include TURN; most home/cellular networks are fine, but strict NATs may fail
- Try a different network or temporarily test via the manual fallback

### Debugging
- Open DevTools console (remote debugging for mobile): look for status messages ("Ably connected", "Answer sent", "Connecting", etc.)
- Ably dashboard: check your app’s metrics and connection logs
- Verify both devices used the exact same room code (codes are case-insensitive, normalized to uppercase)

### Browser Compatibility
- Latest Chrome/Edge/Safari/Firefox recommended. iOS Safari requires HTTPS for WebRTC


## Development

### Code Organization
- Shared logic in `nexus_shared.js` (particle physics, drawing, utilities)
- Desktop window logic in the two HTML files; mobile P2P wiring in `nexus_one.html`

### Extending the Visualization
- Add new particle behaviors in the Particle class (nexus_shared.js)
- Adjust attraction/merge thresholds and color schemes in the HTML configs
- Add more UI controls to the mobile P2P panel (e.g., QR code for room code)

### Performance Notes
- Favor fewer, higher-quality particles over many; reduce tail length and lineWidth on mobile
- Cap DPR on mobile to ~1.5 to reduce fill cost
- Pause/reduce animation when `document.hidden` to save battery


## Ably Beginner Guide

### Where to find your API key
1) Log in to the Ably dashboard
2) Create a new app (or select an existing one)
3) Go to the app → API Keys
4) Copy a key (format: `APP_KEY:APP_SECRET`)

### Free Plan Limits (summary)
- Suitable for small demos and low-rate signaling
- Message/connection caps may apply; see Ably pricing docs for specifics

### Security Best Practices
- Do not ship your master API key in production code. Use Token Authentication via a server endpoint that returns temporary tokens to clients
- Restrict channels and permissions server-side

### Testing With Two Phones
1) Deploy your site over HTTPS (GitHub Pages works)
2) Set `window.ABLY_API_KEY` in nexus_one.html for testing
3) Phone A: Create Room → share the 6‑char code
4) Phone B: Enter code → Join
5) Drag cores on either phone; verify filaments and arcs animate on both

---

Enjoy the nexus experience! If you’d like help adding QR-room joining, Token Auth, or TURN for tougher networks, open an issue or reach out.

