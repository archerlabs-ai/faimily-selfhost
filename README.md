# fAImily - self-hosted

Private AI for your family. Runs on your hardware. You control it.

This repo gets you a local AI chat stack running in Docker, with structural,
age-appropriate guardrails built into the model itself, not bolted onto the
interface. Your kids get an AI to talk to. You decide what it will and won't say.
Nothing leaves your network.

If you're not ready to run this yourself, a simpler setup with no Docker
required is in progress. Star this repo or check [faimily.app](https://faimily.app)
for updates.

## What you get

- A private AI chat assistant that runs entirely on your own hardware
- Per-child profiles with guardrails enforced at the model layer, not a content filter
- Private web search that never leaves your home network
- No cloud account, no API keys, no subscription required for this setup

## What this is not

This is not a parental control app that filters the open web. It's not a
wrapper around ChatGPT. There is no cloud endpoint in this stack. If your
internet goes out, it still works.

## Requirements

- Docker and Docker Compose installed ([get Docker](https://docs.docker.com/get-docker/))
- 16GB RAM minimum (8GB will run a small model, but expect it to feel slow)
- About 10GB free disk space for the base model
- 30-45 minutes for first-time setup

## Quick start

1. Clone this repo:

    ```
    git clone https://github.com/archerlabs-ai/faimily-selfhost.git
    cd faimily-selfhost
    ```

2. Start the stack:
   Start Docker, then run:

    ```
    docker compose up -d
    ```

3. Pull a base model (see `docs/model-guide.md` for which one fits your hardware):

    ```
    docker exec -it faimily-ollama ollama pull llama3.2:3b
    ```

4. Open `http://localhost:3000` (or `http://<SERVER_IP>:3000` from another device
   on your network — see `docs/setup-guide.md`) and create your parent account. The first
   account created is the admin account, this should be you.

5. Follow `docs/setup-guide.md` to create your kids' accounts, set up groups,
   and apply the child Modelfiles from the `modelfiles/` folder.

## Repo structure

```
docker-compose.yml      The full stack: Ollama, Open WebUI, SearXNG
modelfiles/              Template Modelfiles for child profiles, by age tier
docs/setup-guide.md      Full walkthrough: accounts, groups, permissions
docs/model-guide.md      Which model to run based on your hardware
```

## A note on guardrails

The safety rules in this stack live inside the model's own instructions, not
in a UI toggle. A child trying to bypass the interface still hits the same
restrictions, because the restrictions are part of how the model responds,
not a filter sitting in front of it. See `modelfiles/` for the actual
templates and `docs/setup-guide.md` for how to apply and customize them per
child.

## Status

This is an early, self-hosted release for people comfortable with Docker.
It's actively used and tested on real families, not a demo. Issues and pull
requests are welcome.

## License

MIT
