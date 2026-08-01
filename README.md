# Constructions Michel Labbé — site Web

Refonte du site de [Constructions Michel Labbé](https://www.cmlabbe.com), entrepreneur général
en construction commerciale, industrielle et institutionnelle établi à L'Assomption (Québec).

Site statique en **HTML, CSS et JavaScript natif**. Aucun cadriciel, aucune dépendance à
installer, aucune étape de compilation obligatoire, aucune clé ni secret dans le dépôt.

---

## Démarrage

Le plus simple : ouvrir `index.html` dans un navigateur. Tout fonctionne, y compris la
navigation entre les pages.

Pour reproduire les conditions d'un vrai serveur (chemins absolus, page 404) :

```bash
python3 -m http.server 8000
# puis http://localhost:8000
```

---

## Structure

```
index.html                     Accueil
champs-d-activites.html        Index filtrable des 28 spécialités
formules-de-realisation.html   Clé en main, gérance, à l'heure, à forfait + les 5 étapes
entreprise.html                Approche, service à la clientèle, gestion de chantier
clients-et-partenaires.html    43 organisations classées par secteur
documents-de-soumission.html   Sous-traitants et fournisseurs (noindex)
nous-joindre.html              Formulaire de soumission et coordonnées
404.html                       Page d'erreur

robots.txt · sitemap.xml · .nojekyll

assets/css/site.css            Système de design complet (tokens, composants, responsive)
assets/js/site.js              Navigation mobile, filtre, validation de formulaire, animations
assets/js/projets.js           Données des fiches de projets (vide — voir LISEZ-MOI)

_source/build.py               Données du site + gabarits partagés
_source/pages.py               Contenu des pages
```

Les pages HTML du dépôt sont **le livrable** : elles sont versionnées et se déploient telles
quelles. Le dossier `_source/` sert uniquement à les régénérer sans recopier l'en-tête et le
pied de page dans huit fichiers :

```bash
python3 _source/pages.py
```

---

## Déploiement

### GitHub Pages

Réglages → Pages → *Deploy from a branch* → `main` / `/ (root)`. Le fichier `.nojekyll` à la
racine désactive le traitement Jekyll, ce qui garantit que les fichiers sont servis tels
quels. La page `404.html` est reprise automatiquement.

### Hébergement classique

Copier tout le contenu du dépôt sauf `_source/` à la racine du serveur. Sur Apache, indiquer
la page d'erreur :

```apache
ErrorDocument 404 /404.html
```

---

## À configurer avant une mise en ligne réelle

Trois points, détaillés dans **[LISEZ-MOI.md](LISEZ-MOI.md)** :

1. **Les formulaires** — renseigner `CONFIG.endpointFormulaire` dans `assets/js/site.js`. Tant
   que le champ est vide, les formulaires ouvrent le logiciel de courriel avec le message déjà
   rédigé : aucun bouton ne fait semblant de fonctionner.
2. **Les images** — le logo, la photo d'en-tête et les logos de clients sont pour l'instant
   chargés depuis `www.cmlabbe.com`. Il faut les copier dans `assets/img/` et mettre à jour les
   adresses dans `_source/build.py`. Si une image manque, le site ne casse pas : un repli
   textuel prend le relais.
3. **Les fiches de projets** — `assets/js/projets.js` est volontairement vide. Aucun projet
   fictif n'a été inventé ; la section « Réalisations » apparaît dès qu'une vraie fiche est
   ajoutée.

Le LISEZ-MOI contient aussi la liste des informations à confirmer avec l'entreprise (numéro de
licence RBQ, année de fondation, heures d'ouverture, certifications) et celle des médias
manquants.

---

## Accessibilité et performance

Cible WCAG 2.2 niveau AA : HTML sémantique, lien d'évitement, navigation clavier complète,
focus visible, messages d'erreur reliés à leur champ, zones tactiles d'au moins 44 px,
`prefers-reduced-motion` respecté. Les contrastes ont été calculés sur l'ensemble des textes
des huit pages.

Aucune bibliothèque JavaScript. Les animations n'utilisent que `transform` et `opacity`, donc
elles sont composées par le GPU sans recalcul de mise en page.

Testé de 320 px à 2200 px de large : aucun débordement horizontal, aucune erreur console.

---

## Droits

Le nom, le logo et les contenus de Constructions Michel Labbé appartiennent à l'entreprise.
Les logos des clients et partenaires affichés sur le site sont des marques appartenant à leurs
propriétaires respectifs, reproduites à titre de référence commerciale.

Si le dépôt est rendu public, garder en tête que ces logos y seront visibles et que les images
pointent pour l'instant vers le serveur de `cmlabbe.com`.
