# Product change brief

A small Python prototype that reads recent commits from a public GitHub repository and uses the Claude API to draft two reviewable briefs: one for documentation and one for customer-facing teams.

## Set up

Requires Python 3.9 or later, a Claude API key with API access, and a supported Claude model ID. API usage may incur charges. The script uses only Python's standard library.

```bash
export ANTHROPIC_API_KEY="your-key"
export ANTHROPIC_MODEL="your-model-id"
python3 brief.py
```

By default, the script reads the last 30 days of commits from `sydneydoyle/sydneydoyle.github.io` and writes `brief.md`. Use `--repo owner/name`, `--days 14`, `--output path.md`, or `--model model-id` to change those settings. The repository must be public unless you supply an authorized `GITHUB_TOKEN` environment variable.

Do not commit API keys. Public GitHub requests can be rate limited; an optional GitHub token can raise the limit.

## What this version does

The script retrieves up to 100 commit messages and links, sends them to Claude, and saves its response locally. It does not read code diffs or docs, verify a deployment, post to Slack, or automatically run on a schedule. Commit messages can be incomplete; review every finding and source before sharing. The generated file is ignored by Git.

This is an early prototype inspired by a product-change discovery workflow. Compare results against known changes before relying on it, and use only data you are authorized to send to the API.
