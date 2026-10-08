# StudyBuddy 3D

**Your notes. Your AI tutor. Your exam simulator.**

Upload a PDF, ask questions, get answers grounded in your notes, take a 5-question exam, find your weak topics and review them with 3D flashcards.

> Demo GIF placeholder: add `demo.gif` here after you record one.

## Project files

```
StudyBuddy-3D/
├── index.html   page structure
├── style.css    dark futuristic 3D design
├── script.js    all the logic (PDF, search, AI, exam, flashcards, 3D background)
└── README.md    this file
```

Built with only HTML, CSS, vanilla JavaScript, [Three.js](https://threejs.org) and [pdf.js](https://mozilla.github.io/pdf.js/) (both loaded from cdnjs). No npm, no backend, no database.

## Features

- **PDF upload** with drag and drop, page-by-page reading and clear error messages.
- **Grounded chat**: answers come only from your notes and always show `Source: Pages …`.
- **Retrieval Confidence meter** on every answer.
- **English, Hindi and Hinglish** questions (a small local word list helps match them to English notes).
- **Exam Mode**: exactly 5 multiple-choice questions, green pop-forward for correct answers, a shake for wrong ones, explanations, final score and weak topics.
- **3D flashcards** with a real flip, and spaced repetition at **1, 3 and 7 days**.
- **3D design**: Three.js particles and shapes that follow your mouse, tilting cards, buttons that push in, 3D tab transitions.
- Respects `prefers-reduced-motion`.

## How it works (RAG workflow)

```
PDF → pdf.js → ~500-word chunks (90-word overlap) + page numbers
    → BM25 search (written in plain JavaScript)
    → top 4 chunks → confidence check
         ├─ too low → "I don't know based on your uploaded notes." (no AI call)
         └─ good    → Claude answers only from those chunks + page citations
    → 5-question exam → weak topics → 3D flashcards → 1/3/7-day review
```

The confidence number is the best BM25 score divided by the best score a perfect chunk could reach, so it runs from 0 to 100%. You can change the threshold in **Settings**.

## Run it on your computer

1. Put the four files in one folder.
2. Open `index.html` in Chrome, Edge or Firefox. If something does not load, start a tiny local server instead:
   ```
   python -m http.server 8000
   ```
   then open <http://localhost:8000>.
3. Open **Settings**, paste your Anthropic API key, press **Save**.
4. Upload a PDF and start asking.

You need an internet connection (the libraries and the AI come from the web).

### Using it as a Claude Artifact

Inside a Claude Artifact no API key is needed and saving uses `window.storage`. An Artifact must be a single file, so copy the contents of `style.css` into a `<style>` tag and `script.js` into a `<script>` tag inside `index.html`, then paste that one file into Claude.

### Deploying to GitHub Pages

1. Push the files to a GitHub repository (branch `main`).
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick `main` and `/ (root)`, then Save.
4. After a minute your site is live at `https://<your-name>.github.io/<repo-name>/`.

## API key security

**Never commit your API key to GitHub.** The key is never written in the code. You type it into Settings and it stays in your browser only. Anyone who can open your browser tools on a shared computer can see it, so use a key with a spending limit and clear it (Settings → Clear key) when you are done. For a public website, use a backend that keeps the key secret instead.

## Limitations

- **Scanned PDFs** (pictures of pages) have no text, and OCR is not included.
- Search matches words, not meaning, so a question using very different words from your notes may get "I don't know". Lower the threshold in Settings if it refuses too often.
- Hindi/Hinglish support is a small word list, not full translation.
- Exams and flashcards are written by an AI from your notes, so skim them for mistakes.
- Flashcards and settings are only saved where `window.storage` exists (Claude Artifacts). On a normal website they last until you close the tab, and the app tells you so.
- The loaded PDF is not saved. Upload it again next time.
