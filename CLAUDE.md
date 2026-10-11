# CLAUDE.md — meinkauf.wtf

Osobní rozcestník Vítka Meinkaufa ([@vitkuvcestopiss](https://herohero.co/vitkuvcestopiss)), běží na **https://meinkauf.wtf**.

Celý web je **jeden statický `index.html`**: obrázek „mapy" na pozadí a přes něj neviditelné klikací oblasti (`<a>` absolutně pozicované v procentech). Žádný framework, žádný build, žádné závislosti, žádné testy.

## Struktura

| Cesta | Co to je |
|-------|----------|
| `index.html` | Vše — meta/SEO, JSON-LD, CSS, markup, JS (~300 řádků) |
| `assets/rozcestnik-pc.{webp,jpg}` | Desktop obrázek, 2048×1150 (poměr 1024:575); JPEG = fallback generovaný z WebP |
| `assets/rozcestnik-phone.{webp,jpg}` | Mobilní obrázek, poměr 576:1024 |
| `assets/og-image.jpg` | Náhled pro sociální sítě 1200×630 (výřez z desktop WebP) |
| `assets/apple-touch-icon.png` | 180×180 ikona pro iOS (výřez s cedulemi) |
| `assets/*-original.jpg`, `*-phone-2x.png` | Zdrojové soubory, **gitignored**, nenasazují se |
| `404.html` | Samostatná chybová stránka (absolutní cesty `/assets/…`, po 10 s přesměruje na `/`) |
| `robots.txt`, `sitemap.xml` | SEO; `lastmod` v sitemapě se aktualizuje ručně |
| `.github/workflows/static.yml` | Deploy na GitHub Pages při pushi do `main` |
| `README.md` | Návod pro lidi (úprava odkazů, debug, výměna obrázku) |

## Jak funguje index.html

1. `<picture>` vybírá obrázek podle šířky — breakpoint **768 px** (≤768 mobil, ≥769 desktop).
2. `.frame` drží poměr stran obrázku; `.links-layer` (`<nav>`) leží přes něj s `inset: 0`.
3. JS projde `ROZCESTNIK_LINKS` a vygeneruje `<a data-id="N">`. Pozice každého odkazu jsou v CSS
   pod `/* Pozice odkazů */` — **zvlášť pro desktop a pro mobil**, v % rozměrů obrázku.
4. `url: '#'` = odkaz zatím neexistuje → místo navigace `funnyClick(id)`: zatřese rámem a ukáže toast
   s hláškou z objektu `messages` (klíč = `id`).
5. `?debug=1` v URL přidá `body.debug` → červené obrysy klikacích oblastí.

## Při změně odkazů hlídej všechna místa

Stejný seznam odkazů je v souboru **na několika místech**, která se musí udržovat ručně v synchronu:

- `ROZCESTNIK_LINKS` (JS) — zdroj pravdy pro klikací oblasti
- CSS pozice `a[data-id="N"]` — desktop i mobilní media query
- `messages` ve `funnyClick` — jen pro odkazy s `url: '#'`
- `<noscript>` fallback — obsahuje jen **aktivní** odkazy (bez `'#'`)
- JSON-LD `sameAs` — profily/weby Vítka
- `description` / `og:description` / `twitter:description` — výčet projektů v textu

Po změně obrázku nebo pozic vždy ověř v `?debug=1` na desktopové i mobilní šířce.

## Lokální spuštění

```bash
npx serve -p 3456 .        # http://localhost:3456  (viz .claude/launch.json)
# nebo
python3 -m http.server 3456
```

## Deploy

Push do `main` → GitHub Actions zkopíruje do `_site/` jen `index.html`, `404.html`, `robots.txt`,
`sitemap.xml` a `assets/` a ty nasadí na GitHub Pages. **Nový soubor, který má být na webu,
je potřeba přidat do kroku „Prepare site" v `.github/workflows/static.yml`.**

Remote: `git@github.com:olidstva/mein-kauf.wtf.git`. Repo používá SSH klíč `~/.ssh/gitlab`
(GitHub účet `olidstva`) přes `core.sshCommand` v `.git/config` — neměnit na výchozí klíč.

## Konvence

- Kód, komentáře, commity i texty na webu **česky**.
- Commity: `<type>: <popis>` (feat, fix, refactor, docs, chore, …).
- Držet se jednoho souboru a vanilla JS (ES5 styl `var`/`function` v handlerech) — nepřidávat build ani knihovny.
- Externí odkazy vždy `target="_blank" rel="noopener noreferrer"`.

## Pozor na

- WebP je zdroj pravdy pro obrázky. Fallback JPEG, `og-image.jpg` a ikonu po změně WebP přegeneruj (příkazy v README).
- Klikací oblasti se nesmí překrývat — mezi `top + height` jednoho a `top` dalšího nech mezeru.
- Headless Chrome má minimální šířku okna 500 px, takže mobilní screenshot při 390 px vypadá oříznutý. Pro kontrolu použij iframe nebo DevTools emulaci.
