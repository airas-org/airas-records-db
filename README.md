# airas-records-db

The research records AIRAS itself produced, collected from every public
repository whose verification gate passed, so the next study can search
and cite them (`search_papers(sources="airas_records")`,
`register_sources(papers=[{"airas_record": "owner/repo@sha"}])`).

This repository is a dumb store: it copies files, it never interprets
them. AIRAS reads them as raw files and builds its search index on its
own side, so a change to the record schema needs no change here.

## Layout

```
manifest.json                              what is here, one entry per research repository
records/<owner>/<repo>/<sha>/record.json   copied from the repository at that commit (required)
records/<owner>/<repo>/<sha>/claims.tex    if the study has a paper (its claims with verdicts, in prose)
records/<owner>/<repo>/<sha>/main.tex      if the study has a paper (its title)
```

A manifest entry:

```json
{"id": "owner/repo@<sha>", "url": "https://github.com/owner/repo",
 "commit": "<sha>", "stage": "prereg" | "results",
 "collected_at": "2026-09-16T03:00:00+00:00"}
```

`id` is the key everywhere: `records/<owner>/<repo>/<sha>/` on disk, and
what `search_papers` returns and `register_sources` takes.

## What gets in

`collect.yml` (weekly, or on demand) lists the public repositories of
the organizations in `config.json` and, for each one whose default-branch
HEAD has `.research/record.json` **and** both required checks —
"Verify the record" and "Verify the paper" — concluded `success`, copies
that commit's files here and points the manifest at it. A study that was
refuted and never became a paper is collected like any other: its
verdicts are what the next study wants to know.

The gate result is read from GitHub's check runs; nothing is re-verified
here, and nothing here is trusted beyond that.
