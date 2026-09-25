# groklite

Small bash CLI for the xAI Grok API (`/v1/responses`). Distinct from Grok Build.

Imported from MarsTop `/usr/local/bin/grok` (2026-07-08). Live install there is still that file until you deploy from this repo.

## Dependencies

- bash, curl, jq, jo

Ubuntu:

    sudo apt install curl jq jo

## Config (not in git)

    export GROK_API_KEY='...'          # required
    # optional: ~/.config/grok.persona  # system prompt; see grok.persona.example

MarsTop currently injects the key via `/etc/environment` and `source /etc/environment` in the script. Do not copy that file here.

## Usage

    grok -h
    grok 'what time is it on Mars?'    # one-shot
    grok                               # interactive, auto topic
    grok -t mytopic                    # interactive, named topic
    grok -m grok-4.3 -r 0 '...'        # -r omitted (or -1): no temperature field, server default

Conversations and logs: `~/.log/grok/`.

## Install

    sudo install -m 755 grok /usr/local/bin/grok
