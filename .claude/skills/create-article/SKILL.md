# Skill : Creer un article

Ce skill genere un article de blog optimise SEO et/ou GEO selon le type choisi.

## Declenchement

L'utilisateur tape `/create-article` ou demande de creer/rediger un article.

## Etape 1 — Collecter les informations

### Mot-cle et categorie

Demander a l'utilisateur :
- **Mot-cle principal** : le terme sur lequel l'article doit se positionner
- **Categorie** : dans quelle categorie du blog ? (proposer les categories existantes)

Si l'utilisateur n'a pas de mot-cle precis, l'aider a en choisir un en fonction de la thematique du site.

### Type d'article

Lister les templates disponibles en lisant le contenu de `.claude/templates/articles/` et les presenter a l'utilisateur.

Types disponibles par defaut :

| Type | Fichier | Description | Quand l'utiliser |
|------|---------|-------------|-----------------|
| **Article standard** | `article-standard.md` | Article informatif complet, optimise SEO + GEO | Intention informationnelle (c'est le type par defaut) |
| **Comparatif** | `geo-comparatif.md` | Article de classement/comparatif avec mise en avant d'une marque ou d'un produit | Intention comparative ("meilleur X", "X vs Y", "classement") |

**IMPORTANT :** Toujours lire le contenu reel de `.claude/templates/articles/` au moment de l'execution. Si de nouveaux fichiers template ont ete ajoutes par l'utilisateur, les proposer aussi. Le dossier fait reference, pas ce tableau.

Si l'utilisateur ne sait pas quel type choisir, l'aider en analysant l'intention de recherche du mot-cle.

### Informations complementaires selon le type

- **GEO Comparatif (OBLIGATOIRE)** : l'utilisateur DOIT fournir la marque, le produit ou la solution a mettre en avant, ainsi que 2-3 concurrents a comparer. Ce type d'article sert a positionner favorablement un element par rapport aux autres dans un comparatif objectif. Ne jamais rediger sans cette information

## Etape 2 — Audit des contenus existants et maillage interne

### Analyse du site

Lister tous les articles existants dans `content/blog/` en lisant le sitemap (`content/plan-du-site.md` ou directement les fichiers dans `content/blog/`). Pour chaque article existant, noter :
- Le titre
- Le mot-cle principal (visible dans le title et le nom du fichier)
- La categorie

### Verification de cannibalisation

Verifier qu'aucun article existant ne cible deja le meme mot-cle principal. Si cannibalisation detectee, prevenir l'utilisateur et proposer un angle different.

### Identification des liens internes

Identifier **au minimum 3 articles existants** thematiquement proches du nouvel article pour le maillage interne. Privilegier :
1. Les articles de la **meme categorie**
2. Les articles qui partagent des **tags communs**
3. Les articles dont le sujet est **complementaire** (pas identique, mais lie)

**Regles de maillage interne :**
- **Minimum 3 liens internes** vers d'autres articles du blog, inseres de maniere contextuelle dans le corps de l'article
- L'**ancre du lien** (le texte cliquable) doit contenir le **mot-cle principal de l'article cible** — pas de "cliquez ici" ou "lire aussi"
- Les liens doivent etre **naturels et contextuels** : inseres dans une phrase qui a du sens, pas en liste en bas de page
- Repartir les liens dans differentes sections de l'article (pas tous au meme endroit)

Exemple :
- Si l'article cible s'appelle "Les bienfaits du the vert", l'ancre doit etre quelque chose comme : "Comme nous l'avons vu dans notre article sur les **[bienfaits du the vert](/blog/bienfaits-the-vert/)**, ..."
- PAS : "Pour en savoir plus, [cliquez ici](/blog/bienfaits-the-vert/)"

Si le site a moins de 3 articles, faire le maximum avec ce qui existe. Si le site est vide (premier article), noter dans un commentaire les futurs liens a ajouter quand d'autres articles seront publies.

## Etape 3 — Redaction

Lire le template correspondant dans `.claude/templates/articles/[type-choisi].md` et l'utiliser comme squelette.

### Frontmatter

| Champ | Regle |
|-------|-------|
| `title` | Contient le mot-cle principal, max ~65 caracteres |
| `description` | Meta description, max 140 caracteres, contient le mot-cle |
| `date` | Date du jour (YYYY-MM-DD) |
| `lastmod` | Date du jour (YYYY-MM-DD) — identique a `date` a la creation. Mettre a jour si l'article est modifie plus tard |
| `categories` | La categorie choisie |
| `tags` | 5-8 tags pertinents |
| `author` | Nom de l'auteur (lire dans le CLAUDE.md section "Contexte du site" > auteur) |
| Nom du fichier | Slug = mot-cle en minuscules, tirets, sans accents |

### Regles communes a tous les types

| Spec | Valeur |
|------|--------|
| H1 | Jamais dans le body (genere par Hugo depuis le title) |
| Densite mot-cle | 1-2% |
| Mots-cles en gras | Oui, `**mot-cle**` |
| Ton | Impersonnel (pas de je/tu/nous/vous) sauf si precise autrement dans le CLAUDE.md |
| Liens internes | Min. 3 liens contextuels vers des articles existants (ancre = mot-cle de l'article cible) |
| FAQ | 3-5 questions en fin d'article |

### Regles specifiques par type

Lire les commentaires HTML `<!-- NOTES POUR CLAUDE -->` en bas du template choisi. Ils contiennent les specs propres a ce type d'article :
- Nombre de mots minimum
- Blocs obligatoires (quick summary, tableaux, citations, etc.)
- Objectif editorial
- Ton specifique eventuel

**Toujours suivre ces notes.** Elles priment sur les regles communes si conflit.

## Etape 4 — Verification (checklist)

- [ ] Slug = mot-cle en minuscules, tirets, sans accents
- [ ] Title contient le mot-cle, < 65 caracteres
- [ ] Meta description <= 140 caracteres, contient le mot-cle
- [ ] Auteur renseigne dans le frontmatter
- [ ] Structure Hn conforme au type (voir notes du template)
- [ ] Nombre de mots minimum atteint (voir notes du template)
- [ ] Mots-cles en gras
- [ ] Ton correct
- [ ] Min. 3 liens internes contextuels (ancres = mots-cles des articles cibles)
- [ ] Blocs obligatoires presents selon le type
- [ ] FAQ presente avec balises `<details>/<summary>` (accordeon)
- [ ] Build Hugo OK (`hugo`)

## Etape 5 — Sauvegarde et build

```bash
hugo
```

Afficher a l'utilisateur :
- Type d'article utilise
- Nombre de mots
- Nombre de H2
- Liens internes ajoutes
- Proposer de voir le resultat en local (`hugo server`)
