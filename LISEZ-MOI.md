# Constructions Michel Labbé — site Web

Site statique en HTML, CSS et JavaScript natif. Aucune dépendance, aucun processus de
compilation obligatoire, aucune clé ni secret dans le code.

---

## 1. Mise en ligne

Copiez le contenu du dossier (sauf `_source/`) à la racine de l'hébergement. C'est tout.

Configurez la page d'erreur du serveur vers `404.html` :

```apache
# .htaccess (Apache)
ErrorDocument 404 /404.html
```

---

## 2. Ce qui doit être branché avant la mise en ligne

### a) Les deux formulaires

Ouvrez `assets/js/site.js` et renseignez `CONFIG.endpointFormulaire` avec l'adresse du script
qui reçoit les envois (ex. `/envoi.php`).

**Tant que ce champ est vide**, les formulaires restent fonctionnels : après validation, ils
ouvrent le logiciel de courriel de la personne avec le message déjà rédigé, adressé à
`cml@cmlabbe.com`. Aucun bouton ne fait semblant de fonctionner.

Champs envoyés — formulaire de contact : `sujet`, `nom`, `organisation`, `courriel`,
`telephone`, `message`. Formulaire d'accès aux soumissions : `sujet`, `entreprise`, `nom`,
`courriel`, `telephone`, `specialites`.

Le champ `site` est un piège anti-pourriel : s'il est rempli, l'envoi doit être ignoré côté
serveur également.

### b) Les images

