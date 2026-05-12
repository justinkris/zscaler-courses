# Zscaler Courses — Study Guide Project

Personal study-guide pages for Zscaler certification courses (EDU-110, EDU-111, EDU-220, EDU-270, EDU-280). Every page is a self-contained HTML file with localStorage-backed interactivity (notes, questions, checklists, saved answers).

---

## Folder layout

```
zscaler-courses/
├── Zscaler AI Courses/              # EDU-110, EDU-111
├── Zscaler Data Security Courses/   # EDU-220
├── Zero Trust Automation Courses/   # EDU-270
└── Zero Trust Branch Courses/       # EDU-280
```

Each course has a hub page (`edu-XXX-hub.html`) and section pages organised by topic folders. Section folders may contain reference screenshots, generated diagrams (`1.png`, `2.png`, etc.), and the section's HTML file.

---

## Design system

- **Fonts**: Fraunces (serif headings/quotes), Epilogue (sans-serif body)
- **Layout**: 2-column grid (`main{display:grid;grid-template-columns:1fr 320px}`) — content left, sticky sidebar right
- **Header**: dark `background:var(--text)` with breadcrumb, animated dot, big serif title
- **Per-course theme colour** (set as CSS variables in `:root`):
  - EDU-110 / EDU-270 intro: blue (`--blue:#1a6dcc`)
  - EDU-111 Defenders / EDU-220 intro / EDU-270 platform: green / blue depending on page
  - EDU-111 Defence Strategy / EDU-270 platform: green (`--green:#1a7c4a`)
  - EDU-111 Attackers / EDU-220 Incident Mgmt: red (`--red:#b91c1c`)
  - EDU-111 Data Risk / EDU-220 Cloud-Endpoint / EDU-270 troubleshoot: amber (`--amber:#b45309`)
  - EDU-111 Recap / EDU-220 SaaS / EDU-270 architecture & recap / EDU-280 summary: purple (`--purple:#5c35cc`) — teal for EDU-280 S11
- **Sidebar elements**: hub link, lesson nav, progress checklist (localStorage-persisted), dark `.quick-ref` term cards
- **Footer**: footer bar with course identifier

---

## Required components for every new study-guide page

When building a new page, include **all** of these in order:

### 1. Header + breadcrumb + motivation banner
Dark `<header>` with breadcrumb back to the course hub, eyebrow chip with animated dot, Fraunces `<h1>` with italic em accent. Then a `.motivation-banner` with a Fraunces tagline.

### 2. Q-panel (collapsible "My Questions")
Sticky panel at the top of `<main>`. Users add questions per section; questions persist in localStorage and group by section name.

### 3. Sticky section nav
Top-of-content tab strip — one button per `.section-block` for smooth-scroll navigation.

### 4. Section blocks
Each topic is a `<div class="section-block" id="section-X">` containing `<h2>` / `<h3>` / `<p>` / lists / callouts / diagram embeds.

### 5. **Socratic "Think First" block — BEFORE every section-block**
Pattern:
```html
<div class="socratic-block">
  <div class="socratic-header">
    <span class="socratic-icon">💭</span>
    <span class="socratic-label">THINK FIRST · [TOPIC]</span>
  </div>
  <p class="socratic-insight">[2-3 sentence partial insight — frames the topic, leaves room for the reader to reason]</p>
  <ul class="socratic-questions">
    <li>[Q1 — conceptual / first principles]</li>
    <li>[Q2 — tradeoff or edge case]</li>
    <li>[Q3 — application or "what if"]</li>
  </ul>
  <div class="answer-area" data-section="UNIQUE-ID">
    <textarea class="answer-textarea" placeholder="Write your own thoughts here before revealing the model answer..." rows="3"></textarea>
    <div class="answer-buttons">
      <button class="btn-add-answer" onclick="saveSocAnswer(this)">+ Add My Answer</button>
      <button class="btn-see-answer" onclick="revealSocAnswer(this)" disabled>🔒 See Model Answer</button>
    </div>
    <div class="model-answers" style="display:none;">
      <div class="model-answer-item">
        <div class="model-answer-q">Q1 — MODEL ANSWER</div>
        <div class="model-answer-text">[2-4 sentence instructor answer explaining reasoning, not just facts]</div>
      </div>
      <!-- Q2 and Q3 follow same pattern -->
    </div>
  </div>
</div>
```
The "See Model Answer" button is **locked until the user submits their own answer** — this is a deliberate learning mechanic.

### 6. **Analogy box — inside every section, after main content**
```html
<div class="analogy-box">
  <div class="analogy-label">Analogy</div>
  <div class="analogy-text">[Vivid real-world parallel that maps the abstract concept to something everyday — hotel keys, airport security, drive-throughs, etc.]</div>
  <!-- Followed by a collapsed image-prompt that visualises this analogy as a flat illustration scene -->
</div>
```

### 7. **Mental note block — inside every section, after the analogy**
```html
<div class="mental-note-block">
  <div class="mental-note-block-icon">🧠</div>
  <div class="mental-note-block-inner">
    <div class="mental-note-block-label">MENTAL NOTE</div>
    <div class="mental-note-block-text">[Condensed exam-ready takeaway — key facts, numbers, relationships. 2-3 sentences.]</div>
  </div>
</div>
```

