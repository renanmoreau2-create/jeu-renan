# DERNIER BASTION — document de conception (V1)

Tower defense post-apocalyptique. Un homme seul tient un bastion central contre des vagues de zombies qui arrivent par plusieurs chemins. Trois arbres d'évolution : **les armes de l'homme**, **le bunker**, **les défenses posées sur et autour des chemins**.

Titre provisoire. Toutes les valeurs d'équilibrage sont regroupées dans l'objet `DATA` de `index.html`.

---

## 1. Boucle de jeu

1. **Préparation** (pas de chrono) : acheter ou améliorer des armes, faire évoluer le bunker, poser ou améliorer des défenses, réparer.
2. **Vague** : les zombies sortent des bords de la carte et suivent les chemins jusqu'au bunker. L'homme tire automatiquement sur la cible la plus avancée. Un appui sur un zombie force la cible.
3. **Récompense** : chaque zombie tué rapporte de l'argent, plus une prime de fin de vague.
4. **Escalade** : plus de zombies, plus résistants, plus variés, et de nouveaux chemins s'ouvrent.
5. **Défaite** : les points de vie du bunker tombent à zéro. Le record est conservé et la partie est sauvegardée à chaque début de préparation.

Choix de conception (référence : boucle classique du genre et principes de Defender's Quest) :
- **Pas de défilement** : toute la carte tient sur l'écran du téléphone.
- **Information totale** : l'ouverture des chemins est annoncée à l'avance, et les chemins encore fermés restent visibles en grisé.
- **Pas de spirale de défaite** : la prime de fin de vague est garantie, même si des zombies ont atteint le bunker.

## 2. Carte et chemins

Grille de 9 × 13 cases, bunker au centre.

| Chemin | Entrée | Longueur | Ouverture |
|---|---|---|---|
| Nord | bord haut | 14 cases, en S | vague 1 |
| Sud | bord bas | 14 cases, en S | vague 6 |
| Est | bord droit, en bas | 10 cases, flanc court | vague 13 |

- Les **pièges** (barbelés, mines, fossés) se posent **sur** le chemin.
- Les **tours** se posent **à côté** du chemin, sur n'importe quelle case libre.
- Le chemin Est est volontairement court : c'est une brèche de fin de partie qui oblige à réorganiser la défense.

## 3. L'homme et ses armes (11 armes, toutes portables par un fantassin)

L'ordre suit la puissance. Chaque arme se débloque après la précédente. L'homme ne porte qu'une arme active à la fois et en change d'un appui. Le choix compte : fusil à pompe contre les foules proches, Hécate contre les blindés, etc.

| # | Arme | Référence réelle | Rôle dans le jeu |
|---|---|---|---|
| 1 | Pistolet Glock 17 | Arme de poing réglementaire française (Gen 5 FR, livrée depuis fin 2020) | Arme de départ |
| 2 | PM HK MP5 | Pistolet-mitrailleur 9 mm des unités d'intervention | Cadence élevée, courte portée |
| 3 | Fusil à pompe Benelli M4 | Fusil de combat semi-automatique (M1014 aux États-Unis) | 6 plombs en éventail, dévastateur de près |
| 4 | Fusil d'assaut HK416F | Fusil d'assaut réglementaire, remplace le FAMAS | Polyvalent |
| 5 | FN SCAR-H PR | Fusil de précision semi-automatique 7,62 mm | Perforant (traverse 2 cibles) |
| 6 | FN Minimi | Mitrailleuse légère 5,56 mm | Arrosage continu |
| 7 | Lance-grenades M32 MGL | Revolver 40 mm à 6 coups | Dégâts de zone |
| 8 | PGM Hécate II | Fusil anti-matériel 12,7 mm de l'armée française | Ignore l'essentiel de l'armure, traverse 4 cibles |
| 9 | Lance-flammes M2 | Lance-flammes dorsal (portée efficace d'environ 20 m) | Cône de feu, brûlure, très courte portée |
| 10 | Carl Gustaf M4 | Arme multirôle sans recul de 84 mm | Gros dégâts de zone |
| 11 | RPO-A Shmel | Lance-roquettes thermobarique portable | Arme finale, souffle énorme et brûlure |

### Arbre d'évolution des armes

Chaque arme possédée a **3 branches de 5 niveaux**, comme demandé :
- **Cadence** : +18 % de tirs par seconde par niveau.
- **Puissance** : +25 % de dégâts par niveau.
- **Zone** : son effet dépend du type d'arme :
  - balles : +1 cible traversée ;
  - fusil à pompe : +1 plomb ;
  - explosifs : +15 % de rayon ;
  - lance-flammes : cône plus large et portée plus longue.

## 4. Le bunker (arbre d'évolution)

**Tronc : 5 niveaux de structure**, du plus léger au plus solide.

| Niv. | Structure | Effet |
|---|---|---|
| 1 | Sacs de sable | PV de base |
| 2 | Double muret de sacs | PV ×2 |
| 3 | Bastion HESCO (gabions remplis de terre) | PV ×4, armure 2 |
| 4 | Casemate béton | PV ×8, armure 4 |
| 5 | Bunker blindé | PV ×16, armure 7 |

L'armure réduit chaque coup reçu d'une valeur fixe.

**Branches** : modules montés sur le bunker, chacun avec 3 niveaux, débloqués selon le niveau de structure.

| Module | Requiert | Effet |
|---|---|---|
| Barbelé concertina en couronne | niv. 2 | Anneau qui ralentit et blesse au contact du bunker |
| Ceinture de Claymore | niv. 3 | Mine directionnelle (éventail de billes) qui se déclenche puis se réarme |
| Mitrailleuse 12,7 M2 | niv. 3 | Tourelle automatique |
| Lance-flammes fixe | niv. 4 | Cône de feu autour du bunker |
| Lance-roquettes automatique | niv. 5 | Salves de roquettes sur les groupes |

Le bunker peut aussi être réparé contre de l'argent pendant la préparation.

## 5. Défenses du chemin (toutes évolutives, 3 niveaux)

**Pièges, posés sur le chemin :**

| Défense | Niv. 1 | Niv. 2 | Niv. 3 |
|---|---|---|---|
| Barbelés | Concertina (ralentit, blesse) | Double rangée | Barbelé rasoir |
| Mine | Mine antipersonnel | Mine bondissante | Mine antichar (gros rayon) |
| Fossé | Fossé (ralentit fort) | Fossé à pieux (blesse) | Fossé incendiaire (brûle) |

Les mines se réarment après un délai.

**Tours, posées à côté du chemin :**

| Tour | Niv. 1 | Niv. 2 | Niv. 3 |
|---|---|---|---|
| Nid de mitrailleuse | MAG 58 | Double MAG | M2 Browning 12,7 |
| Tireur d'élite | FR-F2 | SCAR-H PR | Hécate II (perce l'armure) |
| Mortier | 60 mm | 81 mm | 120 mm |
| Lance-flammes | Léger | Lourd | Napalm |

Une défense revendue rembourse 60 % de ce qu'elle a coûté.

## 6. Bestiaire

Les types s'inspirent des grandes références du genre : les infectés spéciaux de Left 4 Dead, Dying Light et 7 Days to Die. Chacun a un rôle qui oblige à adapter la défense.

| Zombie | Apparition | Particularité | Contre efficace |
|---|---|---|---|
| Rôdeur | vague 1 | Lent, en nombre | Tout |
| Coureur | vague 3 | Rapide, fragile | Barbelés, fossés |
| Rampant | vague 5 | Passe sous les barbelés | Mines, lance-flammes |
| Bouffi | vague 7 | Explose : blesse les zombies voisins s'il est tué, inflige un gros coup s'il atteint le bunker | À abattre tôt, au milieu des foules |
| Blindé (ex-forces de l'ordre) | vague 8 | Armure élevée | Hécate, tireur d'élite, explosifs |
| Cracheur | vague 9 | S'arrête à distance et crache de l'acide sur le bunker | Portée longue, mortier |
| Hurleur | vague 11 | Accélère les zombies autour de lui | Priorité de tir (appui sur lui) |
| Brute | vague 12, puis toutes les 4 vagues | Énorme, encaisse beaucoup | Puissance concentrée |
| Abomination | boss toutes les 10 vagues | Boss, armure lourde | Toute la défense |

## 7. Vagues et économie

- Nombre de zombies : environ 10 à la vague 1, environ 50 à la vague 10, plus de 100 à la vague 20.
- Points de vie : multipliés par environ 1,13 par vague (courbe exponentielle, conforme aux retours des joueurs du genre).
- Intervalle d'apparition : de plus en plus court. Les zombies se répartissent entre les chemins ouverts.
- Récompense par zombie : augmente avec la vague. La prime de fin de vague est garantie.
- Vitesse de jeu ×1, ×2 ou ×3, et pause.

## 8. Écarts assumés entre réalisme et jeu

- **Lance-flammes M2** : retiré du service américain en 1978. Gardé parce qu'il est emblématique et que le jeu se passe après l'effondrement.
- **Mines antipersonnel** : interdites par la convention d'Ottawa. Dans la fiction, c'est l'apocalypse.
- **Minigun** : exclu, parce qu'il n'est pas portable à la main par un soldat. La demande était « toutes les armes qu'un soldat peut porter ».
- **RPO-A Shmel en arme finale** : c'est l'arme portable non guidée au souffle le plus puissant de la liste (charge thermobarique). Un missile guidé comme l'Akeron MP ou le Javelin aurait moins de sens contre des zombies.

## 9. Pistes V2

- Défenses qui s'usent : barbelés arrachés par les brutes, à réparer.
- Munitions et rechargement par arme.
- Autres cartes, avec des chemins différents.
- Graphismes en sprites et sons plus riches.
- Classement des meilleurs scores.

---

## Sources

- HK416F et FELIN : https://blog.roninsgrips.com/french-military-small-arms-modernization-overview/
- Glock 17 Gen 5 FR, SCAR-H PR : https://www.janes.com/defence-news/french-army-details-vehicle-deliveries-and-upgrade-plans
- PGM Hécate II : https://wikiwand.com/en/articles/PGM_Hecate_II
- Benelli M4 / M1014 : https://wikiwand.com/en/articles/M1014
- M32 MGL : https://en.wikipedia.org/wiki/Milkor_MGL
- Lance-flammes M2 : https://wikiwand.com/EN/M2-2
- Carl Gustaf M4 et AT4 : https://www.saab.com/markets/eu-nato-affairs/decisive-firepower-for-dismounted-forces
- RPO-A Shmel : https://en.wikipedia.org/wiki/RPO-A_Shmel
- Sacs de sable, gabions, HESCO : https://thinkdefence.co.uk/2024/12/sandbags-and-gabions
- Mine Claymore M18A1 : https://www.wikiwand.com/en/articles/Claymore_mine
- Infectés spéciaux de Left 4 Dead : https://gamedeveloper.com/game-platforms/analysis-interplay-in-i-left-4-dead-i-
- Bonnes pratiques tower defense : https://gamedeveloper.com/design/optimizing-tower-defense-for-focus-and-thinking---defender-s-quest
- Boucle de jeu et spirale de défaite : https://skills.sh/thedivergentai/gd-agentic-skills/genre-tower-defense
