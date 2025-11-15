You are an expert front-end developer. Build a modern, responsive single-page website that runs entirely in the browser and provides high-quality text-to-speech for user-provided text using the built-in Web Speech API (`window.speechSynthesis`). No backend, no external TTS APIs.

====================
HIGH-LEVEL GOAL
====================
Create a clean, modern web app where a user can:
- Paste or type text into a large text area
- Press a button to have that text read aloud with text-to-speech
- Control playback (Play, Pause/Resume, Stop)
- Optionally choose a voice and adjust speaking speed (rate)
The website must work well on both desktop and mobile.

ALL text-to-speech must be implemented using the browser’s built-in `SpeechSynthesis` / `SpeechSynthesisUtterance` APIs (the Web Speech API). Do NOT use any external or paid TTS providers.

====================
TECH & STRUCTURE
====================
- Use plain HTML, CSS, and JavaScript (no frameworks or build tools).
- All logic should run in the browser.
- Deliver a single `index.html` file that includes:
  - `<head>` section with meta tags, title, and embedded CSS (or separate CSS file if you prefer, but keep it simple).
  - `<body>` with the app UI and embedded `<script>` for JavaScript (or separate JS file).
- The site should be fully functional by simply opening `index.html` in a browser.

====================
LAYOUT & UI REQUIREMENTS
====================
Overall layout:
- A centered, responsive container with a max-width (e.g. 900px) for readability.
- Use flexbox or CSS grid to create a layout that looks good on both desktop and mobile.

Sections:
1. **Header**
   - App title, e.g. “Text Voice Reader”.
   - Short subtitle/description, e.g. “Paste your text and listen instantly using built-in text-to-speech.”

2. **Main Card / Panel**
   - A card-style container with:
     - A label and a large `<textarea>` for the input text.
     - A small character count beneath the textarea.
     - A row of controls (buttons and selectors) for TTS.
     - A status area for messages.

3. **Footer**
   - Small, subtle text such as “Runs fully in your browser using the Web Speech API.” 
   - Optionally a link or placeholder for “About” or “GitHub” (no need to implement).

====================
TEXT AREA REQUIREMENTS
====================
- Large multi-line `<textarea>` for user text input:
  - Minimum height: around 200–250px.
  - Full width of container.
  - Rounded corners and subtle border.
  - Allow resizing vertically but ensure it doesn’t break the layout.
- Placeholder text explaining usage, like:
  - “Paste or type your text here, then press Play to listen.”
- Show a live **character count** below the textarea, e.g. “Characters: 123”.
  - Update as the user types.

====================
TTS CONTROLS & BEHAVIOR
====================
Implement the following controls below the textarea:

Buttons:
1. **Play**
   - Starts reading the current text from the beginning.
   - If there is already an utterance in progress, `Stop` the current one before starting a new one.
   - Disabled when there is no text.

2. **Pause / Resume**
   - If the speech is currently playing, pause it (`speechSynthesis.pause()`).
   - If the speech is paused, resume it (`speechSynthesis.resume()`).
   - The button text should toggle between “Pause” and “Resume” based on state.
   - Disabled when there is no active utterance.

3. **Stop**
   - Immediately stop speaking (`speechSynthesis.cancel()`).
   - Reset the Pause/Resume state back to “Pause”.
   - Disabled when there is no active utterance.

4. **Clear Text** (optional but preferred)
   - Clears the textarea.
   - Resets character count and any TTS state.

Dropdowns / Sliders:
1. **Voice Selector**
   - Use `speechSynthesis.getVoices()` to populate a `<select>` with available voices.
   - Display each voice as: “Voice Name (Language) [Default?]”.
   - If voices are not immediately available on first load, handle the `voiceschanged` event to repopulate.
   - Remember the user’s selected voice for the current session (e.g. via a variable).

2. **Rate (Speed) Control**
   - An `<input type="range">` slider to control speaking rate.
   - Reasonable range: 0.5 to 2.0 (default 1.0).
   - Show current rate value in a small label (e.g. “Speed: 1.0x”).
   - Apply the selected rate on the `SpeechSynthesisUtterance` before calling `speak()`.

(Optionally, you can add a Pitch slider with similar behavior, but it is not strictly required.)

Status / Feedback:
- A small text area to show status:
  - “Ready to speak.”
  - “Speaking…”
  - “Paused.”
  - “Finished.”
  - “No text to read.”
  - “Your browser does not support text-to-speech.”
