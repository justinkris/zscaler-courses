# Zscaler Courses — Study Guide Project

Personal study-guide pages for Zscaler certification courses. Every page is a self-contained HTML file with localStorage-backed interactivity (notes, questions, checklists, saved answers).

---

## Folder layout

```
zscaler-courses/
├── Zscaler AI Courses/                    # EDU-110, EDU-111
├── Zscaler Data Security Courses/         # EDU-220
├── Zero Trust Automation Courses/         # EDU-270
├── Zero Trust Branch Courses/             # EDU-280
└── Zscaler Digital Delivery Consultant Courses/  # EDU-302
```

Each course has a hub page (`edu-XXX-hub.html`) and section pages organised by topic folders. Section folders contain reference screenshots, descriptively-named embedded screenshots (e.g. `zdx-deployment-steps.png`), and the section's HTML file.

---

## Design system

- **Fonts**: Fraunces (serif headings/quotes), Epilogue (sans-serif body)
- **Layout**: 2-column grid (`main{display:grid;grid-template-columns:1fr 320px}`) — content left, sticky sidebar right
- **Header**: dark navy `background:#0d1a2e` with breadcrumb, animated dot, Fraunces `<h1>` with italic em accent
- **Color theme**: one colour per **course**, applied consistently to every section page. Default is **navy blue** (`#1a6dcc`). Do not rotate colours between sections.
  - Blue `:root` bg: `#eef4ff` · border: `#bfdbfe` · header bg: `#0d1a2e` · chip/accent: `#93c5fd`
  - Secondary callout colours (green/amber/red) are used for semantic callout boxes only — not as page accents
- **Sidebar**: hub link, progress checklist (localStorage-persisted), dark `.quick-ref` term cards (`background:#0d1a2e`)
- **Footer**: course identifier + section label

---

## Required components for every new study-guide page

### 1. Page-transition head script
Very first child of `<head>`:
```html
<script>if(sessionStorage.getItem('pt')){document.documentElement.classList.add('pt-init')}</script>
```
And in CSS: `html.pt-init body{opacity:0;}`

### 2. Header + breadcrumb + motivation banner
Dark `<header background:#0d1a2e>` with breadcrumb, eyebrow chip (`#93c5fd`), animated dot, Fraunces `<h1>`. Then a `.motivation-banner` with a Fraunces tagline.

### 3. Q-panel (collapsible "My Questions")
Sticky panel at the top of `<main>`.

### 4. Sticky section nav
Tab strip — one button per `.section-block`.

### 5. Section blocks
`<div class="section-block" id="section-X">` containing content, screenshot embeds, mental note, optional analogy.

### 6. Socratic "Think First" block — BEFORE every section-block
**Collapsed by default** (click header to expand). Use **1–2 questions** per block; 3 only for genuinely complex topics.

```html
<div class="socratic-block">
  <div class="socratic-header">
    <span class="socratic-icon">💭</span>
    <span class="socratic-label">THINK FIRST · [TOPIC]</span>
  </div>
  <p class="socratic-insight">[2-3 sentence partial insight]</p>
  <ul class="socratic-questions">
    <li>[Q1 — first principles]</li>
    <li>[Q2 — tradeoff or edge case]</li>
  </ul>
  <div class="answer-area" data-section="UNIQUE-ID">
    <textarea class="answer-textarea" ...></textarea>
    <div class="answer-buttons">
      <button class="btn-add-answer" onclick="saveSocAnswer(this)">+ Add My Answer</button>
      <button class="btn-see-answer" onclick="revealSocAnswer(this)" disabled>🔒 See Model Answer</button>
    </div>
    <div class="model-answers" style="display:none;">...</div>
  </div>
</div>
```

Collapse CSS: `.socratic-block:not(.soc-open) .socratic-insight, .socratic-questions, .answer-area{display:none}`
Collapse JS: `document.querySelectorAll('.socratic-header').forEach(h=>h.addEventListener('click',()=>h.closest('.socratic-block').classList.toggle('soc-open')));`

### 7. Screenshot embeds — for diagram/architecture screenshots
Use `.screenshot-embed` for course screenshots that show diagrams, flows, or comparison tables. **Never embed text-only slides** (bullet lists, title cards).

```html
<div class="screenshot-embed">
  <img src="descriptive-name.png" alt="Brief description">
  <div class="screenshot-caption">One sentence: what this shows and why it matters.</div>
</div>
```

