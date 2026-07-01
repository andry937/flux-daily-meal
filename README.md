# Diet RSS Feed — DODA

Génère automatiquement un flux RSS quotidien à partir de ta diet (fichier
Google Drive "Coaching DODA"), publié gratuitement via GitHub Pages.

Chaque matin, une GitHub Action télécharge le fichier, extrait la diet du
jour et met à jour `feed.xml`. Ton lecteur RSS (Feedly, Reeder, NetNewsWire,
etc.) va récupérer automatiquement la nouvelle entrée — sans aucune
intervention de ta part.

---

## 1. Rendre le fichier Google Drive accessible en lecture publique

Le script télécharge le fichier via un lien direct, donc il doit être
partagé en **"Toute personne disposant du lien peut consulter"** :

1. Ouvre le fichier "Coaching DODA" dans Google Drive.
2. Clic droit → **Partager** → change l'accès en **"Tous les utilisateurs
   disposant du lien"** avec le rôle **Lecteur**.
3. Le lien reste "secret" tant que personne ne connaît le File ID — mais
   garde en tête que ce n'est pas un accès protégé par mot de passe.

> Si tu préfères ne pas rendre le fichier public, il faudrait passer par
> l'API Google Drive avec un compte de service (OAuth), plus complexe à
> mettre en place — dis-moi si tu veux cette variante.

## 2. Créer le repo GitHub

1. Crée un nouveau repo sur GitHub (public ou privé — mais GitHub Pages
   gratuit nécessite un repo **public**, sauf si tu as GitHub Pro/Team).
2. Pousse tout le contenu de ce dossier dans le repo :

```bash
cd diet-rss-feed
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<ton-user>/<ton-repo>.git
git push -u origin main
```

## 3. Ajouter le secret DRIVE_FILE_ID

1. Dans le repo GitHub → **Settings** → **Secrets and variables** → **Actions**.
2. Clique **New repository secret**.
3. Nom : `DRIVE_FILE_ID`
   Valeur : `1nGm7eU-hB_qvJKOUhZK4reLliot20HIyYDxMwIMPU8I`
4. Sauvegarde.

## 4. Activer GitHub Pages

1. **Settings** → **Pages**.
2. Source : **Deploy from a branch**.
3. Branche : `main`, dossier `/ (root)`.
4. Sauvegarde. L'URL de ton flux sera :
   `https://<ton-user>.github.io/<ton-repo>/feed.xml`

## 5. Ajuster FEED_LINK (optionnel mais recommandé)

Le workflow calcule automatiquement l'URL à partir du nom du repo. Si tu
préfères la fixer en dur, édite `FEED_LINK` dans
`.github/workflows/daily.yml`.

## 6. Premier lancement manuel

Pas besoin d'attendre le lendemain matin :

1. Onglet **Actions** du repo → sélectionne le workflow **"Génère le flux
   RSS de la diet"**.
2. Clique **Run workflow** → **Run workflow**.
3. Une fois terminé, va vérifier `feed.xml` à la racine du repo, ou visite
   directement l'URL GitHub Pages.

## 7. Abonner ton lecteur RSS

Ajoute l'URL `https://<ton-user>.github.io/<ton-repo>/feed.xml` dans
Feedly, Reeder, Inoreader, NetNewsWire, ou n'importe quel lecteur RSS. Une
nouvelle entrée apparaîtra chaque matin après l'exécution automatique
(05:30 UTC par défaut, ajustable dans le fichier `daily.yml`, ligne `cron`).

---

## Notes

- Le flux garde un historique glissant des **14 derniers jours**
  (modifiable via `MAX_ITEMS` dans `generate_feed.py`).
- Si la date du jour n'est pas trouvée dans la feuille (fichier pas encore
  mis à jour), le flux affichera un message au lieu de planter.
- Le fuseau horaire par défaut est `Europe/Brussels` — à changer via la
  variable d'env `TIMEZONE` dans le workflow si besoin.
- Le fichier étant public via le lien, évite d'y mettre des informations
  que tu ne veux pas exposer publiquement (même si l'URL n'est pas
  indexée par les moteurs de recherche).
