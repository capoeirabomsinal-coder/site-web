# Site de Capoeira Bom Sinal — mode d'emploi

Quelques fichiers statiques, aucune base de données, aucun PHP, aucune mise à jour de sécurité à faire.

```
index.html      accueil : horaires, lieux, professeur, tarifs, contact
capoeira.html   histoire de la capoeira
lignee.html     le Grupo Capoeira Brasil et ses trois mestres fondateurs
style.css       mise en page commune aux trois pages
images/         photos (servies depuis le site, rien n'est chargé ailleurs)
_headers        en-têtes de sécurité HTTP (lu par Cloudflare Pages et Netlify)
README.md       ce fichier
```

Chaque modification poussée sur la branche `main` du dépôt GitHub est mise en ligne automatiquement par Cloudflare Pages en une minute environ.

## Modifier le contenu

**Accueil** : ouvrir `index.html`, descendre jusqu'au commentaire `CONTENU DU SITE`, éditer l'objet `ASSO`.

**Pages « La capoeira » et « Lignée »** : ce sont des pages HTML ordinaires, on modifie directement le texte entre les balises `<p>…</p>`.
Tout ce qui contient `À COMPLÉTER` s'affiche surligné en jaune sur le site tant que ce n'est pas rempli — c'est volontaire, ça évite de mettre en ligne un gabarit à moitié rempli sans s'en apercevoir.

Pour ajouter un créneau :

```js
{ jour:"Mercredi", seances:[
  { debut:"19:30", fin:"21:00", public:"adultes", intitule:"Tous niveaux", lieu:"prouff", salle:"4e étage" }
]},
```

`public` vaut `"enfants"` (barre jaune, enfants et ados) ou `"adultes"` (barre bleue). `lieu` reprend une clé définie plus haut dans `lieux`. `salle` est facultatif. Les jours sans cours ne sont pas affichés : il suffit de ne pas les mettre dans la liste.

Les photos vont dans `images/`, au format JPEG, 1600 px de large au maximum.

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
Pour le domaine nu (sans `www`), Cloudflare Pages exige que Cloudflare gère les DNS du domaine : chez IONOS, remplacer les serveurs de noms par ceux indiqués par Cloudflare (c'est fait pour `capoeirarennes.fr`). Le domaine reste acheté et renouvelé chez IONOS. Cloudflare crée ensuite tout seul les enregistrements et le certificat.

Les autres domaines de l'asso (`capoeirabomsinal.fr`, `capoeirabrasil-rennes.com`, `capoeirabrasil-rennes.fr`) sont ajoutés dans Cloudflare avec une règle de redirection 301 vers `https://capoeirarennes.fr`.

**Ne pas supprimer les enregistrements MX** s'il existe des adresses `@capoeirarennes.fr`, sinon la messagerie tombe avec.

### Autres options équivalentes

- **Codeberg Pages** — associatif, européen, sans compte commercial. Pousser sur une branche `pages`.
- **Netlify** — même principe, quota gratuit de 100 Go/mois.
- **GitHub Pages** — gratuit aussi, mais ignore le fichier `_headers` : pas d'en-têtes de sécurité personnalisés.

## Deux points à arbitrer avant la mise en ligne

**Les polices Google.** Le fichier charge deux polices depuis `fonts.googleapis.com`, ce qui transmet l'adresse IP de chaque visiteur à Google. Pour une association qui n'a aucune raison de le faire, deux sorties : supprimer les trois balises `<link ... fonts.g...>` dans l'en-tête (les polices système prennent le relais, le site reste correct), ou télécharger les fichiers `.woff2` et les servir depuis le dépôt.

**Les photos d'enfants.** La photo de la section « Le professeur » montre un enfant reconnaissable. Elle était déjà publiée sur l'ancien site, mais vérifier que l'autorisation parentale de droit à l'image est toujours valable, sinon la remplacer.

**Le script inline.** La politique de sécurité du fichier `_headers` autorise `script-src 'unsafe-inline'` parce que le JavaScript est dans la page. Pour s'en passer : déplacer le bloc `<script>` dans un `site.js`, remplacer par `<script src="/site.js"></script>` et mettre `script-src 'self'`.

## Ce qu'il reste à faire côté association

- [ ] Vérifier que le titulaire du domaine chez IONOS est bien l'association, pas une personne physique
- [ ] Activer le renouvellement automatique du domaine, moyen de paiement sur le compte de l'asso
- [ ] Créer une adresse collective (`bureau@`) redirigée vers plusieurs membres du bureau
- [ ] Déposer les identifiants IONOS, Cloudflare et Git dans un coffre partagé à deux personnes minimum
- [ ] Compléter les mentions légales : siège social, directeur de la publication
- [ ] Confirmer les tarifs de la saison et le numéro de la place Paul Ricœur (1 ou 5)
- [x] Récupérer le contenu de l'ancien WordPress (repris depuis web.archive.org, archive de février 2024)
