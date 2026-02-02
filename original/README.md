# Canvas Interface

## Running Script

### Minimal

```
python3 canvas_quiz_archive.py COURSE_ID QUIZ_ID out_html quiz_archive.sqlite \
  --base-url https://YOURCANVASDOMAIN \
  --token-file ./.canvas_token \
  --verbose
```

### With Downloaded Student Analysis CSV File

```
python3 canvas_quiz_archive.py COURSE_ID QUIZ_ID out_html quiz_archive.sqlite \
  --base-url https://YOURCANVASDOMAIN \
  --token-file ~/.canvas_token \
  --with-earned-points \
  --earned-points-csv student_analysis.csv \
  --verbose
```
