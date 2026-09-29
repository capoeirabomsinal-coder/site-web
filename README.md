# Site de Capoeira Bom Sinal — mode d'emploi

Trois fichiers, aucune base de données, aucun PHP, aucune mise à jour de sécurité à faire.

```
index.html   tout le site (contenu + mise en page)
_headers     en-têtes de sécurité HTTP (lu par Cloudflare Pages et Netlify)
README.md    ce fichier
```

## Modifier le contenu

Ouvrir `index.html`, descendre jusqu'au commentaire `CONTENU DU SITE`, éditer l'objet `ASSO`.
Tout ce qui contient `À COMPLÉTER` s'affiche surligné en jaune sur le site tant que ce n'est pas rempli — c'est volontaire, ça évite de mettre en ligne un gabarit à moitié rempli sans s'en apercevoir.

Pour ajouter un créneau :

```js
{ jour:"Mercredi", seances:[
  { debut:"19:30", fin:"21:00", public:"adultes", intitule:"Tous niveaux", lieu:"prouff" }
]},
```

`public` vaut `"enfants"` (barre jaune) ou `"adultes"` (barre bleue). `lieu` reprend une clé définie plus haut dans `lieux`.

Vérifier le rendu avant publication : ouvrir le fichier directement dans un navigateur, ou `python3 -m http.server 8000` dans le dossier.

## Mettre en ligne

### Cloudflare Pages (recommandé)

Gratuit sans limite de trafic, certificat TLS automatique, en-têtes du fichier `_headers` pris en compte.

1. Créer un dépôt Git sur GitHub ou Codeberg avec **le compte de l'association**, pas un compte personnel.
2. Y pousser les trois fichiers.
3. Sur `dash.cloudflare.com` : Workers & Pages → Create → Pages → Connect to Git → choisir le dépôt.
4. Build command : laisser vide. Output directory : `/`. Deploy.
5. Le site est en ligne sur `<projet>.pages.dev` en une minute.

Sans Git, l'onglet « Upload assets » accepte un glisser-déposer du dossier. Plus simple, mais plus personne n'a l'historique des versions.

### Brancher le domaine

Dans Cloudflare Pages : Custom domains → `capoeirarennes.fr` puis `www.capoeirarennes.fr`.
Cloudflare affiche les enregistrements à créer. Chez IONOS, dans la zone DNS du domaine :

| Type  | Nom | Valeur |
|-------|-----|--------|
| CNAME | www | `<projet>.pages.dev` |
| A ou ALIAS | @ | valeur indiquée par Cloudflare |

Propagation : quelques minutes à deux heures. Le certificat se génère tout seul ensuite.

**Ne pas supprimer les enregistrements MX** s'il existe des adresses `@capoeirarennes.fr`, sinon la messagerie tombe avec.

### Autres options équivalentes

- **Codeberg Pages** — associatif, européen, sans compte commercial. Pousser sur une branche `pages`.
- **Netlify** — même principe, quota gratuit de 100 Go/mois.
- **GitHub Pages** — gratuit aussi, mais ignore le fichier `_headers` : pas d'en-têtes de sécurité personnalisés.

## Deux points à arbitrer avant la mise en ligne

**Les polices Google.** Le fichier charge deux polices depuis `fonts.googleapis.com`, ce qui transmet l'adresse IP de chaque visiteur à Google. Pour une association qui n'a aucune raison de le faire, deux sorties : supprimer les trois balises `<link ... fonts.g...>` dans l'en-tête (les polices système prennent le relais, le site reste correct), ou télécharger les fichiers `.woff2` et les servir depuis le dépôt.

**Le script inline.** La politique de sécurité du fichier `_headers` autorise `script-src 'unsafe-inline'` parce que le JavaScript est dans la page. Pour s'en passer : déplacer le bloc `<script>` dans un `site.js`, remplacer par `<script src="/site.js"></script>` et mettre `script-src 'self'`.

## Ce qu'il reste à faire côté association

- [ ] Vérifier que le titulaire du domaine chez IONOS est bien l'association, pas une personne physique
- [ ] Activer le renouvellement automatique du domaine, moyen de paiement sur le compte de l'asso
- [ ] Créer une adresse collective (`bureau@`) redirigée vers plusieurs membres du bureau
- [ ] Déposer les identifiants IONOS, Cloudflare et Git dans un coffre partagé à deux personnes minimum
- [ ] Compléter les mentions légales : siège social, directeur de la publication, hébergeur
- [ ] Récupérer le contenu de l'ancien WordPress avant purge par IONOS, s'il est encore restaurable
