/translate [file_path] [output_path] - Translate the contents of a file between English and Chinese. Reads the specified file, automatically detects whether it's in English or Chinese, and translates it to the other language. Creates a new translated file with '_translated' suffix (e.g., article.md becomes article_translated.md). Supports both Simplified and Traditional Chinese. Uses the english-chinese-translator subagent for professional, contextually appropriate translations.

Usage examples:
- /translate README.md - Translates README.md and saves to README_translated.md
- /translate docs/guide.txt - Translates guide.txt and saves to guide_translated.txt
- /translate article.md output/article_cn.md - Custom output location
- /translate article.md - Auto-detects language and translates accordingly

Supported file types: .txt, .md, .json, .yaml, .py, .js, .ts, .html, .xml, and other text-based formats

The command will:
1. Read the file at the specified path
2. Detect the source language (English or Chinese)
3. Use the Task tool to invoke the english-chinese-translator subagent
4. Save the translated content to a new file with '_translated' suffix (or custom output path if specified)
5. Preserve the original file format and structure

Language handling:
- Default: Translates to Simplified Chinese. For Traditional Chinese, the translator will auto-detect based on context.

Content preservation:
- Preserves: Code blocks, markdown formatting, URLs, and technical terms when appropriate

Performance notes:
- For large files (>10MB), translation may be done in chunks to ensure quality
- Note: If the file doesn't exist or cannot be read, the command will display an error message.
