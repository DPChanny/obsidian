---
description: "Use when editing Obsidian markdown notes in this vault, especially when applying vault-wide note structure, linked references, LaTeX preservation, folder taxonomy, and Obsidian CLI workflows."
name: "Obsidian Vault Rules"
applyTo: "[!.]*/**/*.md"
---

## Scope

- Apply to markdown notes under all non-hidden root folders, excluding .github and .obsidian.
- Treat these files as canonical study notes.
- Prefer reuse, linking, and note consolidation over duplication.

## Root and Folder Constraints

- Use the root folders as taxonomy signals.
- Keep the existing top-level division between `Devs`, `Generals`, and `Mathematics`.
- Treat singular folder names as core concepts and plural folder names as category or collection spaces.
- Preserve the existing directory hierarchy when choosing note placement.
- Surface ambiguity instead of guessing when the correct folder is unclear.

## Markdown Rules

- Do not add an H1 title that repeats the filename as a document title.
- Preserve the existing note style of the destination file.
- Preserve inline math and block math as part of the canonical content.
- Do not flatten, paraphrase, or recreate LaTeX unless the existing note structure requires small normalization.
- Use wikilinks in the form `[[폴더/파일명|파일명]]` when referencing existing notes.
- Keep related notes connected with explicit wikilinks.
- Use `related` frontmatter for related concepts that are not already covered in the body, and keep those values quoted.
- Avoid duplicating the same relation both in the body and in `related` frontmatter.
- Use fenced code blocks with language tags when code is present.

## Writing Style

- Respond in Korean by default.
- Use technical terms in English where appropriate.
- Write in a factual, concise, descriptive style.
- Avoid decorative language, emojis, and metaphorical explanations.

## Obsidian CLI Workflow

- Use Obsidian CLI for markdown file operations inside the vault.
- Inspect nearby notes first and keep the search scope minimal.
- When study flow is being summarized, consolidate it directly into the permanent note structure.
- Prefer extending an existing note or linking to it over creating a redundant new note.
