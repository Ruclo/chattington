# Chat App
https://chattington.duckdns.org/

This repo contains the full stack for a real-time chat app with:
- a Spring Boot backend (REST + STOMP over RabbitMQ),
- a Vue frontend,
- PostgreSQL for persistence,
- RabbitMQ for messaging,
- Caddy for local HTTPS reverse proxying.

## Structure
- `chat-app/` - Spring Boot backend
- `chat-vue/` - Vue frontend
- `rabbitmq/` - RabbitMQ config (plugins)
- `secrets/` - local key material (not for production)
- `Caddyfile` - local HTTPS reverse proxy

## Local HTTPS (recommended)
### Windows
1) Add host entry (run PowerShell as Administrator):
```powershell
echo 127.0.0.1 chat.local >> C:\Windows\System32\drivers\etc\hosts
ipconfig /flushdns
```

2) Start stack:
```powershell
docker compose up --build
```

3) Trust Caddy local CA:
```powershell
docker compose exec caddy cat /data/caddy/pki/authorities/local/root.crt > caddy-local-root.crt
certutil -addstore -f Root caddy-local-root.crt
```

Open: `https://chat.local`

### Linux
1) Add host entry:
```bash
echo "127.0.0.1 chat.local" | sudo tee -a /etc/hosts
```

2) Start stack:
```bash
docker compose up --build
```

3) Trust Caddy local CA:
```bash
docker compose exec caddy cat /data/caddy/pki/authorities/local/root.crt > caddy-local-root.crt
sudo cp caddy-local-root.crt /usr/local/share/ca-certificates/caddy-local-root.crt
sudo update-ca-certificates
```

Open: `https://chat.local`

### Environment Variables
Before starting the stack, create a `.env` file from the example and adjust as needed:
```bash
cp .env.example .env
```

Key variables:
- `DB_NAME`, `DB_PASSWORD`
- `BROKER_USER`, `BROKER_PASSWORD`
- `CLOUDINARY_URL`
- `DOMAIN_NAME` (hostname for both Caddy and backend allowed origin, default `chat.local`)

## Caddy Hostname
The hostname is parameterized via `DOMAIN_NAME` (set in `docker-compose.yml`).
If you want a different local URL, update:
- `DOMAIN_NAME` in `.env`
- the host entry in `C:\Windows\System32\drivers\etc\hosts`

## Environment
Copy `.env.example` to `.env` and adjust as needed.

## Notes
- RabbitMQ STOMP is enabled via `rabbitmq/enabled_plugins`.
- The backend expects STOMP on port `61613`.
