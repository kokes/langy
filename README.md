# LANGY

A playful, **100% client-side** page for teaching kids English **reading and listening**. All on-screen text is **UPPERCASE** so letters and words look the way kids learn them. Tap cards to hear words and play a simple listening game—no server, no API keys, no accounts.

Open `index.html` in a browser (or serve the folder locally). Everything runs in the tab.

## How speech works (client-side only)

Langy uses the browser’s built-in [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API): `window.speechSynthesis` and `SpeechSynthesisUtterance`. The device synthesises speech locally; audio never leaves the machine.

```javascript
function speak(text, { rate = 0.9, pitch = 1.05 } = {}) {
  const synth = window.speechSynthesis;
  synth.cancel();

  const utter = new SpeechSynthesisUtterance(text);
  utter.rate = rate;
  utter.pitch = pitch;
  utter.volume = 1;

  const voices = synth.getVoices();
  const voice =
    voices.find((v) => v.name === savedVoiceName) ||
    voices.find((v) => /^Samantha|^Karen|^Daniel|^Alex|^Moira|^Tessa/i.test(v.name) && v.lang.startsWith("en")) ||
    voices.find((v) => v.lang.startsWith("en"));

  if (voice) utter.voice = voice;
  synth.speak(utter);
}

document.getElementById("card").addEventListener("click", () => {
  speak("Cat");
});
```

Important details for a kid-friendly app:

- **User gesture required** — especially on iPhone/iPad Safari, speech must start from a tap/click (card buttons, not autoplay on load).
- **Wait for voices** — `speechSynthesis.getVoices()` is often empty until the `voiceschanged` event; Langy picks a voice after that.
- **Cancel before speaking again** — call `speechSynthesis.cancel()` so rapid taps do not queue a long backlog.
- **Slower rate** — `rate` around `0.85–0.95` is easier for learners than default `1.0`.
- **Display vs speech** — the UI shows **UPPERCASE**; TTS still speaks normal words (`cat`, `A`, `5`) so it sounds natural.

### Voice quality (honest expectations)

| Browser | What you get |
|--------|----------------|
| **Safari (macOS / iOS)** | Only Apple’s compact Web Speech voices. Premium, Enhanced, and Siri voices are **not** exposed to websites—quality is limited but fine for practice. |
| **Chrome / Edge / Arc (macOS)** | Can use **Enhanced** voices you download in System Settings → Accessibility → Spoken Content → Voices. Best option while staying 100% client-side. |
| **Cloud TTS** | Not used here. Langy deliberately avoids network TTS so it stays private, offline-friendly, and zero-config. |

Langy hides novelty voices (Eloquence, etc.) and prefers clear English defaults like **Samantha**, **Karen**, or **Daniel**. Grown-ups can pick another voice in **GROWN-UP SETTINGS**.

## Run locally

```bash
open index.html
# or
python3 -m http.server 8080
# then visit http://localhost:8080
```

## What’s in the app

- **READ** — tap cards to see big **UPPERCASE** text and hear the word.
- **LISTEN** — tap **▶ PLAY**, hear a word, then pick the matching card; earn stars.

**10 big topics** in a grid — one tap (LETTERS, NUMBERS, ANIMALS, FOOD, …). **370+** cards in `lessons.js`.

- **GROWN-UP SETTINGS** — voice and speed (saved in `localStorage` on the same device).

Add more words in `lessons.js`: each line is `["word", "emoji", "SHORT UPPERCASE LINE"]` inside a `pack([...])` list.

## Privacy

No analytics, no external TTS, no cookies. Optional `localStorage` keys: `langy-voice`, `langy-rate`.

## Browser support

Works anywhere `speechSynthesis` exists (Safari, Chrome, Firefox, Edge). For the nicest **local** voices on a Mac, use Chrome with Enhanced voices installed; Safari is still supported for iPads and quick demos.
