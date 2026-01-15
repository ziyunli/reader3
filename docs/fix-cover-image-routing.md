# Fix: Cover Image 422/404 Errors

## Problem

Requests to `/read/{book_id}/cover.jpeg` returned:
1. **422 Unprocessable Entity** - FastAPI couldn't parse "cover.jpeg" as an integer for the `chapter_index` parameter
2. **404 Not Found** - After routing fix, the image path resolution was incorrect

## Root Causes

### 1. Route Ordering Issue (422)

The original route `/read/{book_id}/{chapter_index}` with `chapter_index: int` type annotation caused FastAPI to reject any non-integer path segment with a 422 before other routes could match.

**Fix**: Merged chapter and static file handling into a single route that accepts a string path segment and checks if it's numeric:

```python
@app.get("/read/{book_id}/{path_segment:path}")
async def read_chapter_or_file(request: Request, book_id: str, path_segment: str):
    if path_segment.isdigit():
        # Handle as chapter
    else:
        # Handle as static file
```

### 2. SVG Image Path Rewriting (404)

The EPUB cover used an SVG `<image>` element with `xlink:href="cover.jpeg"`, but `reader3.py` only rewrote paths in `<img src="...">` tags. The cover image was extracted to `images/cover.jpeg` but the HTML still referenced `cover.jpeg`.

**Fix**: Added handling for SVG `<image>` elements in `reader3.py`:

```python
# A2. Fix SVG <image> elements with xlink:href
for img in soup.find_all("image"):
    href = img.get("xlink:href", "") or img.get("href", "")
    # ... rewrite to images/ path
```

## Files Changed

- [server.py](../server.py) - Combined chapter/static file route handler
- [reader3.py](../reader3.py) - Added SVG `<image>` path rewriting

## Verification

After changes, reprocess the EPUB:
```bash
uv run reader3.py system_design_interview.epub
uv run server.py
```

Cover now loads correctly:
```
GET /read/system_design_interview_data/images/cover.jpeg HTTP/1.1" 200 OK
```
