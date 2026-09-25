# TODO

Maintainer list for `grok`. Work order, not difficulty.

User-facing limitations belong in the README; stubs can keep a one-line comment in the script. Do not treat this file as LAN wiki material.

Mark **decision** items as decided before coding them. They are not bugs.

Status: open unless noted.

## Fork (do this first)

The CLI describes a topic manager. The process is a write-only client. Pick one.

**A — Honest write-only client.** Hide or remove `-l`, `-d`, `-t last` / `latest`, and in-session `topic_*`. Help matches what the script does. Topic files may still be named dumps for interactive runs.

**B — Finish the topic manager.** Implement load (below), then list / last / delete. Help stays in that shape only after load works.

Until this is picked, do not “fix help” and “implement `-l`” in the same pass — they are opposite jobs.

Related decision (either fork, don’t guess):

- **One-shot vs interactive history.** Help says one-shot has no history; the code always writes `~/.log/grok/conversations/$topic`. Pick: no file, ephemeral file, or the same store as interactive.

## Real bugs

Worth fixing on either fork.

- `printf "$content\n"` in one-shot output. A `%` in the model reply breaks `printf`. Use `printf '%s\n' "$content"`.
- `log()` treats the message as a printf format. Same class of bug; `echo` or `printf '%s\n'`.
- API errors append to a directory. `cat "$tempfile" >> "$logfile"` but `$logfile` is `~/.log/grok/logs/`. Use the dated file `log()` already writes.
- `converse $@` and `local query=$@`. Unquoted multi-word one-shots can drop words. Quote `"$@"` / decide `"$*"` vs one argument.
- Interactive EOF loops. Failed `read` should exit, same as typing `exit`.

## Dead streamline joins

Delete after the fork, so you do not “fix” flags you are about to remove.

- `$mode` is never set. The interactive-vs-one-shot error return is dead. Set it or delete the branch.
- Role remap is dead. Comparing `$1` to `$username` / `$grokname` never matches. Callers already pass `user` / `assistant`. Delete the two tests.
- `-t last` empty-history fallback is single-quoted. Topic becomes the literal `chat$(date +%Y%m%d)`. Goes away if A drops `last`; fix quoting only if B keeps it.
- Leftover `$HOME/.grok_history_*.json` glob next to the real conversations dir.
- `log touch "$convofile$topic"` logs a `touch` it does not run.

## If B: load, then list / last / delete

Alters what `-t` / `-l` / save actually do. Load is the first change that changes the tool.

1. **Load topic files instead of overwriting.** Read the NDJSON back into `messagestack`, rebuild `$tempchat` from roles, and only create + persona when the file is missing. Saves must not `>` a file they did not load.
2. **Then** `-l` lists names in `~/.log/grok/conversations/`.
3. **Then** `-t last` / `latest` uses that dir (not `~/.grok_history_*.json`).
4. **Then** `delete_topic` unlinks the file; handle missing topic.

In-session `topic_*` switching only after load works, and without destroying `$tempchat` / the stack on switch.

## Out of scope / later

Not this framework.

- Put `$temperature` / `$maxtokens` into the `jq` object (`-r` is unused). True, low value.
- `source /etc/environment` is still there for a legacy install. Key lookup does not install a key there. Order: parent `GROK_API_KEY`, `~/.config/grok.env`, `/etc/grok/api-key`, then a key that source happened to set. Removing the source line is still optional portability work. Failing when `jo` / `jq` / `curl` is missing is still open.
- `list_models` (`GET /v1/language-models`). Different feature.
- Message stack as a bash array of `jo` JSON strings. Fine for short terminal lines; fragile for quotes, newlines, empty API text, or a huge reply. That is a ceiling, not a task. Do not start a rewrite. Topic resume (B) should treat the file as NDJSON via `jq`, not `printf '%s\n' "${messagestack[@]}"` as gospel.
- Startup assigning a topic and logging a header before `-h` / `-l`. Nit; fold into help/getopts if you touch that path.
