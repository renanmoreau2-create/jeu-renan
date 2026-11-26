# DERNIER BASTION — document de conception (V1.2)

Tower defense post-apocalyptique. Un homme seul tient un bastion central contre des hordes de zombies qui arrivent par plusieurs chemins. Toute la progression passe par **un arbre d'évolution unique** : armes de l'homme, tours, pièges et bunker.

Titre provisoire. Toutes les valeurs d'équilibrage sont regroupées en tête du script de `index.html` (WEAPONS, DEF, MODULES, BUNKER_LV, ZT, TREE, UNLOCK_COST).

---

## 1. Boucle de jeu

1. **Préparation** (pas de chrono) : développer l'arbre d'évolution, poser des défenses, réparer le bunker.
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

- Les **pièges** (barbelés, mines, fossés) se posent n'importe où **sur** le chemin.
- Les **tours** se posent **uniquement sur 10 emplacements balisés** (plateformes en béton) : 4 sur le chemin Nord, 3 sur le Sud, 2 sur l'Est et 1 au pied du bunker. Placer une tour devient un choix tactique : quel type sur quel emplacement, et lequel garder libre pour plus tard.
- Le chemin Est est volontairement court : c'est une brèche de fin de partie qui oblige à réorganiser la défense.

## 3. L'arbre d'évolution unique (V1.2)

**Principe.** Au départ, un seul achat possible : le **nid de mitrailleuse**. L'homme a seulement son Glock 17 et des sacs de sable. Chaque nœud acheté ouvre ses enfants, et le joueur part dans la direction de son choix. À chaque étape, deux options :
- **Ouvrir** un nouvel élément : arme, tour, piège, niveau de bunker ou module.
- **Améliorer** un élément déjà acquis, à l'intérieur de son nœud : cadence, puissance et zone pour une arme ; niveaux 2 et 3 pour une défense ou un module.

**Quatre directions** partent du nid de mitrailleuse :

| Direction | Contenu | Enchaînement |
|---|---|---|
| Tir | Les 11 armes de l'homme | Glock → MP5 → Benelli (puis M32, Lance-flammes M2) ou HK416 (puis SCAR-H, Minimi) → Carl Gustaf, Hécate, Shmel |
| Appui | Tours | Tireur d'élite → Mortier ; Tour lance-flammes |
| Génie | Pièges | Barbelés → Mine, Fossé |
| Fortification | Bunker et modules | Double muret → HESCO → Casemate → Blindé, avec un module greffé à chaque palier |

**Croisements entre directions.** Les nœuds avancés exigent une autre direction : c'est ce qui crée des parcours différents d'une partie à l'autre.

| Nœud | Exige aussi |
|---|---|
| M32 | Mine |
| Carl Gustaf | Mortier |
| RPO-A Shmel | Lance-flammes M2 |
| Lance-flammes M2 | Tour lance-flammes |
| Hécate II | Tireur d'élite |
| Minimi | Nid de mitrailleuse niveau 2 |
| Mortier | Mine |
| Tour lance-flammes | Fossé |
| Couronne de barbelés | Barbelés |
| Claymore | Mine |
| Mitrailleuse 12,7 du bunker | Nid de mitrailleuse niveau 3 |
| Lance-flammes fixe | Tour lance-flammes |

**Règles retenues (validées par Renan) :**
- **Un seul arbre** pour tout.
- **Monnaie : l'argent des zombies**, la même que pour poser les défenses.
- **Améliorations globales** : faire passer la mitrailleuse au niveau 2 améliore tous les nids posés et à venir. Poser une défense coûte plus cher à mesure que son niveau monte (+50 % par niveau).

**Limite assumée.** Avec l'argent comme seule monnaie, une très longue partie finit par tout débloquer. Les choix pèsent surtout en début et en milieu de partie. Si c'est gênant, la parade est d'ajouter des embranchements exclusifs, où choisir une branche ferme l'autre.

**Affichage.** L'arbre s'ouvre en plein écran depuis l'onglet « Arbre ». Il se présente en branches indentées, lisibles sur téléphone : nœuds acquis en kaki, disponibles en surbrillance, verrouillés en grisé avec leurs prérequis. Ouvrir l'arbre pendant une vague met le jeu en pause. L'onglet « Construire » sert à poser les défenses débloquées, à changer d'arme et à réparer le bunker.

## 4. L'homme et ses armes (11 armes, toutes portables par un fantassin)

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

### Améliorations d'une arme (dans son nœud)

