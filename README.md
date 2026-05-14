# live-sub LiveSub — Live Speech Subtitles Chrome Extension

Talk on any video call. See your words appear on screen in real time — floating, draggable, fully transparent.


What It Does
LiveSub is a Chrome extension that listens to your microphone and displays live subtitles as a floating overlay on top of any website or video call platform. No setup, no server, no account. Just click the icon and speak.
The subtitle box floats above everything — Google Meet, WhatsApp Web, Zoom, Discord, Teams, or any other tab — and shows your speech as you talk, word by word.

Features

Live real-time subtitles — words appear as you speak, with interim (live) text shown in italic and confirmed words shown in full
Works on any platform — Google Meet, WhatsApp Web, Zoom, Microsoft Teams, Discord, Skype, or any browser tab
Fully transparent background — the overlay does not block the content underneath; you can see your call clearly
Draggable subtitle box — hover the box to reveal the drag handle, then move it anywhere on your screen
9-position snap grid — instantly snap the box to any corner, edge, or center of the screen from the popup
Customizable text size — slider from 14px to 48px
7 text color choices — white, green, amber, red, blue, pink, lime
4 background styles — None (fully transparent), Dark (semi-black), Blur (frosted glass), Shadow (text shadow only)
17 language options — English, Bengali, Hindi, Arabic, Chinese, Spanish, French, German, Japanese, Korean, and more
One-click OFF button — stop and clear subtitles instantly from the popup or from the ✕ on the overlay itself
100% local — uses the browser's built-in Web Speech API; no audio is uploaded anywhere


How It Works
Architecture
The extension has four parts:
FileRolemanifest.jsonDeclares permissions, content scripts, popup, and background workerpopup.html / popup.jsThe settings UI that appears when you click the extension iconcontent.js / content.cssInjected into every page; creates the overlay and runs speech recognitionbackground.jsService worker that re-injects the content script after page reloads
Speech Recognition
LiveSub uses the Web Speech API (webkitSpeechRecognition) built into Chromium browsers. This API:

Accesses your microphone locally
Sends audio to Google's speech servers for transcription (standard browser behavior, same as voice search)
Returns both interim results (words still being spoken) and final results (confirmed text)
Automatically restarts when the recognition session ends (every ~60 seconds by default in Chrome)

The extension sets continuous: true and interimResults: true so the subtitle updates word-by-word in real time without gaps.
Overlay Injection
When you click ON, the popup sends a message to the active tab's content script via chrome.tabs.sendMessage. The content script:

Creates a <div> with position: fixed and z-index: 2147483647 (maximum possible, always on top)
Applies your chosen font size, color, and background style
Starts the SpeechRecognition instance
Updates the overlay text on every onresult event

The overlay is injected directly into the page's DOM but styled to not interfere with the page layout — it floats above everything using fixed positioning.
Drag System
The drag handle (visible on hover) uses mousedown / mousemove / mouseup events to track cursor delta and reposition the overlay. Touch events (touchstart, touchmove, touchend) are also supported for touchscreen devices. After dragging, the new position is saved to chrome.storage.local so it persists across tabs.
Settings Persistence
All settings (font size, color, background, position, language, on/off state) are stored in chrome.storage.local. Every change in the popup is saved instantly and sent to the active tab via message passing so the overlay updates live without needing a page refresh.

Technologies Used
TechnologyPurposeChrome Extensions Manifest V3Extension framework, permissions, message passingWeb Speech API (SpeechRecognition)Real-time speech-to-text, built into Chromechrome.storage.localPersisting user settings across sessionschrome.tabs.sendMessageCommunication between popup and content scriptchrome.scriptingDynamic script injection when neededHTML / CSS / JavaScript (Vanilla)All UI — no frameworks usedCSS position: fixed + z-index: maxOverlay always on top of any websiteMouse & Touch Events APIDraggable subtitle boxGoogle FontsShare Tech Mono + Outfit for popup UI

Supported Platforms
LiveSub works on any browser tab. Tested specifically on:

Google Meet (meet.google.com)
WhatsApp Web (web.whatsapp.com)
Zoom Web (zoom.us)
Microsoft Teams Web (teams.microsoft.com)
Discord (discord.com)
Skype Web (web.skype.com)
Any other website or video call platform


Installation

Download and unzip LiveSub-extension.zip
Open Chrome and go to chrome://extensions
Enable Developer mode (toggle in the top right)
Click Load unpacked
Select the unzipped LiveSub folder
Pin the extension from the puzzle icon in the Chrome toolbar


Requires Chrome or any Chromium-based browser (Edge, Brave, Opera, Arc).
Does NOT work on Firefox (different extension API).


Usage

Open any video call tab (Google Meet, WhatsApp Web, etc.)
Click the LiveSub icon in the toolbar
Toggle the switch to ON
Allow microphone access when Chrome asks
Start speaking — subtitles appear on screen
Hover the subtitle box to reveal the drag handle and move it anywhere
Adjust size, color, background, and position from the popup anytime
Click Stop & Clear or toggle OFF to stop


File Structure
LiveSub/
├── manifest.json       ← Extension config (MV3)
├── popup.html          ← Settings UI markup
├── popup.js            ← Settings logic + message passing
├── content.js          ← Overlay creation + speech recognition
├── content.css         ← Overlay styles (injected into pages)
├── background.js       ← Service worker for tab reload handling
└── icons/
    ├── icon16.png
    ├── icon32.png
    ├── icon48.png
    └── icon128.png

Privacy

No audio is recorded or stored by this extension
No data is sent to any third-party server by this extension
Speech is processed by Chrome's built-in Web Speech API (same as Google voice search)
Settings are stored locally on your device only via chrome.storage.local


Known Limitations

Requires Chrome or Chromium — Firefox is not supported
Microphone must be allowed in browser settings
Speech recognition quality depends on your microphone and internet connection (the Web Speech API sends audio to Google's servers)
On some heavily sandboxed pages (e.g. chrome:// pages, Chrome Web Store) the content script cannot be injected — this is a Chrome security restriction
Recognition auto-restarts every ~60 seconds (Chrome's session limit); there may be a brief 0.3s gap


Built with the Web Speech API + Chrome Extensions MV3.
No frameworks. No backend. No account needed.
