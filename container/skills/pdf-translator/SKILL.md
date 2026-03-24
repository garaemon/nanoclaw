---
name: pdf-translator
description: Translate PDF documents using pdf2zh. Handles URLs (including arxiv) and local files. Produces translated and bilingual PDFs. Uploads to S3 with --upload flag.
allowed-tools: Bash(pdf-translate:*),Bash(s3-upload:*)
---

# PDF Translation with pdf-translate

Translate PDF documents while preserving layout, formulas, and figures.

## Quick start

```bash
pdf-translate <url-or-path> --upload       # Translate and upload to S3 (recommended)
pdf-translate <url-or-path>                # Translate without uploading
pdf-translate <url> -l zh --upload         # Translate to Chinese and upload
pdf-translate <url> -s fr -l en            # French to English
pdf-translate <url> -p 1-5 --upload        # Translate specific pages and upload
```

## URL handling

The tool automatically resolves PDF URLs from various sources:

```bash
# arxiv - all URL formats supported
pdf-translate https://arxiv.org/abs/2301.12345 --upload
pdf-translate https://arxiv.org/pdf/2301.12345.pdf --upload
pdf-translate https://arxiv.org/html/2301.12345 --upload

# Direct PDF URLs
pdf-translate https://example.com/paper.pdf --upload

# Local files
pdf-translate /workspace/group/paper.pdf --upload
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `-l` | Target language | `ja` |
| `-s` | Source language | `en` |
| `-o` | Output directory | `/workspace/group/translated-pdfs` |
| `-p` | Page range (e.g., `1-5,8`) | all pages |
| `--upload` | Upload results to S3 and print download URLs | off |

## Output

The tool produces two files:

- `<name>-mono.pdf` - Fully translated document
- `<name>-dual.pdf` - Bilingual side-by-side comparison

With `--upload`, files are uploaded to a private S3 bucket and AWS Console links are printed. Only users logged into the AWS account can access the files.

## When to use

Use this skill when:
- User shares a PDF URL and asks for translation
- User shares an arxiv link and wants it translated
- User asks to translate a paper or document

Always use `--upload` so the user can download the translated PDF.

## CRITICAL: How to share download links

After `pdf-translate --upload` finishes, it saves a ready-to-send message at:
`/workspace/group/translated-pdfs/upload-message.txt`

You MUST use the Read tool to read this file, then include its FULL content in your response.
The file contains AWS Console URLs starting with `https://`. These URLs are long — that is expected. Do NOT shorten, truncate, or extract parts of them.

## Example workflow

1. User sends: "Translate this paper: https://arxiv.org/abs/2301.12345"
2. **Immediately** use `send_message` to acknowledge the request
3. Run: `pdf-translate https://arxiv.org/abs/2301.12345 --upload`
4. Use the **Read** tool to read `/workspace/group/translated-pdfs/upload-message.txt`
5. Send the file's content to the user verbatim using `send_message`, along with a brief paper summary

**Important**: Always acknowledge the translation request first before starting the translation. The translation can take several minutes, so the user should know their request was received.

## Post-translation summary

After translating a PDF, always provide a brief summary of the paper including:
- Title and authors
- Key contributions or findings
- Why this paper matters

This helps the user decide which parts to read first.
