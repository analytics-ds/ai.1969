# Hugo Site Factory

Ce repo est un template pour creer des sites blogs statiques avec Hugo, optimises SEO/GEO, heberges gratuitement sur GitHub Pages.

## Comment ca marche

Ce repo ne contient pas de site. Il contient les **instructions et templates** pour que Claude Code genere un site complet automatiquement.

### Premier lancement

1. L'utilisateur connecte Claude Code a ce repo
2. L'utilisateur tape `/create-site`
3. Claude pose les questions necessaires (nom du site, couleurs, categories, etc.)
4. Claude genere tout le site Hugo, les fichiers SEO, et configure le deploiement
5. L'utilisateur push sur GitHub, active GitHub Pages, le site est en ligne

### Utilisation courante

- `/create-article` : creer un nouvel article de blog (choix parmi plusieurs types : article standard, comparatif)
- `/seo-setup` : generer ou mettre a jour les fichiers SEO techniques de base (robots.txt, llms.txt, sitemap, structured data)
- `/seo` : mode interactif pour modifier/ajouter des elements SEO (meta tags, JSON-LD, audit on-page, etc.)

## Structure du repo

```
.claude/
├── skills/
│   ├── create-site.md           ← Workflow creation de site complet
│   ├── create-article.md        ← Workflow creation d'article (multi-types)
│   ├── seo-setup.md             ← Workflow fichiers SEO techniques (baseline)
│   └── seo.md                   ← Mode interactif SEO (modifications ponctuelles)
└── templates/
    ├── hugo-workflow.yml         ← GitHub Actions CI/CD
    ├── main.css                  ← CSS avec variables de charte graphique
    ├── articles/                 ← Templates d'articles par type
    │   ├── article-standard.md   ← Article informatif SEO + GEO (type par defaut)
    │   └── geo-comparatif.md     ← Article comparatif avec mise en avant
    ├── seo/                      ← Fichiers SEO techniques (editables)
    │   ├── robots.txt            ← Modele robots.txt
    │   ├── llms.txt              ← Modele llms.txt
    │   └── structured-data/      ← Schemas JSON-LD
    │       ├── article.json      ← BlogPosting
    │       ├── organization.json ← Organization
    │       ├── author.json       ← Person (auteur)
    │       ├── breadcrumb.json   ← BreadcrumbList
    │       ├── website.json      ← WebSite
    │       └── faq.json          ← FAQPage (a integrer manuellement)
    ├── layouts/
    │   ├── baseof.html           ← Layout de base
    │   ├── home.html             ← Page d'accueil
    │   ├── list.html             ← Pages de liste
    │   ├── single.html           ← Page article (avec affichage auteur)
    │   └── sitemap-html.html    ← Page plan du site (liste toutes les pages)
    └── partials/
        ├── header.html           ← Header/navigation
        ├── footer.html           ← Footer
        └── seo-head.html         ← Meta tags SEO + JSON-LD (OG, Twitter, canonical, schemas)
```

## Contexte du site

> Cette section est remplie automatiquement par le skill `/create-site`.
> Elle permet a Claude de connaitre le contexte du site pour les futures actions.

- **Nom du site** : 1969 — L'Art du Desir
- **Description** : Le guide BDSM et vie de couple par 1969. Explorez l'art du desir, la sexualite epanouie et les dynamiques de couple.
- **URL** : https://ai.1969.fr/
- **Couleurs** : Primary #1e1c1c, Accent #cc5500, Background #fffcfa, Background-alt #f7f3f0, Border #e0d8d0
- **Polices** : Headings: Outfit (300-600), Body/UI: Inter (300-500)
- **Categories** : BDSM, Vie de couple, Sexualite (EN: BDSM, Relationships, Sexuality)
- **Langue** : Francais (principal) + Anglais (sous-dossier /en/)
- **Auteur** : Camille Lefevre
- **URL auteur** : https://ai.1969.fr/a-propos/
- **Fonction auteur** : Journaliste specialisee en sexualite et vie de couple

