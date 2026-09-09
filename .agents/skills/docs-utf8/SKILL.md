---
name: docs-utf8
description: Create or edit repository documentation with explicit UTF-8 encoding, or diagnose GitHub Pages failures caused by invalid text encoding.
---

# Documentation encoding and punctuation

- Save new or edited Markdown and other text files as UTF-8. Preserve existing line endings unless a change is requested.
- Avoid em dashes in authored documentation and user-facing prose. Prefer a period, comma, colon, parentheses, or a rewritten sentence. Preserve exact quotations and code where punctuation is significant.
- Specify encoding explicitly when writing files. Windows PowerShell defaults can produce legacy encodings or UTF-16. For PowerShell file writes, use `[System.IO.File]::WriteAllText($path, $text, (New-Object System.Text.UTF8Encoding($false, $true)))`.
- Before converting an existing file, identify its encoding. Decode using that encoding, then write UTF-8. Do not silently replace invalid bytes or assume every invalid UTF-8 file is Windows-1252.
- Validate changed text files by decoding their bytes with strict UTF-8: `$utf8 = New-Object System.Text.UTF8Encoding($false, $true); $null = $utf8.GetString([System.IO.File]::ReadAllBytes($path))`. A decoding exception means validation failed. For encoding-only conversions, also verify the decoded text is unchanged.
- For a GitHub Pages error, inspect the file named in the first conversion failure. This repository previously failed on `docs/DEVELOPMENT.md` because Windows-1252 byte `0x97` represented em dashes. A correctly encoded em dash is valid UTF-8; avoiding it is a writing preference, not a substitute for encoding validation.
- Report local encoding checks separately from a successful GitHub Pages build. Do not claim the remote build passed without observing it.
