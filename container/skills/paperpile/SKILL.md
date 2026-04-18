---
name: paperpile
description: Register PDF papers to Paperpile, translate with pdf2zh, attach translated PDF, and return a summary. Use when a user shares a PDF paper or arxiv link.
allowed-tools: Bash(paperpile-register:*),Bash(paperpile:*)
---

# Paperpile Registration with Translation

Register academic papers to Paperpile and automatically translate them.

## CRITICAL: Always use `paperpile-register`

You MUST use the `paperpile-register` CLI for the entire workflow. Do NOT implement any step manually — no manual downloads, no direct `pdf2zh` calls, no direct `paperpile upload`, no direct `s3-upload`, no Python scripts. The `paperpile-register` script handles everything: download, upload, metadata extraction, rename, translation, attach, and S3 backup.

## Quick start

```bash
paperpile-register <url-or-path>             # Register, translate, backup to S3
paperpile-register <url> -p 1-10             # Translate specific pages only
```

## URL handling

```bash
# arxiv - all URL formats
paperpile-register https://arxiv.org/abs/2301.12345
paperpile-register https://arxiv.org/pdf/2301.12345.pdf

# Direct PDF URLs
paperpile-register https://example.com/paper.pdf

# Local files (e.g., attachment)
paperpile-register /workspace/group/attachments/paper.pdf
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `-l` | Target language | `ja` |
| `-s` | Source language | `en` |
| `-o` | Output directory | `/workspace/group/paperpile` |
| `-p` | Page range (e.g., `1-5,8`) | all pages |

## What it does

1. Downloads the PDF (if URL)
2. Uploads to Paperpile (metadata auto-extracted)
3. Waits for Paperpile to process and retrieves title, author, year
4. Renames the PDF to Paperpile-style: `Title - FirstAuthor Year.pdf`
5. Translates with pdf2zh producing a bilingual `-dual.pdf`
6. Attaches the `-dual.pdf` to the Paperpile library item
7. Backs up translated PDFs to S3 and writes AWS Console URLs to summary

## When to use

Use this skill when:
- User shares a PDF and asks to register it to Paperpile
- User shares an arxiv link
- User shares a paper PDF attachment
- User says something like "this paper", "register this", "add to Paperpile"

## CRITICAL: How to share results

After `paperpile-register` finishes, read `/workspace/group/paperpile/summary.txt` and include the following in your response:
- Paper title, authors, year (`TITLE`, `FIRST_AUTHOR`, `YEAR`)
- AWS Console URLs (`DUAL_S3_URL`, `MONO_S3_URL`) for the translated PDFs
- Confirmation that it was registered to Paperpile

Do NOT generate your own S3 URLs. Only use the URLs from `summary.txt`.

## Example workflow

1. User sends a PDF or arxiv link
2. **IMMEDIATELY — before doing anything else** — call `send_message` to tell the user you received the request and that registration + translation will take a few minutes. Do NOT skip this step. The process takes several minutes and the user must know their request was received.
3. Run: `paperpile-register <url-or-path>`
4. Read `/workspace/group/paperpile/summary.txt` for metadata and URLs
5. Send the user a summary with the information from summary.txt

## Direct paperpile usage

For manual operations only (not for the registration workflow):

```bash
paperpile list              # List all library items
paperpile me                # Show account info
```
