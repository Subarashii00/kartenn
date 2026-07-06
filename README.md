# Kartenn — carte interactive de Saint-Brieuc

Petit site statique : thèmes flottants → liste de lieux → carte (Leaflet + OpenStreetMap)
habillée façon carte marine bretonne. 100 % gratuit à héberger.

## Structure du repo

```
kartenn/
├── index.html        ← toute l'application (HTML + CSS + JS)
├── data/
│   └── lieux.json    ← LA liste des lieux (c'est le seul fichier à modifier au quotidien)
├── .pages.yml        ← configuration de l'interface admin (Pages CMS)
├── .nojekyll         ← dit à GitHub Pages de servir les fichiers tels quels
└── README.md         ← ce guide
```

---

# Guide de déploiement pas-à-pas (from scratch, Linux)

## Étape 0 — Installer git et créer un compte GitHub

Dans un terminal :

```bash
sudo apt update && sudo apt install git        # Debian/Ubuntu/Mint
# ou : sudo dnf install git                    # Fedora
# ou : sudo pacman -S git                      # Arch
```

Puis dis à git qui tu es (une seule fois, pour toujours) :

```bash
git config --global user.name "TonPseudo"
git config --global user.email "ton@email.fr"
```

Crée un compte gratuit sur https://github.com/signup si tu n'en as pas.

## Étape 1 — Créer le dépôt (repo) sur GitHub

1. Va sur https://github.com/new
2. « Repository name » : `kartenn` (ou ce que tu veux)
3. Laisse en **Public** (obligatoire pour GitHub Pages gratuit)
4. Ne coche RIEN d'autre (pas de README, pas de .gitignore)
5. Clique **Create repository**

Laisse la page ouverte, on en aura besoin.

## Étape 2 — Créer une clé SSH (pour pousser sans mot de passe)

GitHub n'accepte plus les mots de passe en ligne de commande. La méthode propre :

```bash
ssh-keygen -t ed25519 -C "ton@email.fr"
# Appuie sur Entrée à toutes les questions (chemin par défaut, pas de passphrase si tu veux simple)
cat ~/.ssh/id_ed25519.pub
```

Copie toute la ligne affichée (elle commence par `ssh-ed25519`), puis :

1. GitHub → clique sur ton avatar en haut à droite → **Settings**
2. Menu de gauche → **SSH and GPG keys** → **New SSH key**
3. Title : `mon-pc`, colle la clé dans « Key » → **Add SSH key**

Vérifie que ça marche :

```bash
ssh -T git@github.com
# Réponds "yes" à la question, tu dois voir : "Hi TonPseudo! You've successfully authenticated..."
```

## Étape 3 — Envoyer les fichiers du site

Place-toi dans le dossier `kartenn/` (celui qui contient `index.html`), puis :

```bash
cd chemin/vers/kartenn
git init
git add .
git commit -m "Première version de Kartenn"
git branch -M main
git remote add origin git@github.com:TonPseudo/kartenn.git   # ← remplace TonPseudo !
git push -u origin main
```

Rafraîchis la page GitHub du repo : tes fichiers sont là.

> Astuce : les fichiers `.pages.yml` et `.nojekyll` commencent par un point,
> ils sont donc « cachés ». `ls -a` permet de vérifier qu'ils sont bien dans le dossier.

## Étape 4 — Activer GitHub Pages (l'hébergement gratuit)

1. Sur la page GitHub du repo → onglet **Settings** (roue crantée)
2. Menu de gauche → **Pages**
3. Section « Build and deployment » → Source : **Deploy from a branch**
4. Branch : **main**, dossier : **/ (root)** → **Save**
5. Attends 1 à 2 minutes, recharge la page : une bannière affiche l'adresse du site,
   du type **https://tonpseudo.github.io/kartenn/**

C'est en ligne, en HTTPS (indispensable pour que la géolocalisation fonctionne). 🎉

## Étape 5 — Brancher l'admin (Pages CMS, gratuit)

Le fichier `.pages.yml` du repo décrit déjà le formulaire d'édition. Il reste à connecter :

1. Va sur https://app.pagescms.org
2. **Sign in with GitHub**
3. GitHub demande quelles autorisations donner : choisis **Only select repositories**
   et sélectionne `kartenn` (plus prudent que « All repositories »)
4. De retour sur Pages CMS, ouvre le projet `kartenn`
5. Dans le menu : **Lieux de la carte** → tu as une interface avec la liste complète :
   ajouter, modifier, supprimer, changer l'emoji, les coordonnées...
6. Clique **Save** : Pages CMS fait un commit dans ton repo, et GitHub Pages
   republie le site automatiquement (compte ~1 minute)

Bonus : Pages CMS permet d'inviter d'autres personnes par simple e-mail
(elles n'ont même pas besoin d'un compte GitHub) → menu Settings du projet → Collaborators.

## Étape 6 — Trouver les coordonnées d'un lieu

1. Va sur https://www.openstreetmap.org
2. Cherche ou zoome sur le lieu
3. **Clic droit** dessus → « Afficher l'adresse »
4. La latitude et la longitude apparaissent dans le panneau / la barre d'adresse
   (ex : `48.5139, -2.7647`) → copie-les dans le formulaire admin

## Mettre à jour le code (pas les lieux) plus tard

```bash
cd chemin/vers/kartenn
# ... tu modifies index.html ...
git add .
git commit -m "Description de la modif"
git push
```

Le site se met à jour tout seul 1-2 minutes après le push.

---

## Ce que ça coûte

| Brique | Service | Prix |
|---|---|---|
| Hébergement + HTTPS | GitHub Pages | 0 € |
| Fond de carte | OpenStreetMap (attribution obligatoire, déjà en place) | 0 € |
| Admin des lieux | Pages CMS (open source, MIT) | 0 € |
| Nom de domaine perso (optionnel) | ~10 €/an si tu en veux un ; sinon l'URL github.io gratuite | 0 € |

## Limites à connaître

- Le repo est public : tout le monde peut LIRE les fichiers (pas les modifier).
- Les tuiles OSM gratuites sont pour un usage raisonnable — largement suffisant ici.
- La géolocalisation demande l'autorisation du navigateur et ne marche qu'en HTTPS
  (c'est le cas sur github.io) ou en local via un petit serveur :
  `python3 -m http.server` puis http://localhost:8000
