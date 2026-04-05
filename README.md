# Spanish Word Cards

A kids' Spanish vocabulary learning app. Shows flashcards with Spanish, English, and Chinese translations, emoji images, and pronunciation audio. Works on iPhone, iPad, Mac, Chromebook, and Android via a GitHub Pages URL — no app store, no installation.

---

## What It Does

- **Word list** — all vocabulary in a searchable table showing emoji, Spanish, English, and Chinese
- **Flashcards** — tap any word to see a large flashcard with the Spanish word and emoji
- **Pronunciation** — tap the mouth button to hear the word spoken aloud (Spanish, English, or Chinese)
- **English / Chinese panels** — tap the language buttons on a flashcard to reveal the definition or translation
- **Add words** — type an English word, auto-fetch Spanish/Chinese translations and an English definition, pick an emoji, and save
- **Delete words** — swipe into edit mode and tap the red × on any row

---

## Project Structure

```
web_cards/
├── index.html          # The entire web app (HTML + CSS + JS)
├── data.js             # Word data loaded by index.html
├── data.json           # Same data in JSON format (reference copy)
├── words.txt           # Source word list (one English word per line)
├── generate_data.py    # Script that reads words.txt and generates data.js
├── requirements.txt    # Python dependencies
└── standalone.html     # Auto-generated single-file version (for offline use)
```

---

## One-Time Setup

### 1. Clone the repo

```bash
git clone git@github.com:abayax/web-cards.git
cd web_cards
```

### 2. Install Python dependencies

```bash
pip install -r requirements.txt
```

Dependencies: `anthropic`, `gTTS`, `requests`

### 3. Configure SSH for GitHub (if not already done)

The repo uses SSH for pushing. If you haven't set this up:

```bash
# Generate a new SSH key (use a different filename to avoid overwriting existing keys)
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519_ext_github

# Add to ~/.ssh/config:
# Host github.com
#   HostName github.com
#   User git
#   IdentityFile ~/.ssh/id_ed25519_ext_github

# Add the public key to GitHub → Settings → SSH and GPG keys
cat ~/.ssh/id_ed25519_ext_github.pub
# Copy the output and paste it into GitHub
```

---

## Accessing the App

### On iPhone / iPad (anywhere, no Mac needed)

Open Safari and go to:
```
https://abayax.github.io/web-cards/
```

**Tip:** tap Share → Add to Home Screen for a shortcut that opens like an app.

### On Mac (local, no internet needed)

Open the file directly:
```bash
open /path/to/web_cards/index.html
```

Or serve locally so your iPhone can also connect over WiFi:
```bash
cd web_cards
python -m http.server 8080
# Then on iPhone Safari: http://YOUR_MAC_IP:8080
# Find your IP with: ipconfig getifaddr en0
```

---

## Updating the Word List

There are two ways to add or remove words: via the **browser UI** (quick, on any device) or via the **Python script** (batch, AI-generated content).

---

### Method 1 — Browser UI (iPhone or Mac)

Use this for adding or deleting individual words on the fly. Changes are saved to the browser's local storage on that device.

**To add a word:**
1. Open the app → tap **Edit** (top right)
2. Tap **+ Add Word**
3. Type the English word → tap **Look Up**
   - Spanish and Chinese are auto-translated via Google Translate
   - English definition is auto-fetched from Free Dictionary API
4. Pick an emoji (tap 😊 to open the picker, or type one)
5. Tap **Save Word**

**To delete a word:**
1. Tap **Edit** → tap the red **×** on any row → confirm

**To make changes permanent (sync to GitHub):**

Changes made in the browser are stored locally on that device only. To push them to GitHub so all devices see them:

1. Tap **Edit** → tap **Export data.js**
2. A `data.js` file is downloaded to your Mac
3. Replace `web_cards/data.js` with the downloaded file
4. Push to GitHub:
   ```bash
   git add data.js
   git commit -m "Update word list"
   git push
   ```
5. The live site updates within a minute

---

### Method 2 — Python Script (batch, AI-generated)

Use this when adding many words at once. The script uses Claude AI to generate translations, definitions, and emoji for every word in `words.txt`.

**Step 1 — Edit words.txt**

Add one English word per line:
```
apple
elephant
hello
butterfly
rainbow
```

**Step 2 — Run the script**

If on an Apple corporate machine (uses internal proxy):
```bash
ANTHROPIC_BASE_URL=http://localhost:5522 python generate_data.py
```

If using a personal Anthropic API key:
```bash
ANTHROPIC_API_KEY=sk-ant-... python generate_data.py
```

The script generates:
- `data.json` — word data in JSON format
- `data.js` — same data as a JavaScript file loaded by the app
- `standalone.html` — a self-contained single-file version of the app

**Step 3 — Push to GitHub**

```bash
git add data.js data.json standalone.html
git commit -m "Add new words: butterfly, rainbow"
git push
```

The live site at `https://abayax.github.io/web-cards/` updates within ~1 minute.

---

## How Pronunciation Works

Audio is generated on-the-fly by the browser's built-in **Web Speech API** — no audio files are downloaded or stored.

| Language | Voice code | Notes |
|---|---|---|
| Spanish | `es-ES` | Available on all platforms |
| English | `en-US` | Uses dictionary audio URL when available, falls back to TTS |
| Chinese | `zh-CN` | Requires Chinese voice pack; may need to be installed on Android |

The animated mouth SVG on each button pulses while audio plays.

---

## How Data Is Stored

| Data | Where stored | Persists across devices? |
|---|---|---|
| Base word list | `data.js` in GitHub repo | ✓ Yes |
| Words added via browser | Browser `localStorage` | ✗ Device only |
| Words deleted via browser | Browser `localStorage` | ✗ Device only |

The app merges both sources at load time: `data.js` words + locally added words, minus locally deleted words.

To make local changes permanent, use **Export data.js** and push to GitHub (see Method 1 above).

---

## Technical Notes

- **No backend, no server** — the app is 100% static HTML/CSS/JS
- **GitHub Pages** hosts the app for free from the `main` branch
- **Emoji picker** is fully self-contained (no CDN dependencies)
- **Translations** use the unofficial Google Translate API (no key required)
- **Definitions** use the Free Dictionary API (`api.dictionaryapi.dev`, no key required)
- **AI generation** (batch script only) uses the Anthropic Claude API (`claude-sonnet-4-6`)
