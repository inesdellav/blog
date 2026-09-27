# AGENTS.md

Guida per agenti AI che lavorano in questo repo. Per gli umani vedi `README.md`.

## Cos'è

Blog personale statico: **Hugo** (≥ 0.166.0, extended) + tema **Zen** (`themes/zen`,
incluso nel repo) + **GitHub Pages** via Actions. Nessun Node/Bun, nessun bundler: Hugo
fa da solo (pipeline CSS/JS via Hugo Pipes).

- URL: <https://www.colorimancanti.it/> — `baseURL` in `hugo.toml`, dominio in `static/CNAME`.
- Lingua: italiano, `defaultContentLanguage = 'it'`, contenuti in `content/` (non in sottocartella lingua).
- Tema a monte: <https://github.com/zenpe/zen>. Gli override del sito stanno in `layouts/`.

## Comandi

```bash
hugo server -D                            # dev server
hugo --minify --gc                        # build in public/
```

Non c'è un runner di test: la verifica è `hugo --minify --gc` che deve finire senza errori.

## Mappa del codice

| Percorso | Ruolo |
|---|---|
| `hugo.toml` | Config: `baseURL`, lingua, menu, `outputs` (FlexSearch/ArchiveJSON), `params` |
| `content/about.md` | Pagina "Chi sono?" (`layouts/page.html` del tema) |
| `content/posts/` | Sezione blog; la home elenca `where .Site.RegularPages "Type" "posts"` |
| `content/archive/_index.md` | Pagina `/archive/`; `outputs` aggiunge `archive-month.json` |
| `i18n/it.toml` | Stringhe UI in italiano (il tema ne ha solo en/zh) |
| `layouts/_partials/head/css.html` | Override: compila lo SCSS del tema + link favicon |
| `layouts/_partials/head/js.html` | Override: concatena `main.js`+`search.js`, **esclude** `busuanzi.js` |
| `layouts/_partials/footer.html` | Override: sezione link utili solo se `friendshipLinks` è definito |
| `layouts/_partials/sidebar.html` | Override: card Categorie/Tag solo se esistono tassonomie; niente contatori visite |
| `layouts/404.html` | Pagina 404 (la usa GitHub Pages) |
| `static/` | favicon, apple-touch-icon, icone PWA |
| `themes/zen/` | Tema Zen vendored (non modificare: gli override vanno in `layouts/`) |
| `.github/workflows/deploy.yml` | Build Hugo + deploy su Pages |

## Invarianti

1. **Non modificare `themes/zen/`**: sovrascrivi in `layouts/` / `assets/` / `i18n/` / `static/`,
   che hanno precedenza sul tema.
2. **`head/js.html` esclude `busuanzi.js`** di proposito: carica uno script di analytics
   esterno (`busuanzi.ibruce.info`). Non reintrodurlo.
3. **Radice del sito.** Il sito vive in radice sul dominio personalizzato `www.colorimancanti.it`; `baseURL` deve restare `https://www.colorimancanti.it/` e `static/CNAME` deve contenere `www.colorimancanti.it`.
4. **Ricerca.** `assets/js/search.js` del tema cerca l'indice in `/<lingua>/flexsearch.json`
   tranne che per il cinese; per questo `[outputFormats.FlexSearch]` ha `path = 'it'`.
   Se cambi la lingua, aggiorna quel valore.
5. **Contenuti.** Frontmatter YAML: `title` obbligatorio; `date`, `description`, `categories`,
   `tags`, `featured_image` opzionali. Articoli in `content/posts/`.
