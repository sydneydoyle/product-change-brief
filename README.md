# Product change brief

A small Python prototype that reads recent changes from a GitHub repository and uses the Claude API to draft a focused product-change brief for documentation and customer-facing teams.

## Set up

Requires Python 3.9 or later, `git`, and a Claude API key. API usage may incur charges. The script uses only Python's standard library.

```bash
export ANTHROPIC_API_KEY="your-key"
python3 brief.py
```

By default, the script reads the last 30 days of commits from `sydneydoyle/sydneydoyle.github.io`, uses `claude-opus-5-5`, and writes `brief.md`.

| Option | What it does |
| --- | --- |
| `--repo owner/name` | Repository to read |
| `--days 14` | How far back to look |
| `--max-commits 100` | Most recent commits to consider |
| `--max-changes 5` | How many change sets to explain in detail |
| `--docs-repo owner/name` | Repository holding the documentation to compare against (defaults to `--repo`) |
| `--docs-path docs/` | Folder of documentation inside the docs repo; `.` for the whole repo |
| `--model model-id` | Claude model (or set `ANTHROPIC_MODEL`) |
| `--effort high` | Claude effort level: `low`, `medium`, `high`, `xhigh`, `max`, or `default` |
| `--output path.md` | Where to write the brief |
| `--dry-run` | Collect commits and diffs without calling Claude |
| `--save-evidence path.json` | Save the collected commits and diffs for inspection |

Examples:

```bash
# Compare portfolio changes against the portfolio's own pages
python3 brief.py --docs-path .

# Product code in one repo, documentation in another
python3 brief.py --repo acme/app --docs-repo acme/docs --docs-path docs/
```

Private repositories need a `GITHUB_TOKEN` environment variable with read access. Do not commit API keys.

## How it works

1. **Read the diffs.** The script clones the repository into `.cache/` (skipping large files such as images) and reads each commit's changed files. Lockfiles, images, fonts, and minified assets are left out. For HTML and similar markup, only the visible text of changed lines is kept, so Claude sees what readers would see.
2. **Group related commits.** Claude groups commits into change sets, such as a new page plus its follow-up wording fixes, and rates each set's audience (customer-facing, internal, maintenance) and significance (high, medium, low), based on the diffs rather than only the messages.
3. **Select and explain.** Claude also orders the change sets by importance to readers, so a new page outranks a long series of small edits. The highest-ranked customer-facing and internal change sets (up to `--max-changes`) get a detailed write-up: what changed, potential impact, and sources. Everything else gets a one-line summary under "Other changes" with links.
4. **Check documentation only when there's something to compare.** If you supply a docs source, the script searches it for passages that match at least two of each change's key terms, skipping files the change itself edited, so it finds related pages that were *not* updated. Claude reports a gap only when an excerpt is clearly about the same feature and is missing, outdated, or contradicted by the diff. Otherwise it says the change was not assessed.

## Limits

This version does not verify a deployment, post to Slack, or run on a schedule. Diffs are trimmed per file and per commit, so very large changes are only partly visible. Review every finding and source before sharing, and use only data you are authorized to send to the API. Generated briefs and the `.cache/` folder are ignored by Git.
