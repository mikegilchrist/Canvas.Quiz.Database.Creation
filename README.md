# Canvas Interface

## Canvas Access Token

Before you can run the script, you need to generate a Canvas access token, then saving it to a file.

1) Create a Canvas access token using Canvas UI:

    a. Click Account (left sidebar)
    a. Click Settings
    a. Scroll to Approved Integrations
    a. Click + New Access Token
    a. Enter a Purpose (e.g., quiz-archive)
    a. Optionally set an Expiration
    a. Click Generate Token
    a. Copy the token (you won’t be able to see it again)
2) Save it to `~/.canvas_token` on Linux
   In a terminal:
```{bash}
mkdir -p "$HOME"
umask 077
printf '%s\n' 'PASTE_TOKEN_HERE' > "$HOME/.canvas_token"
```
3) Set permissions:

```{bash}
chmod 600 "$HOME/.canvas_token"
```
4) Verify the file contents. 
   It should contain one line (the token):

```{bash}
wc -l ~/.canvas_token
head -n 1 ~/.canvas_token
```

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
