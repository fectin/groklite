# groklite

Small bash CLI for the xAI Grok API (`/v1/responses`). Distinct from Grok Build.

Imported from a legacy `/usr/local/bin/grok` (2026-07-08). That install stays in place until you deploy from this repo.

## Dependencies

- bash, curl, jq, jo

Ubuntu:

    sudo apt install curl jq jo

## Config (not in git)

The script looks for an API key in this order:

1. `GROK_API_KEY` already set in the environment that started `grok`
2. `~/.config/grok.env` (mode `600`), one line: `GROK_API_KEY=xai-...`
3. `/etc/grok/api-key` (mode `640`, `root:grok`), the raw key on one line

If none of those is usable and stdin is a terminal, `grok` asks what to do with a key: save it for your user, save it for this machine (group `grok`, via sudo), or use it for this run only. `grok -k` opens that menu even when a key is already set. A key must start with `xai-` and contain no spaces. Length is not fixed.

Optional persona: `~/.config/grok.persona` (see `grok.persona.example`).

The script still sources `/etc/environment` for a legacy install. A key found only there is used after the three places above. Do not put new keys in that file, and do not copy it into this repo.

## Usage

    grok -h
    grok -k                            # API key menu, then continue
    grok 'what time is it on Mars?'    # one-shot
    grok                               # interactive, auto topic
    grok -t mytopic                    # interactive, named topic
    grok -m grok-4.3 -r 0 '...'        # -r omitted (or -1): no temperature field, server default

Conversations and logs: `~/.log/grok/`.

## Install

    sudo install -m 755 grok /usr/local/bin/grok
