# Klinik Aagaard

Website for kraniosakral terapi.

## URL
- **Produktion:** https://klinikaagaard.dk
- **Lokal preview:** http://localhost:4321/kliniknew/

## Tech Stack
- **Framework:** Astro 5
- **Styling:** Tailwind CSS 4
- **Build:** Statisk site (SSG)
- **Deployment:** Build til `dist/`, serveres via Caddy

## Mappestruktur
```
src/
├── components/     # Astro komponenter (Nav, Hero, Services, etc.)
├── layouts/        # Layout wrapper
├── pages/          # Sider (kun index.astro)
└── styles/         # CSS filer
public/             # Statiske filer (favicon)
dist/               # Build output (genereret)
```

## Komponenter
Single-page website med sektioner:
- Nav, Hero, Services, About, Testimonials, Prices, Locations, CTA, Footer

## Udvikling
```bash
cd ~/projects/lisbeth-aagaard

# Start dev server
npm run dev

# Build til produktion
npm run build

# Preview build
npm run preview
```

## Deployment
1. Kør `npm run build`
2. Output i `dist/` serveres af Caddy
3. Base path er `/kliniknew` (se `astro.config.mjs`)

## Konfiguration
- `astro.config.mjs` - Astro config med Tailwind plugin og base path
- `tsconfig.json` - TypeScript config
