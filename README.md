# Campus chatbot  M1

A chatbot hosted on a campus server. Open WebUI provides the browser interface
and accounts; Ollama runs the model on the server's NVIDIA GPU. M1 has no
homework or rubber-duck features.

```text
Browser -> Open WebUI -> Ollama -> NVIDIA GPU
```

## Get started

Use a Linux server with Git, Docker Compose 2.30+, an NVIDIA driver, and the
[NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)
configured for Docker. Internet access is needed for initial downloads.

```bash
git clone https://github.com/cptc-computer-programming/rubber-duck-cam-t.git
cd rubber-duck-cam-t
cp .env.example .env
openssl rand -hex 32
```

Paste the generated key into `WEBUI_SECRET_KEY` in `.env`, then start:

```bash
docker compose up -d --wait --wait-timeout 600
docker compose exec ollama ollama pull llama3.2:3b
```

Open `http://localhost:3000` on the server. The first account becomes the
administrator. Select `llama3.2:3b` and send a message.

For a headless server, run `ssh -L 3000:127.0.0.1:3000 user@server-address`
on your computer, then open `http://localhost:3000` there.

## Campus access

After creating the administrator, change `WEBUI_BIND_ADDRESS` in `.env` to
the server's campus IP and rerun the startup command. The interface will be
at `http://SERVER_ADDRESS:3000`. Approve users in the Admin Panel, or disable
signup and create accounts yourself.

Coordinate campus-only access and HTTPS with IT. This setup serves HTTP;
use the SSH tunnel for account access until HTTPS is available. Only the web
port is published; Ollama stays inside the Docker network.

## Project files

- `compose.yaml`: services, GPU access, and storage.
- `.env`: local settings and the private secret.
- `data/`: accounts, conversations, settings, and downloaded models.

See [DEVELOPMENT.md](DEVELOPMENT.md) for everyday commands, resets, and backup
and restoration. Local settings and data are excluded from Git.
