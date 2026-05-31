# 🤝 Contributing to Power BI Data Modeling — Visual Edition

First off: **thank you!** This project gets better every time someone adds a clearer analogy or a missing concept. You don't need to be a data engineer — if you can explain something simply, you can contribute.

---

## 🌟 Ways to contribute

- **Add a new concept** (the most valuable!)
- **Improve an existing analogy** — make it clearer or more memorable
- **Fix or upgrade a diagram**
- **Fix typos / grammar / broken links**
- **Translate** a concept into another language

---

## ✍️ Adding a new concept

Each concept is **one markdown file** in the [`/concepts`](concepts) folder. Keep filenames lowercase with hyphens, e.g. `star-schema.md`.

### Copy this template

```markdown
# 🔣 Concept Name

> **🧒 Explain Like I'm 5:** One friendly sentence using an everyday analogy.

## 🖼️ The Picture

\`\`\`mermaid
flowchart LR
    A[Step one] --> B[Step two] --> C[Result]
\`\`\`
<!-- Or a simple ASCII diagram inside a code block. -->

## 🔧 How it actually works

2–3 short paragraphs for readers who want to go deeper. Plain language first,
jargon second (and always explained). Aim for "smart friend explaining over
coffee," not "textbook."

## 🌍 Real-world example

One concrete place the reader has already encountered this — an app, a product,
or a daily situation.

## 🔗 Related

- [Another Concept](other-concept.md)
```

### Then

1. Add your file to `/concepts`.
2. Add a row to the **table in `README.md`** (keep it in logical order) with the emoji, link, and one-liner.
3. Open a Pull Request.

---

## ✅ Style guide (the whole thing)

- **Analogy first.** If a 12-year-old wouldn't get the one-liner, simplify it.
- **Show, don't just tell.** Every concept needs a diagram (mermaid preferred — GitHub renders it).
- **Short paragraphs.** 2–4 sentences each. White space is your friend.
- **Explain every acronym** the first time you use it.
- **Friendly & visual tone.** Emojis are welcome as signposts, not decoration overload.
- **No gatekeeping.** Assume minimal prior BI knowledge.

### Mermaid color palette

Use only these four colors for consistency across the repo:

| Role | Style |
|------|-------|
| Input / neutral (dimension tables, sources) | `fill:#dbeafe,stroke:#3b82f6,color:#1f2937` |
| Key / central node (fact tables, engines) | `fill:#fef3c7,stroke:#f59e0b,color:#1f2937` |
| Output / positive (reports, fast paths) | `fill:#dcfce7,stroke:#22c55e,color:#1f2937` |
| Error / warning (traps, slow paths) | `fill:#fee2e2,stroke:#ef4444,color:#7f1d1d` |

---

## 🔁 Pull Request checklist

- [ ] New file follows the template above
- [ ] Mermaid diagram included and renders correctly
- [ ] Added to the README table of contents
- [ ] Links work
- [ ] Friendly, beginner-readable tone

---

## 💬 Not sure where to start?

Open an issue describing the concept you want to add or the analogy you'd improve. We're happy to help shape it.

Good first additions: *Row-Level Security (RLS), Object-Level Security, Power Query M basics, Dataflows, Field Parameters, Calculation Groups, Many-to-many patterns.*

Thanks for making Power BI data modeling easier to understand for everyone. 🗂️💛
