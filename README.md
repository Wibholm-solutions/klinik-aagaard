# Klinik Aagaard – website

Kildekoden til [klinikaagaard.dk](https://klinikaagaard.dk), et statisk
one-page website for Klinik Aagaard (kraniosakral terapi). Bygget med
[Astro](https://astro.build) 5 og Tailwind CSS 4 og udrullet som statiske filer
i et `nginx:alpine`-image.

> *English:* Source for a static, Danish-language single-page website for a
> craniosacral therapy clinic, built with Astro and Tailwind CSS. All
> user-facing content is in Danish.

## Status

Færdigt kundewebsite, der vedligeholdes lejlighedsvis ved indholdsændringer.
Ingen releases eller changelog. Repositoriet er offentliggjort som
reference-implementation; det er ikke en skabelon, der holdes generel.

## Struktur

```
src/
├── components/   # Nav, Hero, Services, About, Testimonials, Prices, Locations, CTA, Footer
├── layouts/      # Layout.astro (head, fonte, global CSS)
├── pages/        # index.astro (eneste side)
└── styles/       # global.css (Tailwind)
public/           # favicon
```

`astro.config.mjs` sætter `base` (stien sitet serveres under). Se også
`2026-02-28-klinik-aagaard-deploy.md` for det oprindelige udrulningsnotat og
`CLAUDE.md` for agentinstruktioner.

## Udvikling

Kræver Node.js 20 eller nyere.

```sh
npm ci
npm run dev       # http://localhost:4321/<base>/
npm run check     # astro check
npm run lint      # biome lint
npm run build     # statisk output i dist/
npm run preview
```

Container: `docker build -t klinik-aagaard .` og kør med `-p 8080:80`.
Workflowet i `.github/workflows/ci-cd.yml` udruller `master` via
vedligeholderens egen self-hosted runner og er specifikt for det miljø.

## Data og privatliv

Sitet er helt statisk og indsamler ingen data: ingen formularer, cookies eller
analytics. Ved indlæsning henter browseren skrifttyper fra Google Fonts og ét
portrætbillede fra klinikkens eksisterende domæne; Googles og klinikkens egne
vilkår gælder for de kald.

Indholdet (tekster, priser, adresser, udtalelser med fornavn/initial og
portrættet) er klinikkens offentliggjorte markedsføringsmateriale og gengives
her, som det vises på det live website. Repositoriet indeholder ingen
legitimationsoplysninger og ingen persondata ud over det.

## Tredjepartsmateriale og rettigheder

Afhængigheder er udgivet under permissive licenser: Astro, Tailwind CSS og
Biome (MIT / Apache-2.0), TypeScript (Apache-2.0). Skrifttyperne Cormorant
Garamond og Jost indlæses fra Google Fonts under SIL Open Font License og er
ikke vendoreret.

**Kildekoden** er MIT-licenseret (se nedenfor). **Indhold, navn, logo og
billeder tilhører Klinik Aagaard** og er ikke omfattet af licensen; de må ikke
genbruges uden klinikkens tilladelse.

## Sikkerhed

Se [SECURITY.md](SECURITY.md) for, hvordan en sårbarhed rapporteres.

## Licens

Kode: [MIT](LICENSE) © 2026 Wibholm Solutions. Indhold: © Klinik Aagaard.