## Regles generales

- Toujours utiliser `relURL` dans les templates Hugo pour les liens (compatibilite GitHub Pages)
- Les articles vont dans `content/blog/`
- Les slugs sont en minuscules, sans accents, mots separes par des tirets
- Ne JAMAIS utiliser `&` dans les noms de categories ou de tags — toujours remplacer par "et" (Hugo genere un double tiret `--` dans le slug, ce qui casse les URLs)
- Le ton des articles est impersonnel (pas de je/tu/nous/vous) sauf instruction contraire
- Les specs d'article (mots minimum, H2, blocs obligatoires) dependent du type choisi — lire les `<!-- NOTES POUR CLAUDE -->` dans chaque template d'article
- Chaque article doit contenir au minimum 3 liens internes contextuels vers d'autres articles du blog. L'ancre de chaque lien doit contenir le mot-cle principal de l'article cible
- L'auteur est ajoute automatiquement dans le frontmatter et affiche sur la page (configure dans `hugo.toml [params]`)
- Les templates SEO dans `.claude/templates/seo/` sont editables par l'utilisateur — toujours lire la version en place avant de generer
- Pour ajouter un nouveau type d'article, creer un `.md` dans `.claude/templates/articles/` — il sera automatiquement propose par `/create-article`
- Pour ajouter un schema JSON-LD, creer un `.json` dans `.claude/templates/seo/structured-data/` et utiliser `/seo` pour l'integrer
- Chaque article doit avoir un champ `lastmod` dans le frontmatter (= date de derniere modification). Il est utilise par le sitemap XML, le sitemap HTML et le schema JSON-LD
- Quand un article est modifie, toujours mettre a jour le champ `lastmod` avec la date du jour
- Le sitemap HTML (`/plan-du-site/`) se regenere automatiquement a chaque build Hugo
- Toujours build et verifier (`hugo`) avant de commit

## Comment repondre a l'utilisateur

- Tutoiement, ton decontracte
- Pas de jargon technique sans explication
- Reponses structurees avec listes a puces
- Pas d'emoji sauf demande explicite

## Publications evergreen automatiques

En plus des articles GEO (geo-comparatif, rediges a la main via `/create-article`), chaque blog peut publier automatiquement des articles evergreen SEO. Deux methodes coexistent dans le reseau, le choix se fait par blog en fonction du contexte (modele, frequence, fetch concurrents, maillage).

### Methode 1 : CCR cloud auto (`/create-article-auto`)

- **Skill** : `/create-article-auto`
- **Execution** : sandbox cloud Anthropic (CCR), declenchee par une routine `/schedule` (cron 2x/semaine, mardi + vendredi 3h du matin)
- **Modele** : Sonnet 4.6 force (Opus 4.7 a un bug Stream idle timeout en CCR)
- **Fetch concurrents** : bloque par le sandbox (aucun acces aux domaines commerciaux), analyse limitee aux metadonnees SerpAPI (titles + snippets + PAA)
- **Maillage cross-batch** : non (1 article a la fois)
- **Publication** : push immediat -> en ligne tout de suite
- **Cas d'usage** : tient la cadence sans intervention humaine, ideal pour les blogs avec roadmap stable
- **Exemple en prod dans le reseau** : `como-blog-ai`

### Methode 2 : batch local + GitHub Actions cron (`/create-article-evergreen`)

- **Skill** : `/create-article-evergreen` polyvalente
- **Execution** : Mac de Damien (local), Opus 4.7 sans contrainte
- **Modele** : Opus 4.7 (qualite max, pas de bug timeout)
- **Fetch concurrents** : marche normalement, analyse SERP avec lecture des 3-5 pages concurrentes
- **Maillage cross-batch** : oui (les articles produits dans une meme batch se citent entre eux)
- **3 modes au choix** :
  - **(A) Roadmap blog** : N premieres entrees `todo` triees par scheduled_date
  - **(B) Roadmap externe** : roadmap fournie par l'utilisateur (Sheet, KW client)
  - **(C) KW a la demande** : 1 ou plusieurs KW dans le chat