- Update these messages appropriately based on events.

====================
TTS IMPLEMENTATION DETAILS
====================
Use the Web Speech API:

- Check for support:
  - If `window.speechSynthesis` or `SpeechSynthesisUtterance` is not available, show a prominent, friendly warning:
    - e.g. “Your browser does not support the Web Speech API. Please try a recent version of Chrome, Edge, or Safari.”
  - Disable all TTS controls if unsupported.

- Core flow when Play is clicked:
  1. Read the text from the textarea.
  2. If empty, show “Please enter some text to read.” and do nothing further.
  3. Cancel any existing speech (`speechSynthesis.cancel()`).
  4. Create a new `SpeechSynthesisUtterance` with the full text.
  5. Apply the selected voice, if any.
  6. Apply the selected rate (and pitch if implemented).
  7. Set up `onstart`, `onend`, `onerror`, and `onpause` / `onresume` event handlers:
     - `onstart`: update status to “Speaking…” and button states (e.g. disable Play while speaking).
     - `onend`: update status to “Finished.”, re-enable Play, reset Pause/Resume button text.
     - `onerror`: show an error message in the status area.
  8. Call `speechSynthesis.speak(utterance)`.

- Manage a simple state object or variables to track:
  - Whether speech is currently in progress.
  - Whether the speech is paused.
  - The current utterance instance.

- Make sure that:
  - Clicking Stop cancels the utterance and resets state.
  - Clicking Play while already speaking will restart from the beginning.
  - Pausing and resuming work smoothly across desktop and mobile.

====================
RESPONSIVE DESIGN REQUIREMENTS
====================
General:
- The design must be visually appealing and work well on both **desktop** and **mobile**.
- Use a mobile-first approach or responsive breakpoints to adjust layout.

Desktop:
- Centered card with max-width (e.g. 900px).
- Plenty of whitespace and padding.
- Controls aligned nicely in a row with spacing.

Mobile:
- All content should fit within the viewport with comfortable spacing.
- Use a single-column layout.
- Buttons should be large and easy to tap (full-width or nearly full-width).
- Make sure text and controls are not too small (use reasonable font sizes like 16px+).

Typography & Styling:
- Use a clean, sans-serif font stack (e.g. `system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`).
- Apply a light, modern color palette (e.g. soft background, white card, subtle primary accent color for buttons).
- Buttons:
  - Rounded corners.
  - Hover/active styles on desktop.
  - Clear disabled state (dimmed / reduced opacity).
- Inputs (textarea, select, range):
  - Rounded corners.
  - Focus outline for accessibility.
  - Subtle shadows or borders.

====================
ACCESSIBILITY REQUIREMENTS
====================
- All interactive elements must be keyboard accessible:
  - You must be able to tab between textarea, select, sliders, and buttons.
  - Enter/Space triggers buttons.
- Provide visible focus outlines on controls.
- Use semantic HTML:
  - `<header>`, `<main>`, `<footer>` where appropriate.
  - `<label>` elements tied to `<textarea>`, `<select>`, and range inputs.
- ARIA attributes where helpful:
  - Example: status area can have `role="status"` or `aria-live="polite"` to announce changes like “Speaking…” or “Finished.”
- Ensure sufficient color contrast between text and background.

====================
ERROR HANDLING & EDGE CASES
====================
- If the user clicks Play with empty text, show a clear, friendly message in the status area and do nothing else.
- If the selected voice is no longer available (or voices array is empty), fall back to the default voice.
- Handle the case where voices load asynchronously:
  - Use `speechSynthesis.onvoiceschanged` to populate/repopulate the voice selector.
- If `speechSynthesis.speak()` throws or fails, catch the error and show a user-friendly message.

====================
EXTRA POLISH (NICE-TO-HAVE)
====================
If it’s simple to implement within this scope, add:
- A small toggle for light/dark mode (CSS only, or using `prefers-color-scheme`).
- A “Sample Text” button that auto-fills the textarea with an example paragraph so users can try the TTS quickly.

====================
DELIVERABLE
====================
- Provide the complete `index.html` file (and optional separate CSS/JS files if you prefer, but keep everything easy to run locally).
- All code should be clearly structured and commented enough that a non-expert can understand the main pieces:
  - TTS setup
  - UI controls
  - State management
  - Responsive styling
- Ensure that opening `index.html` in a modern browser is enough to use the app with no additional setup.

Build this now.
