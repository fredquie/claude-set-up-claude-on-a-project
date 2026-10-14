# NOTES

## What I put in CLAUDE.md, and what I left out

`CLAUDE.md` covers only what Claude cannot get from a quick look at the code: the commands to run, the architecture (entry point, one router per resource, `db/store.js` as the only data layer), and conventions that are easy to break (CommonJS only, the error response shape, converting route params to numbers, the lint rule for unused arguments). I kept it to about 40 lines, because every line is loaded into every session and a long file gets ignored.

I left out the things that either belong somewhere else or go stale. The course steps from the README stay in the README. The file tree is omitted because Claude can list it. The Node version is mentioned only as a gotcha tied to CI. I also kept secrets out: `.env` is referenced by name and never shown.

## Permission rules I added

Rules are in `.claude/settings.json`. The allow list covers the commands I run all the time and that cannot change anything: `npm test`, `npm run lint`, `npm run dev`, and read-only git (`status`, `diff`, `log`, `branch`). The ask list covers actions that change shared state or dependencies: `git push`, `git commit`, `npm install`, and `npm audit fix`. I want to confirm those each time.

The deny list covers actions that would be hard to undo:

- `Read(./.env)` and `Bash(cat .env:*)` block reading the local secrets file. The Read rule alone would not stop `cat .env` run through Bash, so I added both.
- `git push --force` and `git push -f` can overwrite shared history.
- `git reset --hard` can throw away uncommitted work.
- `rm -rf` can delete a whole directory with no undo.

Without the deny rules, one bad prompt or a misread instruction could leak the secrets file or wipe work with no confirmation. Deny rules are a safety net, not a complete barrier: a command run through a different tool or with different flags can still get around a pattern match.

## Verification

- `.claude/settings.json` is valid JSON and is not git-ignored, so it will be committed. `.gitignore` only ignores `.claude/settings.local.json`.
- `/memory` and `/permissions` are interactive commands inside a Claude Code session, so I did not run them here. Check them in a fresh session to confirm that `CLAUDE.md` loads and the rules appear.
