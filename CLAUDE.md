# Partogramme — suivi du travail en salle de naissance

Ce fichier est lu par Claude Code quand on travaille dans ce dossier. Il complète `../CLAUDE.md`.

## À quoi ça sert

Site pour suivre un accouchement : on choisit une patiente et sa grossesse, on note chaque
examen (dilatation, hauteur, orientation, remarque), et une courbe de dilatation s'affiche.
Un bouton exporte tout en Excel. Les autres onglets gèrent patientes, grossesses (équipe
référente : sage-femme, obstétricien, anesthésiste, pédiatre), enfants et praticiens.

## Fichiers

- `index.html` — toute l'interface, un `<div class="page">` par onglet (suivi, patientes,
  grossesses, enfants, praticiens). Les scripts sont chargés dans cet ordre :
  `config.js`, `app.js`, `grossesses.js`, `examens.js`, `enfants.js`, `praticiens.js`, `export.js`.
- `config.js` — URL du projet Supabase + clé publishable, dans `window.SUPABASE_URL` et
  `window.SUPABASE_KEY`. Ne mettre **que** la clé publishable ici, jamais une clé secrète.
- `app.js` — client Supabase (`db`), navigation entre onglets, page « Patientes »
  (liste + formulaire, création et modification).
- `grossesses.js`, `examens.js`, `enfants.js`, `praticiens.js` — une page chacun,
  même modèle : liste, formulaire, boutons Modifier/Annuler, messages d'état.
- `examens.js` — aussi la page « Suivi du travail » : choix patiente/grossesse, KPI,
  courbe de dilatation (Chart.js, canvas `#courbe`), tableau des examens.
- `export.js` — export Excel via ExcelJS (bouton « Exporter en Excel », désactivé tant
  qu'aucune grossesse n'est choisie).
- `maquette.png` — la maquette d'origine, à respecter pour le style.
- `style.css` — tout le style (police Inter, cartes `.carte`, pastilles, onglets).

## Base de données (Supabase, projet `sclxjrcqncmjlylwnzjl`)

Six tables dans `public` :
- `patiente` (`id_patiente`, nom, nom_marital, prénom, `gs_rh` enum groupe sanguin, date de naissance)
- `grossesse` (`id_grossesse`, `id_patiente`, début, parité, VIH, toxo, durées, 4 liens vers `praticien`)
- `examen` (`id_examen`, `id_grossesse`, date_heure, dilatation 0–10, presentation_hauteur −5 à 5,
  `id_orientation`, remarque)
- `enfant` (`id_enfant`, `id_grossesse`, prénom, `sexe` enum, date de naissance, poids kg, taille cm)
- `praticien` (`id_praticien`, nom, prénom, `specialite` enum)
- `orientation` (`id_orientation`, nom, fichier_pictogramme)

Toutes les tables ont le RLS ouvert en lecture, ajout et modification pour `anon`
(convention des noms : `table_lecture`, `table_ajout`, `table_modification`,
`examen_suppression`). Si une nouvelle table est créée, faire pareil.
Les données du TP sont fictives : pas de vraies données médicales.

## Points d'attention

- Site statique sans build : tester avec un petit serveur local
  (`npx.cmd http-server <dossier> -p 8765 -c-1`, Python n'est pas installé sur ce PC)
  et vérifier avec MCP Playwright. L'option `-c-1` évite le cache du navigateur.
- Bibliothèques depuis CDN : supabase-js@2, chart.js@4, exceljs@4.4.0, fontsource Inter@5.
- Mise en ligne : dépôt GitHub public `partogramme`, workflow `.github/workflows/deploy.yml`
  (ne pas le modifier) — chaque `git push` publie sur
  https://sps-g28-parto.professeurpetitchat.com/.