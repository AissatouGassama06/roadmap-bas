# CLAUDE.md — Roadmap CRM & Agentique (Aïssatou Gassama)

Mode d'emploi pour mettre à jour et publier la roadmap CRM & Agentique de bout en bout, sans intervention manuelle. Ce dossier contient le dépôt Git publié sur GitHub Pages.

## Contexte

- **Qui** : équipe CRM & Data Ops iAdvize — Aïssatou Gassama & Henri de Cargouet.
- **Quoi** : une roadmap Q2 2026 en HTML, déclinée en 2 fichiers (interne + externe).
- **Rythme** : mise à jour chaque lundi matin (tâche planifiée `weekly-roadmap-update`). Ce fichier décrit la même procédure pour qu'elle tourne seule.
- **Source de vérité des statuts** : Monday.com. En cas de doute, Monday prime sur tout le reste.
- **Règle d'intégrité** : ne jamais inventer un statut, une date ou un chiffre. Si une source est inaccessible, le signaler avec ⚠ plutôt que d'estimer.

## Emplacement des fichiers

Le dossier a été déplacé depuis `Downloads` vers la racine utilisateur. Chemin actuel :

```
/Users/aissatougassama2/Roadmaps-Projets/roadmap-bas/
├── roadmap-business-agentic-systems.html          # version INTERNE (3 onglets : OKRs, Roadmap, Suivi 19 briefs)
├── roadmap-business-agentic-systems-externe.html  # version EXTERNE (2 onglets : Roadmap, Suivi 19 briefs — PAS d'OKR)
└── .git/                                  # dépôt publié sur GitHub Pages
```

Si le dossier est de nouveau déplacé, demander l'accès au nouvel emplacement avant toute opération (les chemins ci-dessous sont à adapter).

## Sources à consulter (dans cet ordre)

### 1. Monday.com — board des projets/briefs
- **Board** : `👷🏽 BUILD | CRM & Agentic Planning` — ID **4907730144** (workspace « Business & Agentic Systems »).
- Utiliser le MCP Monday. La colonne de statut est `status`. Pour récupérer beaucoup d'items sans dépasser la limite de tokens, demander uniquement les colonnes utiles : `["status","date_mm38vffe","date_mm3889ty"]` (Finished Date / Start Date) avec `limit:200`.
- **Correspondance statut Monday → roadmap** :
  - `Finished` → **done**
  - `Working on it`, `Unit Tests In Progress`, `UAT In Progress`, `Ready to deploy` → **wip**
  - `To Do`, `Planned`, `Not started` → **todo**
  - `Postponed`, `Suspended`, `Canceled` → traiter au cas par cas (ne pas passer en done).
