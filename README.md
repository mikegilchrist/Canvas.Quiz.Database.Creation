# Canvas.API

Canvas LMS archival tool for exporting Classic Quiz submissions, assignment
submissions (SpeedGrader data), discussion boards, and Student Analysis
reports via the Canvas REST API.

**Output formats:** JSON, HTML, SQLite, CSV, and downloaded student files
(PDF, Word, etc.)

## Setup

### 1. Create a Canvas API token

See [README.TOKEN.SETUP.md](README.TOKEN.SETUP.md) for step-by-step
instructions. Save the token to `~/.canvas_token` (chmod 600).

### 2. Create a profile config (recommended)

Copy the example and edit:

```bash
cp canvas.api.conf.example ~/.canvas.api.conf
```

Contents of `~/.canvas.api.conf`:

```ini
[default]
base_url = https://utk.instructure.com
token_file = ~/.canvas_token
```

With a profile config in place, you never need to pass `--base-url` or
`--token-file` on the command line. CLI arguments always override the
config if provided.

## Quick Start

With a profile config, the commands are short:

```bash
# List your courses (filter by year and/or semester)
python3 src/canvas.api.list_courses.py -y 2025 -s Spring

# List quizzes in a course
python3 src/canvas.api.list_quizzes.py COURSE_ID

# List assignments (filter by grading group)
python3 src/canvas.api.list_assignments.py COURSE_ID --group Homework

# Download quiz report CSVs (all quizzes in a course)
python3 src/canvas.api.report_to_csv.py COURSE_ID

# Download assignment submissions (files, rubric, comments)
python3 src/canvas.api.submissions_to_files.py COURSE_ID --group Homework

# List and download discussion boards
python3 src/canvas.api.list_discussions.py COURSE_ID
python3 src/canvas.api.discussions_to_json.py COURSE_ID

# Export quiz submissions to JSON (all quizzes, or one specific quiz)
python3 src/canvas.api.to_json.py COURSE_ID
python3 src/canvas.api.to_json.py COURSE_ID QUIZ_ID
python3 src/canvas.api.json_to_all.py output/JSON output/SQLITE/quiz_archive.sqlite
```

Without a profile config, pass `--base-url` and `--token-file` explicitly:

```bash
python3 src/canvas.api.list_courses.py \
    --base-url https://utk.instructure.com \
    --token-file ~/.canvas_token -y 2025 -s Spring
```

## CLI Scripts

All scripts are in `src/`, prefixed with `canvas.api.` for easy
tab-completion. Run any with `--help` for full usage.

**Scripts that call the Canvas API** (require base_url + token, from
config file or CLI):

| Script                                  | Required Args  | Purpose                                          |
|-----------------------------------------|----------------|--------------------------------------------------|
| `canvas.api.list_courses.py`            | (none)         | List courses; filter by `-y YEAR`, `-s SEMESTER` |
| `canvas.api.list_quizzes.py`            | COURSE_ID      | List quizzes in a course                         |
| `canvas.api.list_assignments.py`        | COURSE_ID      | List assignments; filter by `--group NAME`       |
| `canvas.api.report_to_csv.py`           | COURSE_ID      | Download quiz Student/Item Analysis CSVs         |
| `canvas.api.submissions_to_files.py`    | COURSE_ID      | Download assignment submissions and files        |
| `canvas.api.list_discussions.py`        | COURSE_ID      | List discussion topics in a course               |
| `canvas.api.discussions_to_json.py`     | COURSE_ID      | Download discussion threads to JSON              |
| `canvas.api.to_json.py`                 | COURSE_ID         | Export quiz submissions to canonical JSON     |

**Offline scripts** (no API access needed):

| Script                                  | Required Args         | Purpose                                     |
|-----------------------------------------|-----------------------|---------------------------------------------|
| `canvas.api.json_to_html.py`            | JSON_DIR              | Render canonical JSON to per-submission HTML |
| `canvas.api.json_to_sqlite.py`          | JSON_DIR OUTDB        | Load canonical JSON into SQLite             |
| `canvas.api.json_to_all.py`             | JSON_DIR OUTDB        | Run json_to_html + json_to_sqlite together  |
| `canvas.api.mock_to_json.py`            | MOCK_DIR              | Convert offline mock payloads to canonical JSON |

### Common Flags

- `-v, --verbose` -- detailed progress output
- `-n, --dry-run` -- show what would be done without changes
- `-u, --update` -- skip items whose output already exists
- `--anonymize` -- replace student PII with anonymous identifiers

## Output

All output goes to `output/` (gitignored). Subdirectories:

- `output/CSV/` -- quiz report CSVs
- `output/Assignments/` -- assignment submission files and metadata
- `output/Discussions/` -- discussion topic metadata and threaded views
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
- Discussion Topics API: `/view` endpoint returns full threaded discussion
  in a single response
- File downloads use verifier tokens in the URL (no auth header)
- Targets Classic Quizzes; New Quizzes have a separate API
- UTK Canvas returns 403 on quiz questions endpoint even for instructors;
  Student Analysis CSV bypasses this limitation