Before referencing a screenshot in HTML, copy it to a descriptive filename (e.g. `zdx-deployment-steps.png`). Never reference raw macOS timestamp filenames in HTML.

### 8. Mental note block — inside every section, after main content
```html
<div class="mental-note-block">
  <div class="mental-note-block-icon">🧠</div>
  <div class="mental-note-block-inner">
    <div class="mental-note-block-label">MENTAL NOTE</div>
    <div class="mental-note-block-text">[Exam-ready takeaway — key facts, numbers, relationships. 2-3 sentences.]</div>
  </div>
</div>
```

### 9. Analogy box — optional, 1-2 per page maximum
Only include when the concept is abstract/counterintuitive or maps clearly onto a real-world system. Not required for every section.

```html
<div class="analogy-box">
  <div class="analogy-label">Analogy</div>
  <div class="analogy-text">[Vivid real-world parallel]</div>
  <div class="analogy-img" ...>
    <img src="A1.png" ...>
    <button class="prompt-toggle" ...>▾ Show analogy image prompt</button>
    <div class="prompt-content" hidden>[Cartoon scene prompt ending with "minimal text labels only where needed, educational infographic feel, modern tech-analogy aesthetic"]</div>
  </div>
</div>
```

### 10. NO generated diagram image placeholders
Do **not** add `<div class="img-display-block">` sections with `1.png`, `2.png` prompts. Diagrams come from real course screenshots via `.screenshot-embed`. Use `phase-steps` or `two-col-grid` HTML components when no screenshot covers a concept.

### 11. Lesson tools — per section
`.lesson-tools` grid with two textareas: "Ask a Question" and "My Notes".

### 12. Sidebar
Hub link, progress checklist, dark `.quick-ref` cards for key terms.

### 13. Footer
Course identifier + section label.

### 14. Scripts (keep `PAGE_KEY` unique)
- Socratic collapse toggle
- `saveSocAnswer()` + `revealSocAnswer()`
- `toggleQPanel()` + `renderQPanel()` + `addQuestion()` + `deleteQuestion()`
- `navTo()`, `toggleDone()`, `initNotes()`, `initChecks()`
- `DOMContentLoaded` handler rehydrating from localStorage

---

## Tone & writing conventions

- **Socratic questions**: guide reasoning, not just test recall. At least one should challenge an assumption or surface a tradeoff.
- **Analogies**: vivid and concrete — must map ≥2 specific elements of the concept. Not just a vague vibe.
- **Mental notes**: lead with the most concrete fact (number, ordered list, key relationship). Exam-ammo only.
- **Model answers**: 2-4 sentences, instructor-level, explain *why* rather than *what*.
- **Image prompts** (analogy only): detailed scene, include short labels for product names (ZIA, ZPA, ZCC) and stage names in multi-step flows. End with `minimal text labels only where needed, educational infographic feel, modern tech-analogy aesthetic`.
- No emojis beyond the established set: 💭 🧠 📷 🔒 💡 ✓ ▾ ▴

---

## Status snapshot

### Completed
- **EDU-302** (Digital Delivery Consultant, 11 sections): full study guides with collapsed Socratic blocks, screenshot embeds, mental notes, optional analogies. Unified blue theme across all sections.
- **EDU-280** (Zero Trust Branch, 11 sections): full study guides with Socratic blocks, analogies, mental notes.
- **EDU-270** (Zero Trust Automation, 5 sections): full study guides.
- **EDU-110** (Intro to AI, 1 page): full study guide.
- **EDU-111** (Cybersecurity AI, 5 pages): full study guides.
- **EDU-220** (Data Security, 6 pages): full study guides.

### Skill file location
The `/zscaler-study-guide` skill is at `~/.claude/skills/zscaler-study-guide/`. `SKILL.md` contains the full workflow; `ANATOMY.md` contains component code templates and CSS/JS patterns.

---

## When asked to create a new study-guide page

1. Read the course screenshots; identify embed-worthy diagrams.
2. For each section: write content → compose collapsed Socratic Think-First block → add screenshot embeds → add mental note → add analogy (if concept warrants one).
3. Write 1-2 model answers per Socratic block (3 only for complex topics).
4. Register the page in its course hub.
5. Append any analogy image prompts to `zscaler-image-prompts.txt`.

If a component would be skipped, say so rather than silently omitting it.