Les fichiers actuellement affichés (logo, photo d'en-tête, logos de clients) sont chargés
depuis `www.cmlabbe.com`. **Copiez-les dans `assets/img/` et remplacez les adresses** dans
`_source/build.py` (constantes `LOGO_ENTETE`, `LOGO_PIED`, `PHOTO_HEROS`, fonction `L()`),
puis relancez `python3 _source/pages.py`.

Si une image manque, le site ne casse pas : le logo est remplacé par le nom écrit en toutes
lettres, et l'emplacement photo par un aplat technique.

### c) Les logos de clients animés

Deux composants distincts, tous deux alimentés par la liste `SECTEURS` de `_source/build.py` :

- **Accueil** — un mur de 21 tuiles (les 3 premières organisations de chaque secteur) qui
  pivotent sur elles-mêmes en vague, à la manière d'un panneau à lames. Face avant : logo en
  gris. Face arrière : logo en couleur. Au survol ou au focus clavier, la vague se fige et la
  face visible passe en couleur ; le clic ouvre le site de l'organisation.
- **Page Clients** — un bandeau rectangulaire par secteur, à défilement continu de droite à
  gauche. Le défilement s'arrête au survol et au focus ; la couleur revient et le clic ouvre
  le site.

Réglages des bandeaux, en haut de `_source/build.py`, puis relancer `python3 _source/pages.py` :

```python
LARGEUR_TUILE   = 152   # px, largeur d'une case du bandeau
VITESSE_BANDEAU = 34    # px par seconde
```

La durée de chaque bandeau est calculée à partir de ces deux valeurs, ce qui garantit une
vitesse identique partout, quel que soit le nombre de logos. Le rythme de la vague du mur de
tuiles se règle dans l'animation `bascule` du CSS (cycle de 16 s, tuiles à plat 92 % du temps)
et dans le décalage calculé par `mur_logos()`.

**Une seule source de rotation par tuile.** Le survol ne fait volontairement *pas* tourner la
tuile une seconde fois : les rotations d'éléments imbriqués s'additionnent, et la face affichée
deviendrait imprévisible selon la position de la vague. Le survol se contente de figer la vague
(`animation-play-state: paused`) et de colorer la face visible. Ne pas réintroduire de
`transform: rotateY()` sur `.tuile__pivot`.

**Aucun bouton de pause.** Les animations tournent en continu. Le critère WCAG 2.2.2 demande un
moyen d'arrêter tout mouvement automatique de plus de cinq secondes ; deux moyens subsistent
— pointer une tuile ou un bandeau à la souris, ou y entrer au clavier — et le réglage système
`prefers-reduced-motion` rend les deux composants entièrement fixes. Si un audit formel est un
jour exigé, remettre un bouton explicite est la façon la plus sûre de lever la question.

Une seule occurrence de chaque organisation est annoncée aux lecteurs d'écran et atteignable
au clavier ; les répétitions qui remplissent le bandeau sont inertes (`aria-hidden`,
`tabindex="-1"`).

### d) Le portail de documents de soumission

`documents-de-soumission.html` explique la démarche et recueille les demandes d'accès. Si un
véritable portail existe, ajoutez-y un bouton pointant vers son adresse. La page est en
`noindex` et exclue du `robots.txt`, ce qui est voulu.

---

## 3. Ajouter des fiches de projets

Le site contient un emplacement prêt pour des réalisations, **volontairement vide** : aucun
projet fictif n'a été inventé.

Ouvrez `assets/js/projets.js` :

```js
window.CML_PROJETS = [
  {
    titre: "Nouvelle dalle structurale sur pieux",
    client: "Nom du client",
    lieu: "L'Assomption",
    annee: "2025",
    description: "Deux phrases sur la portée des travaux et la contrainte principale."
  }
];
```

Dès qu'une fiche est ajoutée, la section « Réalisations » apparaît automatiquement sur la page
d'accueil et sur la page Clients. Tant que le tableau est vide, elle reste masquée.

---

## 4. Modifier le contenu

Deux façons de faire.

**Ponctuel** : modifiez directement le fichier `.html` concerné.

**Structurel** (menu, coordonnées, liste des spécialités, clients, formules) : modifiez
`_source/build.py`, puis relancez la génération. Cela évite de reprendre l'en-tête et le pied
de page dans huit fichiers.

```bash
python3 _source/pages.py
```

Le dossier `_source/` n'a pas besoin d'être mis en ligne.

---

## 5. Ce qui reste à confirmer avec l'entreprise

Aucune de ces informations n'a été inventée : elles sont simplement absentes des sources.

| À confirmer | Où cela changerait quelque chose |
|---|---|
| Numéro de licence RBQ | Pied de page et page L'entreprise (élément de confiance fort dans le secteur) |
| Année exacte de fondation | Remplacerait « plus de 40 ans » par une date précise |
| Territoire officiellement desservi | Actuellement déduit de la liste des clients (Lanaudière, Grand Montréal, Laurentides) |
| Heures d'ouverture | Volontairement absentes du site plutôt qu'inventées ; à ajouter aux coordonnées et au balisage `LocalBusiness` |
| Certifications (ASP Construction, mutuelle de prévention, ISO, etc.) | Seule la certification amiante est mentionnée, car elle figure sur le site actuel |
| Nombre d'employés | Repère factuel potentiel sur la page d'accueil |
| Contacts par service (estimation, chargés de projets) | Page Nous joindre |
| Adresse exacte | Le site actuel écrit tantôt « 940 route 341 », tantôt « 940, Montée de l'Épiphanie ». Les deux ont été fusionnés ; à valider |
| Comptes de réseaux sociaux | Ajouterait `sameAs` aux données structurées et des liens au pied de page |
| Version anglaise | Le site est unilingue français ; le balisage `lang` et `hreflang` est prêt à recevoir une seconde langue |

---

## 6. Médias manquants

Le site est conçu pour bien paraître sans photo, mais gagnerait beaucoup avec :

- 6 à 10 photos de chantiers réels (fondations, structure, travaux en usine, aménagement) ;
- 1 photo d'atelier de métaux ouvrés — c'est un différenciateur réel et rien ne l'illustre ;
- 1 photo d'équipe ou de chargés de projets ;
- des fiches de projets (client, lieu, année, portée) ;
- des témoignages écrits, avec autorisation nommée.

Traitement photographique à respecter : noir et blanc contrasté, ou couleur désaturée, recadrage
large. Le système est prévu pour cela.

---

## 7. Accessibilité et performance

- Cible WCAG 2.2 AA : lien d'évitement, focus visible, navigation clavier complète, messages
  d'erreur reliés aux champs, zones tactiles d'au moins 44 px, contrastes vérifiés.
- `prefers-reduced-motion` est respecté : toutes les animations sont désactivées.
- Aucune bibliothèque JavaScript. Le fichier `site.js` fait environ 14 ko.
- Les animations de logos utilisent uniquement `transform` et `opacity` : elles sont
  composées par le GPU et ne provoquent aucun recalcul de mise en page.
- Les images des bandeaux ne sont volontairement pas en `loading="lazy"` : un élément déplacé
  par `transform` ne déclenche pas le chargement différé. Les 43 URL sont uniques, les
  répétitions viennent du cache.
- Les polices sont chargées avec `display=swap` et `preconnect`.
- Pensez à optimiser les images une fois copiées localement (WebP, largeur maximale 1900 px).

---

## 8. Structure

```
index.html                       Accueil
champs-d-activites.html          Index filtrable des 28 spécialités
formules-de-realisation.html     Clé en main, gérance, à l'heure, à forfait + 5 étapes
entreprise.html                  Approche, service, gestion de chantier, coordonnées
clients-et-partenaires.html      43 organisations classées par secteur
documents-de-soumission.html     Sous-traitants et fournisseurs (noindex)
nous-joindre.html                Formulaire de soumission et coordonnées
404.html
robots.txt · sitemap.xml
assets/css/site.css              Système de design complet
assets/js/site.js                Comportements
assets/js/projets.js             Données des réalisations (vide)
_source/                         Générateur (ne pas mettre en ligne)
```
