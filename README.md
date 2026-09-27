# Colori Mancanti

Blog personale di Ines Dellavalle. Sito statico in [Hugo](https://gohugo.io/), tema [Zen](https://themes.gohugo.io/themes/zen/) (`themes/zen`, git submodule), pubblicato su GitHub Pages.

- Sito: <https://inesdellav.github.io/>
- Lingua: italiano (`defaultContentLanguage = 'it'`), contenuto non in sottocartella → gli URL stanno in radice.

## Requisiti

Hugo **extended** ≥ 0.166.0 (il tema compila `assets/css/style.scss` con `toCSS`, che richiede la versione extended).

```bash
# macOS
brew install hugo
# Debian/Ubuntu
sudo dpkg -i hugo_extended_0.166.0_linux-amd64.deb
```

## Comandi

```bash
git submodule update --init --recursive   # scarica il tema (prima volta / dopo un clone)
hugo server -D                            # dev server su http://localhost:1313/
hugo --minify --gc                        # build in public/
```

## Struttura

```
hugo.toml                  # configurazione sito (lingua, menu, output, ricerca)
content/
  about.md                 # pagina "Chi sono?"
  posts/                   # sezione blog (elencata in home e in /posts/)
  archive/_index.md        # pagina /archive/
i18n/it.toml               # stringhe di interfaccia in italiano
layouts/_partials/         # override del tema: head/css, head/js, footer, sidebar
static/                    # favicon e icone (copiati in radice dell'output)
themes/zen/                # tema (submodule)
.github/workflows/deploy.yml
```

## Note sul tema

Il tema Zen è pensato per siti bilingue (en/zh). Questo sito è monolingua, quindi ci sono
alcuni override in `layouts/_partials/`:

- `head/css.html` — aggiunge i link a favicon/apple-touch-icon.
- `head/js.html` — esclude `busuanzi.js` (contatore visite di terze parti, `busuanzi.ibruce.info`).
- `sidebar.html` — nasconde le card Categorie/Tag quando non ci sono tassonomie e rimuove i contatori di visite.
- `footer.html` — nasconde la sezione "Link utili" quando `friendshipLinks` è vuoto.
- `header.html` — rimuove il selettore di lingua (sito monolingua).
- `../baseof.html` — aggiunge `<link rel="canonical">` e usa `.Site.Language.Direction`.
- `../robots.txt` — aggiunge la riga `Sitemap:`.

`hugo.toml` pubblica una copia extra dell'indice di ricerca in `/it/flexsearch.json`, perché
`assets/js/search.js` del tema cerca il file in `/<lingua>/flexsearch.json` per tutte le lingue
che non siano il cinese.

## Deploy

`.github/workflows/deploy.yml` costruisce il sito con Hugo e lo pubblica su GitHub Pages
(`actions/upload-pages-artifact` + `actions/deploy-pages`) a ogni push su `main`.

L'URL di destinazione è in `baseURL` dentro `hugo.toml`:
`https://www.colorimancanti.it/`. Il dominio personalizzato è dichiarato in `static/CNAME`
(che Hugo copia in `public/`), quindi non serve rinominare il repository.

Per il dominio servono, una volta sola, lato DNS e GitHub:

1. In `inesdellav.github.io` (o `<user>.github.io`) crea un record `CNAME` per `www` →
   `<user>.github.io`.
2. Per l'apice `colorimancanti.it`, aggiungi il redirect/`A` record verso gli IP di GitHub Pages.
3. In **Settings → Pages** imposta *Custom domain* = `www.colorimancanti.it` e attiva
   *Enforce HTTPS*.
