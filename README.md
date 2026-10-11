# meinkauf.wtf

Osobní rozcestník Vítka Meinkaufa ([@vitkuvcestopiss](https://herohero.co/vitkuvcestopiss)).

Web je postavený jako jeden HTML soubor s obrázkem na pozadí a neviditelnými klikacími oblastmi přes něj. Žádný framework, žádný build step.

## Úprava odkazů

Všechny odkazy jsou v poli `ROZCESTNIK_LINKS` v `index.html`:

```js
const ROZCESTNIK_LINKS = [
  { id: 1, label: 'Hero Hero',             url: 'https://herohero.co/vitkuvcestopiss' },
  { id: 2, label: 'Sudety Culture',        url: 'https://www.sudetyculture.cz' },
  { id: 3, label: 'Kratomia',              url: 'https://www.kratomia.cz/' },
  { id: 4, label: 'Moskva',                url: '#' },  // '#' = odkaz zatím neexistuje
  { id: 5, label: 'Kavamia',               url: '#' },
  { id: 6, label: 'Vítek vysvětluje věci', url: 'https://www.youtube.com/@vitekvysvetlujeveci' },
];
```

`url: '#'` zobrazí vtipnou animaci s hláškou místo přesměrování.

## Úprava hlášek pro neaktivní odkazy

V `index.html` ve funkci `funnyClick`:

```js
var messages = {
  4: 'Moskva? Zatím jen na mapě. 🗺️',
  5: 'Kavamia ještě neotevřela. Brzy! ☕🦥'
};
```

## Debug mód

Přidej `?debug=1` do URL → zobrazí se červené obrysy všech klikacích oblastí:

```
https://meinkauf.wtf/?debug=1
```

Hodí se při ladění pozic odkazů po výměně obrázku.

## Výměna obrázku

Do `assets/` patří:

| Soubor | Použití |
|--------|---------|
| `rozcestnik-pc.webp` | Desktop (primární, WebP, 2048×1150) |
| `rozcestnik-pc.jpg` | Desktop (fallback, JPEG) |
| `rozcestnik-phone.webp` | Mobil (primární, WebP, 1152×2048) |
| `rozcestnik-phone.jpg` | Mobil (fallback, JPEG) |
| `og-image.jpg` | Náhled pro sociální sítě (1200×630) |
| `apple-touch-icon.png` | Ikona na plochu iOS (180×180) |

Fallbacky a náhled vygeneruješ z WebP (macOS):

```bash
sips -s format jpeg -s formatOptions 82 assets/rozcestnik-pc.webp --out assets/rozcestnik-pc.jpg
sips -s format jpeg -s formatOptions 82 -Z 1200 assets/rozcestnik-pc.webp --out assets/og-image.jpg
sips --cropToHeightWidth 630 1200 assets/og-image.jpg
```

Po výměně obrázku zkontroluj pozice odkazů v CSS (sekce `/* Pozice odkazů */`) pomocí debug módu — na desktopu i v mobilní šířce.

## Deploy

Push do větve `main` automaticky spustí GitHub Actions workflow a nasadí na GitHub Pages → [meinkauf.wtf](https://meinkauf.wtf).

```bash
git add .
git commit -m "feat: popis změny"
git push
```

## SSH – více GitHub účtů

Tento repozitář používá SSH klíč `~/.ssh/gitlab` (účet `olidstva`), nastaveno v `.git/config`:

```
[core]
  sshCommand = ssh -i ~/.ssh/gitlab -o IdentitiesOnly=yes
```

Přehled klíčů:
- `~/.ssh/id_ed25519` → GitHub účet `ostudalidstva-dev`
- `~/.ssh/gitlab` → GitHub účet `olidstva` (tento repo)
