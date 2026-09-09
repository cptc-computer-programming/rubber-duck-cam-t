# Getting started with development

Follow [README.md](README.md) for first setup. M1 uses published images, so
there is no application build or Python/Node environment to install.
Run these commands from the repository directory on the Linux server.

## Check the services

```bash
docker compose ps
docker compose logs --tail=100 open-webui ollama
docker compose exec ollama ollama list
```

Send a chat message, then check GPU use with
`docker compose exec ollama ollama ps`. If GPU access fails, check:

```bash
nvidia-smi
docker run --rm --gpus all ubuntu:24.04 nvidia-smi
```

## Apply configuration changes

Edit `.env` for the address, port, or secret; edit `compose.yaml` for services.
Keep the existing secret unless deliberately rotating it.

```bash
docker compose config --quiet
docker compose up -d --wait --wait-timeout 600
```

Change saved Open WebUI settings in the Admin Panel: database settings can
override environment values. Keep `.env`, `data/`, and backups out of Git.

## Common restarts and resets

**Restart a stuck service** — preserves all data:

```bash
docker compose restart open-webui
```

Substitute `ollama` to restart the model server. Restart does not apply edits
to configuration; use the startup command above for those.

**Stop and start everything** — preserves all data:

```bash
docker compose down
docker compose up -d --wait --wait-timeout 600
```

**Recreate containers** — preserves all data:

```bash
docker compose up -d --force-recreate --wait --wait-timeout 600
```

**Download a missing model** — refresh the browser afterward:

```bash
docker compose exec ollama ollama pull llama3.2:3b
```

Substitute another model tag to try a different model.

**Reset accounts and settings** — back up first. Move the old web data aside:

```bash
docker compose down
sudo mv data/open-webui "data/open-webui-before-reset-$(date +%Y%m%d-%H%M%S)"
```

Set `WEBUI_BIND_ADDRESS=127.0.0.1` in `.env`, then run the startup command.
Create the new administrator before reopening campus access. Old accounts,
chats, and settings remain in the moved directory but are unavailable in the
fresh instance. Downloaded models are kept. For a full fresh start, also move
`data/ollama` aside before starting and download the model again.

## Backup and restore

Stop services for a consistent backup. Use a new archive name each time:

```bash
mkdir -p backups
chmod 700 backups
docker compose stop
sudo tar -czpf backups/m1-backup.tar.gz .env compose.yaml data
sudo chmod 600 backups/m1-backup.tar.gz
docker compose up -d --wait --wait-timeout 600
```

The archive includes private accounts, chats, the secret, and model files.
Keep a protected copy off the server. If backup fails, restart services and
investigate before treating the archive as usable.

To restore, stop the original deployment with `docker compose down` first.
Extract into a **new, empty directory**, not over existing data:

```bash
mkdir campus-chat-restored
cd campus-chat-restored
sudo tar -xzpf /absolute/path/to/m1-backup.tar.gz
sudo chown "$(id -u):$(id -g)" .env compose.yaml
chmod 600 .env
```

Set the restored `.env` binding to `127.0.0.1`, keeping its secret, then run
`docker compose up -d --wait --wait-timeout 600`. Verify an existing login,
conversation, saved setting, and new reply before reopening campus access.
Restore the backed-up image versions along with the data, especially after upgrades.

## Future development

M2 configures and evaluates prompts and assignment knowledge in the existing
application. M3 may introduce a small source change in a separate
[Open WebUI fork based on v0.11.3](https://github.com/open-webui/open-webui/tree/v0.11.3).
Build the unchanged source first, then try a custom image here. Back up before
switching images. Ollama is pinned to `0.33.3`.