Chaque arme possédée a **3 branches de 5 niveaux**, comme demandé :
- **Cadence** : +18 % de tirs par seconde par niveau.
- **Puissance** : +25 % de dégâts par niveau.
- **Zone** : son effet dépend du type d'arme :
  - balles : +1 cible traversée ;
  - fusil à pompe : +1 plomb ;
  - explosifs : +15 % de rayon ;
  - lance-flammes : cône plus large et portée plus longue.

## 5. Le bunker (branche Fortification)

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

## 6. Défenses (branches Appui et Génie, 3 niveaux chacune)

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

## 7. Bestiaire

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

## 8. Vagues et économie : des hordes

Le principe retenu est la masse plutôt que la résistance individuelle.
- **Nombre de zombies** : 17 à la vague 1, environ 80 à la vague 10, environ 170 à la vague 20, environ 270 à la vague 30.
- **Arrivée en hordes** : des paquets serrés de 5 à plus de 25 zombies sur un même chemin, séparés par de courtes accalmies qui raccourcissent au fil des vagues. Les chemins sont attaqués à tour de rôle, donc simultanément dès qu'ils sont plusieurs.
- **Points de vie** : multipliés par 1,07 par vague jusqu'à la vague 15, puis la pente se durcit (+6 % supplémentaires par vague). La fin de partie ne devient donc pas une promenade.
- **Conséquence voulue** : les armes et défenses de zone (mines, mortier, lance-grenades, lance-flammes, barbelés) prennent le dessus sur le tir unique.
- Chaque nouveau type de zombie est annoncé à son arrivée, avec la parade conseillée.
- Armure du boss progressive : 4 à la vague 10, 6 à la vague 20, 8 à partir de la vague 30.
- Intervalle d'apparition : de plus en plus court. Les zombies se répartissent entre les chemins ouverts.
- Récompense par zombie : augmente avec la vague. La prime de fin de vague est garantie.
- Vitesse de jeu ×1, ×2 ou ×3, et pause.

## 9. Écarts assumés entre réalisme et jeu

- **Lance-flammes M2** : retiré du service américain en 1978. Gardé parce qu'il est emblématique et que le jeu se passe après l'effondrement.
- **Mines antipersonnel** : interdites par la convention d'Ottawa. Dans la fiction, c'est l'apocalypse.
- **Minigun** : exclu, parce qu'il n'est pas portable à la main par un soldat. La demande était « toutes les armes qu'un soldat peut porter ».
- **RPO-A Shmel en arme finale** : c'est l'arme portable non guidée au souffle le plus puissant de la liste (charge thermobarique). Un missile guidé comme l'Akeron MP ou le Javelin aurait moins de sens contre des zombies.

## 10. Calibrage (simulations automatiques)

Version 1.2 (arbre unique) : trois profils de joueurs automatiques, qui suivent chacun un ordre de déblocage différent.

| Profil | Vagues atteintes |
|---|---|
| Génie d'abord (pièges, puis tours) | 39 à 40 |
| Équilibré | 8 à 40, selon la composition des hordes |
| Armes d'abord | 12 à 30 : les morts à la vague 12 surviennent avec un bunker resté en sacs de sable face à une Brute |

Ajustements faits pour l'arbre :
- Coûts de déblocage des défenses réduits.
- Améliorations globales à 2 fois le coût unitaire du niveau.
- Récompenses relevées de 25 %.

Toutes les directions sont viables. La direction Génie est actuellement la plus forte face aux hordes, ce qui est cohérent avec le thème.


Version 1.1 (hordes et emplacements) : un joueur automatique qui répartit ses achats atteint la **vague 20 à 30**. Un joueur qui garde son argent sans construire tombe entre les **vagues 9 et 12**. Les murs voulus sont les boss des vagues 10, 20 et 30.

Ajustements faits pour les hordes :
- Récompense par zombie réduite, puisqu'ils sont plus nombreux.
- Argent de départ porté à 400.
- Armure du Blindé ramenée de 6 à 4 : le MP5 ne lui faisait plus qu'un point de dégât par balle.
- La pente des points de vie se durcit après la vague 15 : en simulation, le jeu devenait trop facile en fin de partie, avec 180 000 d'argent en banque à la vague 60.

Version 1 :

Ajustements faits après simulation :
- Bouffi moins destructeur au contact.
- Cracheur à portée de toutes les armes sauf le lance-flammes : le Benelli ne pouvait plus l'atteindre.
- Premier boss allégé : il était quasi invulnérable aux armes disponibles à la vague 10.
- Brute un peu moins résistante.

## 11. Pistes V2

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
