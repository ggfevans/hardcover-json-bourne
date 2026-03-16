# Hardcover JSON Bourne

A composite GitHub Action that fetches reading data from the [Hardcover](https://hardcover.app) GraphQL API and writes it to a structured JSON file. Built as part of [gvns.ca](https://gvns.ca) ([source](https://github.com/ggfevans/gvns.ca)) and provided as-is.

## What it does

This action hits the Hardcover API to pull down your reading activity -- currently reading, finished, and want to read -- then writes everything to a single JSON file in your repository. When paired with a commit step and a static site generator like Astro, this gives you a "live" reading data page that updates on a schedule without any server-side runtime.

Authentication is required -- you need a Hardcover API token.

## Installation

Copy [`example.yml`](example.yml) to `.github/workflows/fetch-reading.yml` in your repository and replace the secret names if needed.

Or use this minimal snippet:

```yaml
name: Fetch Reading Data

on:
  schedule:
    - cron: '0 0 * * *'  # daily
  workflow_dispatch:

jobs:
  fetch:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6.0.2

      - uses: ggfevans/hardcover-json-bourne@v1
        with:
          token: ${{ secrets.HARDCOVER_TOKEN }}
          user_id: ${{ secrets.HARDCOVER_USER_ID }}

      - name: Commit and push
        uses: stefanzweifel/git-auto-commit-action@b863ae1933cb653a53c021fe36dbb774e1fb9403 # v5.2.0
        with:
          commit_message: 'chore: update reading data'
          file_pattern: src/data/reading.json
```

The action writes the JSON file. Committing and pushing is handled separately.

## Inputs

| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `token` | Yes | -- | Your Hardcover API token |
| `user_id` | Yes | -- | Your Hardcover user ID (positive integer) |
| `output_path` | No | `src/data/reading.json` | Path to write the JSON file |
| `limit` | No | `50` | Maximum number of books to fetch |

## Output JSON

The action writes a single JSON file with these fields:

- `lastUpdated` -- ISO 8601 timestamp of when the data was fetched
- `currentlyReading` -- array of books with `title`, `author`, `coverUrl`, `hardcoverUrl`
- `finished` -- array of books with `title`, `author`, `coverUrl`, `hardcoverUrl`, `rating`, `dateFinished`
- `wantToRead` -- array of books with `title`, `author`, `coverUrl`, `hardcoverUrl`

Status mapping from Hardcover's `status_id`:
- `1` = Want to Read
- `2` = Currently Reading
- `3` = Finished
- `4` (Did Not Finish) and `5` (Dropped) are excluded

## AI Disclosure

This project was built with the assistance of AI tools (Claude). The design, specification, and implementation were developed collaboratively with AI-generated code. All code has been reviewed and tested, but use at your own discretion.

## License

MIT -- see [LICENSE](LICENSE) for details.