- **Piège — doublons** : certains briefs existent en double sur le board (ex. « Approval Process », « Product Feedback » apparaissent dans le groupe d'avril ET dans le groupe de juin). Toujours retenir l'item le plus récent / celui du groupe du mois en cours, pas l'ancien.
- Board des **tickets** (séparé) : `🛟 TICKETS | CRM Case Management` — ID **3713814023**. Utile seulement pour le suivi du backlog tickets.

### 2. Notes quotidiennes Henri × Aïssatou
- Fichier déposé chaque matin par la tâche `rappel-notes-gdoc` :
  `My Drive/Work OS/notes-henri-aissatou.md` (Google Drive).
- Accès via le **connecteur Google Drive** (chercher le titre `notes-henri-aissatou.md`), PAS via un chemin local : le dossier `GoogleDrive-.../My Drive/` n'est pas monté pour Claude.
- Si le fichier est absent ou date de plus de 7 jours : noter « notes non disponibles cette semaine » et continuer avec Monday seul.
- En extraire : avancement projets, nouveaux projets démarrés, blocages, validations reçues. Les notes complètent Monday mais ne le contredisent pas (Monday prime).

### 3. Les 2 fichiers HTML actuels (ce dossier)
Les lire avant toute modif pour repartir de l'état réel.

## Ce qu'il faut mettre à jour dans les 2 fichiers

À chaque passage :

1. **Date du header** : `Mise a jour : <date du jour>` (format « 8 juin 2026 »).
2. **Constante JS `TODAY`** : `const TODAY=new Date(2026,MOIS_0indexed,JOUR);` (mois en base 0 : juin = 5).
3. **Légende** : le repère « | <date> » du marqueur « aujourd'hui ».
4. **Statuts des projets** dans le tableau JS `P=[...]` (l'onglet Roadmap/Gantt) : passer `wip→done`, `todo→wip`, etc. selon Monday.
5. **Statuts des briefs** dans le tableau JS `briefs=[...]` (onglet Suivi 19 briefs) : champ `st`, plus `d` (date) et `note` si pertinent.
6. **Cartes de stats** (haut de page) :
   - *Briefs livrés* : `X/19`
   - *Projets en cours* : nombre d'items `st:'wip'` dans `P` (toutes catégories : CRM + Agentique + Infra)
   - *Projets agentiques* : total + « dont N en cours »
7. **Barre de progression OKR briefs** : recalculer les 3 segments (done/19, wip/19, todo/19) en %.
8. **Barres OKR** (version interne uniquement) : adoption Dust, doc Henri, agent QuoteHelper, etc. si l'info bouge.

### Règle de synchronisation
Les 2 fichiers doivent contenir **exactement les mêmes données** (tableaux `P` et `briefs`, stats, dates). Seule différence : la version externe **n'a pas l'onglet OKRs** (ni le bloc `tab-okrs`). Ne jamais ajouter d'onglet OKR à la version externe.

### Cohérence à vérifier (erreur déjà rencontrée)
La carte « Projets en cours » et la carte « Projets agentiques – dont N en cours » doivent être cohérentes avec le nombre réel d'items `wip` dans `P`. Recompter à chaque fois plutôt que d'incrémenter de tête.

## Publication GitHub Pages — via le connecteur GitHub (méthode par défaut)

La publication se fait **par le connecteur GitHub (MCP), PAS par `git push` en local**.

Pourquoi : le bac à sable de Claude n'a pas d'accès réseau à github.com (ni HTTPS ni SSH), et il ne peut pas supprimer les fichiers verrou `.git/*.lock` du dossier monté (`Operation not permitted`). Résultat avec git local : le commit/push échoue et laisse un verrou qui bloque le run suivant. Le connecteur, lui, écrit via l'API GitHub avec sa propre connexion authentifiée — ni réseau sandbox ni git local requis, donc aucun verrou possible.

- **Dépôt** : `AissatouGassama06/roadmap-bas`, branche `main`.
- **Pré-requis** : le connecteur GitHub doit être branché et avoir les droits d'écriture (push) sur ce dépôt.

Procédure :

1. Modifier les 2 HTML en local (Read/Write/Edit — fonctionne normalement sur le dossier monté).
2. Publier chaque fichier modifié via le connecteur GitHub :
   - récupérer le `sha` actuel du fichier sur `main` (outil de lecture de fichier du connecteur) ;
   - puis `create_or_update_file` avec le **contenu complet** du fichier local, le `sha`, branche `main`, message `MAJ roadmap <date>` ;
   - ou `push_files` pour committer les 2 fichiers en un seul commit.
3. GitHub Pages se régénère tout seul (1–2 min).
4. Toujours pousser le **contenu intégral** du fichier généré localement (pas une reconstruction de mémoire), pour ne pas corrompre le HTML publié.

### Fallback si le connecteur GitHub est indisponible
Enregistrer les modifs dans les fichiers et prévenir Aïssatou qu'un push manuel est nécessaire depuis son Mac :
```bash
cd "/Users/aissatougassama2/Roadmaps-Projets/roadmap-bas"
rm -f .git/HEAD.lock .git/index.lock
git add -A && git commit -m "MAJ roadmap $(date +%Y-%m-%d)" && git push
```
⚠ Ne pas tenter de faire le `git push` depuis le bac à sable (réseau bloqué + verrou non supprimable). Le remote contient un token/clé d'accès — ne jamais l'afficher ni le logger.

## Google Drive (copie « source »)
La tâche d'origine prévoyait de sauvegarder aussi les 2 HTML dans `My Drive/Work OS/`. En pratique ces fichiers HTML ne sont pas accessibles à Claude (chemin CloudStorage non monté, connecteur Drive ne les indexe pas). La version qui fait foi est donc **celle du dépôt Git / GitHub Pages**. Si une copie Drive doit rester synchro, Aïssatou la met à jour manuellement. Signaler ce point, ne pas inventer un succès de sauvegarde Drive.

## Correspondance briefs ↔ items Monday (réf. au 2026-06-08)

| # | Brief | Item Monday | Statut |
|---|-------|-------------|--------|
| 1 | Business Stage | 11600617393 | done |
| 2 | Scoring MEDDICC – Accounts | 11600615487 | done |
| 3 | CS Portfolio Plan | 11687650692 | done |
| 4 | Sales Layout Lot 2/3 | 11688627113 | done |
| 5 | Buyer Intents | 11720566517 | done |
| 6 | Mailer Global Strategy | 11720550092 | done |
| 7 | Verbal Call x Forecast | 11720578779 | done |
| 8 | Healthscore | 11720572677 | done |
| 9 | Correction Booking Value | 11720556096 | done |
| 10 | Targeted Contact | 11720566400 | done |
| 11 | Churn & Retention Forecast | 11720587698 | done (22/05) |
| 12 | Product Feedback | 12153122884 | done (03/06) |
| 13 | Auto New Logo & Expansion Booking Values | 11720976586 | wip (Unit Tests) |
| 14 | AVG Monthly Visit Aggregate | 11720976079 | done (25/05) |
| 15 | Approval Process | 12153029571 | done (08/06) |
| 16 | New Stage New Contract & Upsell | 11853552720 | todo |
| 17 | Scoring MEDDICC – Opportunities | 11853552614 | todo |
| 18 | Customer Trial & Onboarding – Implémentation | 11854111150 | todo |
| 19 | SOD & Touch Level | 11721023739 | wip (démarré 08/06) |

> Cette table est une photo à un instant T : revérifier les statuts sur Monday à chaque passage, ne pas la recopier aveuglément.

## Résumé de la procédure (checklist)

1. Lire les notes Drive (fraîcheur < 7 j).
2. Lire les statuts du board Monday 4907730144 (gérer les doublons).
3. Lire les 2 HTML.
4. Mettre à jour : date header + `TODAY` + légende, statuts `P` et `briefs`, cartes de stats, barres de progression.
5. Garder les 2 fichiers synchro (externe = sans onglet OKR).
6. Vérifier la cohérence des compteurs (recompter les `wip`).
7. `git add` + `commit` + `push` (gérer le verrou si besoin).
8. Signaler tout point bloqué (Drive, push) avec ⚠ — ne rien inventer.
