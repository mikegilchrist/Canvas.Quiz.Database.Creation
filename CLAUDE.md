# Canvas.API

Canvas LMS archival tool. Exports Classic Quiz submissions, assignment
submissions (SpeedGrader data), and Student Analysis reports via the
Canvas REST API. Output formats: JSON, HTML, SQLite, CSV, and downloaded
student files.

## Repository Layout

```
src/                     Python modules + CLI entrypoints
output/                  Generated output (gitignored, contains student PII)
README.TOKEN.SETUP.md   How to create and store a Canvas API token
EXAMPLES.txt            Offline mock pipeline walkthrough
CHANGELOG.md            Release history
```

## CLI Entrypoints (src/)

All CLIs accept `--base-url URL` and `--token TOKEN | --token-file FILE`.
Run any with `--help` for full usage.

| Script                  | Purpose                                          |
|-------------------------|--------------------------------------------------|
| `list_courses.py`       | List courses; filter by `-y YEAR`, `-s SEMESTER` |
| `list_quizzes.py`       | List quizzes in a course                         |
| `list_assignments.py`   | List assignments; filter by `--group NAME`       |
| `report_to_csv.py`      | Download quiz Student/Item Analysis CSVs         |
| `submissions_to_files.py` | Download assignment submissions (files, rubric, comments) |
| `api_to_json.py`        | Export quiz submissions to canonical JSON        |
| `json_to_html.py`       | Render canonical JSON to per-submission HTML     |
| `json_to_sqlite.py`     | Load canonical JSON into SQLite                  |
| `json_to_all.py`        | Run json_to_html + json_to_sqlite together       |
| `mock_to_json.py`       | Convert offline mock payloads to canonical JSON  |

### Common Flags

Most CLIs support these flags:

- `-v, --verbose` -- detailed progress output
- `-n, --dry-run` -- show what would be done without making changes
- `-u, --update` -- skip items whose output already exists
- `--anonymize` -- replace student PII with anonymous identifiers

## Library Modules (src/)

| Module           | Purpose                                           |
|------------------|---------------------------------------------------|
| `canvas_http.py` | HTTP layer: GET/POST with auth, pagination, raw download |
| `canvas_api.py`  | Canvas endpoint wrappers (courses, quizzes, assignments, submissions, reports, rubrics) |
| `io_utils.py`    | JSON I/O helpers, `read_token()`                  |
| `model.py`       | Canonical JSON schema for quiz submissions        |
| `render_html.py` | HTML rendering for quiz submissions               |
| `sqlite_writer.py` | SQLite schema and insert logic                  |

## Canvas API Notes

- Quiz Reports API is async: POST to trigger, poll progress, GET file URL,
  download CSV (no auth header on final download -- verifier in URL)
- Assignment Submissions API is synchronous; file attachment URLs also use
  verifier tokens (no auth header)
- UTK Canvas returns 403 on `/courses/:id/quizzes/:id/questions` even for
  instructors -- Student Analysis CSV bypasses this
- Classic Quizzes and New Quizzes have separate APIs; this tool targets
  Classic Quizzes

## Authentication

Store your Canvas API token in `~/.canvas_token` (chmod 600).
See README.TOKEN.SETUP.md for step-by-step instructions.

Pass it to any CLI via `--token-file ~/.canvas_token` or
`--token <inline-token>`.

## Output and PII

- All output goes under `output/` which is gitignored
- CSV and submission downloads contain student names, Canvas IDs, SIS IDs
- Use `--anonymize` to replace PII with Student_01..Student_NN
- Never commit student data to the repository

## Filename Conventions

- Underscores separate major sections, periods separate words within a section
- Example: `S01_2025-01-16_Mutations.Teamwork_student.analysis_12345-67890_2026-02-19.csv`
- `sanitize_for_filename()` in `report_to_csv.py` and `submissions_to_files.py`
  handles the slug conversion

## Development

- Python 3.7+, stdlib only (no pip dependencies)
- All scripts run from the repo root: `python3 src/script.py ...`
- CLI entrypoints should be executable (`chmod +x`)
- Library modules are not executable
