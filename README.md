# Salón Camarena — Mirror en GitHub Pages

Réplica estática del sitio [saloncamarena.com](https://saloncamarena.com)
hosteada en GitHub Pages: **<https://aguitech.github.io/saloncamarena/>**

> **Sitio origen:** SPA Vite (React) servida estáticamente desde Cloudflare.
> **Este mirror:** clon 1:1 de la build estática (`index.html` + assets bundle),
> sin servidor, sin backend, sin analytics.

## Estructura

```
saloncamarena/
├── index.html                              (2 KB, shell React)
├── favicon.ico                             (18 KB)
├── og-image.png                            (96 KB, Open Graph preview)
├── robots.txt
├── assets/
│   ├── index-BfQCAcpW.css                  (78 KB, todos los estilos)
│   ├── index-GBuhbScj.js                   (473 KB, bundle JS principal)
│   ├── salon-camarena-logo-blanco-DVwO1LEj.png   (71 KB, logo)
│   └── tunnel-color-bars-CFHhJOfr.png     (205 KB, barras decorativas)
└── fonts/
    └── Minecraftia-Regular.ttf             (70 KB, fuente pixel)
```

## Decisiones del clon

- **`~flock.js` (Tinybird analytics) eliminado** — apunta al dominio original,
  no funciona fuera de `saloncamarena.com`
- **`og-image` URL reescrita** de `saloncamarenacom.lovable.app` → local `og-image.png`
- **Todas las URLs de assets se mantienen relativas** (`/assets/...`, `/fonts/...`)
  para que Pages las sirva desde la raíz del repo
- **Google Fonts externas** se mantienen vía CDN — son públicas y gratuitas
- **Manifest JSON** (`/assets/...`) — servido por el bundle desde el origen;
  este mirror lo conserva sin tocar

## Reproducir el clon

```bash
UA="Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36"
mkdir -p saloncamarena/assets saloncamarena/fonts

# HTML shell
curl -sL -A "$UA" --compressed https://saloncamarena.com/ -o saloncamarena/index.html

# Assets
for url in \
  https://saloncamarena.com/assets/index-BfQCAcpW.css \
  https://saloncamarena.com/assets/index-GBuhbScj.js \
  https://saloncamarena.com/assets/salon-camarena-logo-blanco-DVwO1LEj.png \
  https://saloncamarena.com/assets/tunnel-color-bars-CFHhJOfr.png \
  https://saloncamarena.com/fonts/Minecraftia-Regular.ttf \
  https://saloncamarena.com/favicon.ico \
  https://saloncamarena.com/og-image.png \
  https://saloncamarena.com/robots.txt; do
  path="${url#https://saloncamarena.com}"
  curl -sL -A "$UA" --compressed "$url" -o "saloncamarena$path"
done

# Quitar flock.js analytics
sed -i 's|<script defer src="/~flock.js"[^<]*</script>||' saloncamarena/index.html
# Reescribir og:image
sed -i 's|https://saloncamarenacom.lovable.app/og-image.png|og-image.png|g' saloncamarena/index.html
```

## Deploy

```bash
gh repo create saloncamarena --public --description "Mirror de saloncamarena.com" --source=. --push
gh repo edit aguitech/saloncamarena --enable-pages --pages-source-branch main --pages-source-path /
```

O con curl (ver skill `github-repo-management`).

## Changelog

- **2026-08-15** — Mirror inicial. 1.0 MB total. Cero modificaciones al JS/CSS bundle.