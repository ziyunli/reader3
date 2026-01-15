# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A lightweight EPUB reader web application for collaborative reading with LLMs. Takes EPUB files, processes them into a navigable web interface, and extracts plain text for LLM context.

**Note**: This project was "90% vibe coded" for exploration/inspiration. It's intentionally simple and not production-focused.

## Commands

```bash
# Install dependencies
uv sync

# Process an EPUB file (creates <filename>_data/ directory with pickled book data)
uv run reader3.py <file.epub>

# Start web server on port 8123
uv run server.py
```

## Architecture

### Data Flow
```
EPUB → reader3.py → *_data/book.pkl + images/ → server.py → Web UI
```

### Core Files
- `reader3.py` - EPUB processing engine. Extracts metadata, TOC, spine (reading order), and images. Cleans HTML and serializes to pickle.
- `server.py` - FastAPI server. Loads pickled books, serves reading interface with Jinja2 templates.
- `templates/library.html` - Book grid view
- `templates/reader.html` - Reading interface with sidebar TOC and navigation

### Key Concepts
- **Spine vs TOC**: Spine is the linear reading order (physical files). TOC is the hierarchical navigation tree. They're connected via filename (`href`).
- **Image handling**: Images are extracted to `images/` folder and HTML paths are rewritten.
- **HTML sanitization**: Scripts, styles, iframes, forms, etc. are stripped for safety.
- **Plain text extraction**: Each chapter's text is extracted for LLM context.

### Data Classes (reader3.py)
- `Book` - Master object with metadata, spine, TOC, images
- `ChapterContent` - A physical file in the EPUB
- `TOCEntry` - Navigation entry (can be hierarchical)
- `BookMetadata` - Title, authors, description, etc.
