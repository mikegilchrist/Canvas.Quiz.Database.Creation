# Canvas.API

Canvas LMS archival tool for exporting Classic Quiz submissions, assignment
submissions (SpeedGrader data), and Student Analysis reports via the Canvas
REST API.

**Output formats:** JSON, HTML, SQLite, CSV, and downloaded student files
(PDF, Word, etc.)

## Quick Start

1. Create a Canvas API token and save it to `~/.canvas_token`
   (see [README.TOKEN.SETUP.md](README.TOKEN.SETUP.md))

2. List your courses:
   ```bash
   python3 src/list_courses.py --base-url https://utk.instructure.com \
       --token-file ~/.canvas_token -y 2026 -s Spring
   ```

3. List quizzes or assignments in a course:
   ```bash
   python3 src/list_quizzes.py 215694 --base-url https://utk.instructure.com \
       --token-file ~/.canvas_token

   python3 src/list_assignments.py 215694 --base-url https://utk.instructure.com \
       --token-file ~/.canvas_token --group Homework
   ```

4. Download quiz report CSVs:
   ```bash
   python3 src/report_to_csv.py 215694 --base-url https://utk.instructure.com \
       --token-file ~/.canvas_token --verbose
   ```

5. Download assignment submissions (files, rubric, comments):
   ```bash
   python3 src/submissions_to_files.py 215694 --base-url https://utk.instructure.com \
       --token-file ~/.canvas_token --group Homework --verbose
   ```

6. Export quiz submissions to JSON, then render HTML + SQLite:
   ```bash
   python3 src/api_to_json.py 215694 12345 --base-url https://utk.instructure.com \
       --token-file ~/.canvas_token --outdir output/JSON --verbose

   python3 src/json_to_all.py output/JSON output/SQLITE/quiz_archive.sqlite \
       --html-outdir output/HTML --verbose
   ```

## CLI Scripts

All scripts are in `src/` and accept `--help` for full usage.

| Script                    | Purpose                                          |
|---------------------------|--------------------------------------------------|
| `list_courses.py`         | List courses; filter by `-y YEAR`, `-s SEMESTER` |
| `list_quizzes.py`         | List quizzes in a course                         |
| `list_assignments.py`     | List assignments; filter by `--group NAME`       |
| `report_to_csv.py`        | Download quiz Student/Item Analysis CSVs         |
| `submissions_to_files.py` | Download assignment submissions and files        |
| `api_to_json.py`          | Export quiz submissions to canonical JSON        |
| `json_to_html.py`         | Render canonical JSON to per-submission HTML     |
| `json_to_sqlite.py`       | Load canonical JSON into SQLite                  |
| `json_to_all.py`          | Run json_to_html + json_to_sqlite together       |
| `mock_to_json.py`         | Convert offline mock payloads to canonical JSON  |

### Common Flags

- `-v, --verbose` -- detailed progress output
- `-n, --dry-run` -- show what would be done without changes
- `-u, --update` -- skip items whose output already exists
- `--anonymize` -- replace student PII with anonymous identifiers

## Output

All output goes to `output/` (gitignored). Subdirectories:

- `output/CSV/` -- quiz report CSVs
- `output/Assignments/` -- assignment submission files and metadata
- `output/JSON/` -- canonical quiz submission JSON
- `output/HTML/` -- rendered quiz submission HTML
- `output/SQLITE/` -- SQLite databases

**Warning:** Output contains student PII (names, IDs, sections).
Use `--anonymize` to strip PII, and never commit the `output/` directory.

## Requirements

- Python 3.7+ (stdlib only, no pip dependencies)
- Canvas API token with instructor/TA access

## Notes

- Quiz Reports API is asynchronous (POST, poll, download)
- Assignment Submissions API is synchronous
- File downloads use verifier tokens in the URL (no auth header)
- Targets Classic Quizzes; New Quizzes have a separate API
- UTK Canvas returns 403 on quiz questions endpoint even for instructors;
  Student Analysis CSV bypasses this limitation