- **3 strategies de scheduling** :
  - Garder les `scheduled_date` source (defaut)
  - Cascade remapping a partir d'une date X (decale en avant)
  - Prochain slot dispo dans la cadence (mardi/vendredi non occupe)
- **Publication** : article ecrit avec `publishDate` futur. Hugo (`buildFuture: false`) le masque jusqu'a la date. GitHub Actions cron mardi/vendredi 3h Paris rebuild le site, l'article apparait automatiquement quand sa date est arrivee.
- **Cas d'usage** : production en lot mensuelle, qualite max, maillage interne propre
- **Exemple en prod dans le reseau** : `ma-bonne-sante`

### Principe commun aux 2 methodes

- **SEO pur**, pas GEO : pas de "prompt GEO", pas de "En bref numerote". Juste un mot-cle SEO cible, analyse SERP, structure Hn basee sur les concurrents, redaction optimisee.
- **Bilingue FR + EN** comme tous les articles du reseau (trad directe de la version FR).
- **Human in the loop** uniquement sur la roadmap : c'est l'humain qui decide des mots-cles a cibler et de leur date de publication.

### Roadmap editoriale

Fichier : `roadmap.yaml` a la racine du blog. Format documente dans `.claude/templates/roadmap-template.yaml`.

Chaque entree = 1 article a publier. Champs editables par l'humain :
- `kw` (obligatoire) : mot-cle SEO principal dans la langue principale du blog
- `category` (obligatoire) : doit matcher une categorie definie dans `hugo.toml`
- `scheduled_date` (obligatoire) : date a partir de laquelle l'agent peut publier (YYYY-MM-DD)
- `status` : `todo` | `done` | `failed`

Champs remplis par l'agent (ne pas toucher sauf pour reactiver un `failed`) :
- `published_date`, `published_url_fr`, `published_url_en`, `error`

### Comment l'humain modifie la roadmap

- **Ajouter une entree** : copier un bloc existant, remplir `kw` + `category` + `scheduled_date`, laisser les autres champs tels quels, garder `status: todo`.
- **Reporter une entree** : modifier `scheduled_date`.
- **Annuler une entree non encore traitee** : supprimer le bloc, ou passer `status` a `done` manuellement (l'agent l'ignorera).
- **Debloquer un `failed`** : corriger la cause (ex: `kw` trop concurrentiel, category invalide), repasser `status: todo`, vider `error`.

Demander a Claude "ajoute telle entree a la roadmap du blog X" ou "passe la roadmap de X ca" fonctionne aussi, tant que le format YAML reste respecte.

### Execution

- **Manuelle (test methode 1)** : se placer dans le dossier du blog, taper `/create-article-auto`. L'agent prend la prochaine entree eligible et deroule.
- **Planifiee (production methode 1)** : routine `/schedule` qui lance `/create-article-auto` dans le contexte du blog, 2x/semaine (mardi + vendredi, 3h du mat recommande pour minimiser les conflits avec les autres consultants).
- **Batch (methode 2)** : se placer dans le dossier du blog, taper `/create-article-evergreen`. La skill propose les 3 modes (A/B/C) puis les 3 strategies de scheduling. Articles produits avec `publishDate` futur, Hugo les masque, le cron GitHub Actions du blog (mardi/vendredi 3h Paris) les rend visibles automatiquement quand leur date arrive.

### Echecs

Une entree qui echoue passe en `status: failed` avec `error: "[etape] [message]"`. Elle n'est **pas retentee automatiquement**. L'humain corrige, repasse en `todo`, l'agent la reprendra au lancement suivant.

Le suivi des articles publies en auto se fait via :
- Le champ `published_date` / URLs de chaque entree de la roadmap
- Le `MEMORY.md` a la racine du blog (suffixe ` | auto` sur les lignes generees par cette skill)