### 8. **Image placeholder — for 2-3 sections per page**
Choose sections with the most visual concepts (flows, comparisons, architectures). Pattern:
```html
<div class="img-display-block">
  <img src="N.png" alt="Diagram N" onerror="this.style.display='none';this.nextElementSibling.style.display='block';">
  <div class="img-missing-msg" style="display:none;">📷 <strong>N.png</strong> will appear here once you add it to this folder</div>
  <button class="prompt-toggle" onclick="togglePrompt(this)" type="button">▾ Show image generation prompt</button>
  <div class="prompt-content" hidden>[Flat (theme-colour) minimalist illustration of... no text labels]</div>
</div>
```
- `N.png` is sequential per page (1.png, 2.png, ...). Drop the actual image into the section folder when ready.
- Image-gen prompts should specify flat, minimalist style with the page's theme colour, and end with "no text labels".

### 9. Per-section "lesson-tools" — Q&A + notes
Below each section's main content, a `.lesson-tools` grid with two textareas: "Ask a Question" (feeds the Q-panel) and "My Notes" (free-form, autosaves).

### 10. Sidebar
- Hub link, lesson nav, progress checklist (toggleable, localStorage-persisted)
- Dark `.quick-ref` cards for key terms

### 11. Footer
Course identifier + section label.

### 12. Scripts
Standard set of helpers — keep `PAGE_KEY` unique per file. Include:
- `navTo()` — smooth-scroll between sections
- `toggleDone()` — checklist persistence
- `toggleQPanel()` + `renderQPanel()` + `addQuestion()` + `deleteQuestion()` — Q-panel state
- `initNotes()` — textarea autosave
- `saveSocAnswer()` + `revealSocAnswer()` — Socratic answer mechanic
- `togglePrompt()` — image-prompt collapse toggle
- `DOMContentLoaded` handler that rehydrates everything from localStorage

---

## Master prompts file

All image generation prompts (both the diagram prompts and the analogy-visualisation prompts) are aggregated in:

```
zscaler-courses/zscaler-image-prompts.txt
```

When adding new prompts to any page, **also append them** to this file under the appropriate course/page heading so the user can grab prompts without searching through HTML.

---

## Tone & writing conventions

- **Socratic questions** must guide reasoning, not just test recall. Make at least one question challenge an assumption or surface a tradeoff.
- **Analogies** must be vivid and concrete — drive-throughs, hotel keys, airports, libraries, assembly lines.
- **Mental notes** prioritise what's likely to appear on an exam — numbers, ordered lists, key relationships.
- **Image prompts** are scene-based and detailed (multiple labelled sub-scenes when the concept has parts), always flat / minimalist / no text labels, in the page's theme colour.
- **Model answers** are 2-4 sentences, instructor-level, explain *why* rather than just *what*.
- Do NOT add emojis unless the user has explicitly asked, and stick to the small set already in use (💭 🧠 📷 🔒 💡 ✓ ▾ ▴).

---

## Status snapshot

### Completed
- **EDU-280** (Zero Trust Branch, 10 sections + Section 11 summary): full study guides with Socratic blocks, analogies, mental notes, embedded images, and answer feature on S11.
- **EDU-270** (Zero Trust Automation, 5 sections): Socratic + analogies + mental notes + image placeholders + answer feature on all pages.
- **EDU-110** (Intro to AI, 1 page): Socratic + analogies + mental notes + image placeholders + answer feature.
- **EDU-111** (Cybersecurity AI, 5 pages): Socratic + analogies + mental notes + image placeholders. *Answer feature being added in background batch.*
- **EDU-220** (Data Security, 6 pages): Socratic + analogies + mental notes + image placeholders. *Answer feature being added in background batch.*

### In flight
- Image-placeholder → `<img src="N.png">` + collapsible prompt transformation across all 18 pages (Agent A in progress on first 7).
- Single master `zscaler-image-prompts.txt` aggregating every diagram prompt.

### Queued (next pass)
- Add a "Show analogy image prompt" collapse under every `.analogy-box` with a detailed scene-style visual prompt for that specific analogy.
- Append those analogy prompts to `zscaler-image-prompts.txt` under a new `ANALOGY IMAGE PROMPTS` section.

---

## When the user asks to create a new study-guide page

Default to the **full anatomy above** unless told otherwise. Use the page's course-appropriate theme colour. For each section in the new page:

1. Write the section content first (h2/h3/p/lists/callouts).
2. Compose a Socratic Think-First block to sit *before* the section.
3. Inside the section, add an analogy box, a mental note, and (if visually rich) an image placeholder with sequential filename.
4. Write 3 model answers for the Socratic questions and wire them into the answer-area.
5. After all sections, register the page in its course hub.
6. Append every image prompt to `zscaler-image-prompts.txt` under the new page's heading.

If a single component would be skipped, surface that decision to the user rather than silently omitting it.
