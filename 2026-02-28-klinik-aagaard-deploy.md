# Opgave: Deploy-setup for Klinik Aagaard + /new-static-site skill

## Kontekst

Klinik Aagaard (`~/projects/lisbeth-aagaard/`) er en statisk Astro-site uden deploy-setup.
Domæne: `klinikaagaard.dk`. GitHub repo: `saabendtsen/klinik-aagaard`.

**Nuværende tilstand:**
- Astro 5 + Tailwind CSS 4, bygger til `dist/`
- Ingen Dockerfile, ingen CI/CD, ingen deploy-mappe, ingen Caddy-config
- `astro.config.mjs` har `base: '/kliniknew'` — skal ændres til `/` (standalone domæne)

## Del 1: Deploy-setup for Klinik Aagaard

### 1.1 Dockerfile (multi-stage)

```dockerfile
FROM node:20-slim AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

Nginx serverer statiske filer fra `dist/`. Ingen custom nginx.conf nødvendig for simple sider.

### 1.2 docker-compose.yml (dev/projekt)

```yaml
services:
  klinik-aagaard:
    build: .
    container_name: klinik-aagaard
    restart: unless-stopped
    ports:
      - "3202:80"
```

Port 3202 er allerede reserveret i `~/deploy/ports.yml` til lisbeth-aagaard.

### 1.3 GitHub Actions workflow

`.github/workflows/build-and-deploy.yml` — samme mønster som de andre projekter:
1. `test` job: `npm ci && npm run build` (for statiske sider er "test" = "kan det bygge?")
2. `build-and-deploy` job: Docker build+push til GHCR, trigger deploy webhook

Reference: `~/projects/taskflow/.github/workflows/build-and-deploy.yml`

### 1.4 Deploy-mappe

`~/deploy/klinik-aagaard/`:

`docker-compose.yml`:
```yaml
services:
  klinik-aagaard:
    image: ghcr.io/saabendtsen/klinik-aagaard:latest
    container_name: klinik-aagaard
    restart: unless-stopped
    ports:
      - "3202:80"
```

`test.yml`:
```yaml
enabled: true
container_port: 80
health_path: /
timeout: 60
```

### 1.5 Fix astro.config.mjs

Ændr `base: '/kliniknew'` til `base: '/'` — siden kører på eget domæne, ikke sub-path.

### 1.6 Caddy-config

Tilføj til Caddyfile:
```
klinikaagaard.dk {
    reverse_proxy localhost:3202
}
```

### 1.7 DEPLOY_TOKEN secret

```bash
echo "1PjGk2lYnzRkRkaJZZv131VHL_p4Vw9jcF6zZeX5MEo" | gh secret set DEPLOY_TOKEN --repo saabendtsen/klinik-aagaard
```

### 1.8 Verifikation

- [ ] `docker compose up -d --build` kører lokalt
- [ ] `curl localhost:3202` returnerer HTML
- [ ] Push til main → GitHub Actions bygger + deployer
- [ ] `curl https://klinikaagaard.dk` virker

---

## Del 2: /new-static-site skill

Simpel skill til at sætte deploy op for statiske sider. Ingen subagents — det er få filer.

### Hvad skillen gør

1. Spørger: app-navn, port, domæne/sub-path
2. Opretter Dockerfile (multi-stage node+nginx) i projektet
3. Opretter docker-compose.yml i projektet
4. Opretter `.github/workflows/build-and-deploy.yml`
5. Opretter `~/deploy/<app>/` med docker-compose.yml + test.yml
6. Sætter DEPLOY_TOKEN secret
7. Committér og push

### Hvad skillen IKKE gør

- Ingen template-kloning (projektet eksisterer allerede)
- Ingen model/route/test tilpasning (ingen backend)
- Ingen Caddy-config (gøres manuelt eller via deploy-center)

### Skill-fil

`~/.claude/commands/new-static-site.md` — ca. 50 linjer. Ingen subagents nødvendigt da omfanget er lille (4-5 filer oprettes).

---

## Rækkefølge

1. Sæt deploy op for Klinik Aagaard manuelt (del 1)
2. Baseret på erfaringen, byg `/new-static-site` skillen (del 2)
3. Test skillen på landing-page (hvis den mangler deploy)
