# NUNI Backend — v0.3 (code d'accès envoyé par email)

Ce dossier contient ton backend `Nuni-backend`, avec une nouveauté :
**quand tu actives un abonnement, le code à 6 caractères est automatiquement
envoyé par email à `nunimisiki@gmail.com`**, et tu as maintenant une petite
page web pour le faire sans ligne de commande.

## Ce qui a changé par rapport à ta version

| Fichier | Changement |
|---|---|
| `mailer.js` | **Nouveau.** Envoie l'email avec le code d'accès. |
| `public/admin.html` | **Nouveau.** Page web pour activer un abonnement en quelques clics. |
| `server.js` | Ajout de la route `/api/admin/activate-by-email` + envoi d'email automatique lors de l'activation. `db.js` et `auth.js` sont **inchangés**. |
| `package.json` | Ajout de la dépendance `nodemailer`. |
| `.env.example` | Ajout de `EMAIL_USER` et `EMAIL_APP_PASSWORD`. |

Rien d'autre n'a été modifié : tes routes existantes (`/api/register`, `/api/login`,
`/api/subscribe/request`, `/api/subscribe/redeem`, `/api/tracks`, `/api/clips`)
fonctionnent exactement comme avant.

## Comment ça marche maintenant

1. Le client s'inscrit sur le site → choisit un Pass → est redirigé vers WhatsApp
2. Il te paie via Mobile Money sur WhatsApp
3. **Toi**, tu vas sur `https://ton-backend.up.railway.app/admin.html`
4. Tu entres : l'email du client, le Pass choisi, la durée, et ta clé admin
5. Tu cliques sur **"Activer et envoyer le code par email"**
6. Le code (ex : `7A4JH8`) s'affiche à l'écran **et** arrive dans la boîte mail `nunimisiki@gmail.com`
7. Tu copies ce code et tu le renvoies au client sur WhatsApp
8. Le client retourne sur le site, clique sur "Débloquer mon accès", entre le code → il a accès à tout NUNI

## 1. Configurer l'envoi d'email (obligatoire, à faire une seule fois)

Gmail exige un "mot de passe d'application" spécial (pas ton mot de passe normal) :

1. Va sur [myaccount.google.com/security](https://myaccount.google.com/security) avec le compte `nunimisiki@gmail.com`
2. Active la **validation en 2 étapes** si ce n'est pas déjà fait
3. Va sur [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
4. Crée un mot de passe d'application (nom libre, ex : "NUNI backend")
5. Google te donne un code à 16 caractères (ex : `abcd efgh ijkl mnop`) — **copie-le sans les espaces**

## 2. Tester en local

```bash
cd nuni-backend
npm install
cp .env.example .env
```

Ouvre le fichier `.env` et remplis :
```
JWT_SECRET=une_longue_chaine_aleatoire
ADMIN_KEY=une_autre_chaine_secrete_que_toi_seul_connait
EMAIL_USER=nunimisiki@gmail.com
EMAIL_APP_PASSWORD=le_code_16_caracteres_de_l_etape_1
```

Puis :
```bash
npm start
```

Ouvre `http://localhost:3000/admin.html` dans ton navigateur pour tester la page.

## 3. Déployer sur Railway (production)

1. Remplace les fichiers de ton dépôt GitHub `Nuni-backend` par ceux de ce dossier
   (voir section suivante pour la méthode "glisser-déposer")
2. Railway redéploiera automatiquement
3. Sur Railway, va dans l'onglet **Variables** de ton service et ajoute (si pas déjà présentes) :
   - `JWT_SECRET`
   - `ADMIN_KEY`
   - `EMAIL_USER` = `nunimisiki@gmail.com`
   - `EMAIL_APP_PASSWORD` = ton mot de passe d'application Gmail
4. Une fois redéployé, ta page admin sera accessible à :
   `https://nuni-backend-production-cf8e.up.railway.app/admin.html`

## 4. Comment remplacer les fichiers sur GitHub

Pour chaque fichier nouveau ou modifié (`server.js`, `package.json`, `.env.example`,
`mailer.js`, et le nouveau dossier `public/admin.html`) :

1. Va sur `https://github.com/NUNI-misikicg/Nuni-backend`
2. Clique sur **"Add file"** → **"Upload files"**
3. Glisse les fichiers modifiés (ils remplaceront automatiquement les anciens s'ils portent le même nom)
4. Pour le nouveau dossier `public/`, glisse tout le dossier ou crée-le en glissant `admin.html` — GitHub proposera de créer le chemin `public/admin.html`
5. Écris un message de commit, ex : "Ajout envoi email du code d'accès"
6. Clique **"Commit changes"**

Railway redéploiera automatiquement après ce commit (si le déploiement automatique
est activé, ce qui est le cas par défaut).

## Sécurité

- La clé `ADMIN_KEY` protège les routes d'activation — ne la partage avec personne
- La page `/admin.html` est publique en tant que page web, mais **inutilisable sans connaître ta clé ADMIN_KEY**
- Le mot de passe d'application Gmail n'est jamais exposé côté client, il reste uniquement dans les variables d'environnement du serveur
