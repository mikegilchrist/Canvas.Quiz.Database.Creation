Canvas Quiz Archive - Modular Offline/Online Pipeline

Folders
- ./src            Python modules + CLI entrypoints
- ./data           Input data (mock JSON, exported HTML, etc.)
- ./output/JSON    Canonical per-submission JSON files
- ./output/HTML    Rendered per-submission HTML files
- ./output/SQLITE  SQLite databases

Pipeline
1) API -> JSON     (src/api_to_json.py)   or Offline mocks -> JSON (src/mock_to_json.py)
2) JSON -> HTML    (src/json_to_html.py)
3) JSON -> SQLITE  (src/json_to_sqlite.py)

Notes on newlines
- Canvas web UI often collapses whitespace when rendering HTML, so underlying exported HTML or API text may contain newlines that are not visually obvious.
- JSON preserves newline characters by escaping them as \n inside JSON strings. This is normal and preserves meaning.
- We treat canonical JSON as the authoritative record, and only apply readability cleanup when rendering HTML.
