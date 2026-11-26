# DERNIER BASTION — document de conception (V1.8)

Tower defense post-apocalyptique. Un homme seul tient un bastion central contre des hordes de zombies qui arrivent par plusieurs chemins. Toute la progression passe par **un arbre d'évolution unique** : armes de l'homme, tours, pièges et bunker.

Titre provisoire. Toutes les valeurs d'équilibrage sont regroupées en tête du script de `index.html` (WEAPONS, DEF, MODULES, BUNKER_LV, ZT, TREE, UNLOCK_COST).

---

## 1. Boucle de jeu

1. **Préparation** (pas de chrono) : développer l'arbre d'évolution, poser des défenses, réparer le bunker.
2. **Vague** : les zombies sortent des bords de la carte et suivent les chemins jusqu'au bunker. L'homme tire automatiquement sur la cible la plus avancée. Un appui sur un zombie force la cible.
3. **Récompense** : chaque zombie tué rapporte de l'argent, plus une prime de fin de vague.
4. **Escalade** : plus de zombies, plus résistants, plus variés, et de nouveaux chemins s'ouvrent.
5. **Victoire** : tenir la vague 40. La partie peut continuer en mode infini.
6. **Défaite** : les points de vie du bunker tombent à zéro. Le record est conservé par difficulté, et la partie est sauvegardée à chaque début de préparation.

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

## 2 bis. Changements V1.4 (retours de Renan après une partie jusqu'à la vague 40)

**Emplacements limités pour les pièges aussi.** Les pièges ne se posent plus que sur 13 cases peintes sur la route, aux virages et aux abords du bastion. Les tours gardent leurs 10 emplacements balisés.

**Améliorations une à une.** L'arbre débloque les options : chaque défense a deux nœuds, niveau II et niveau III (par exemple Double MAG 58, puis M2 Browning 12,7). Ensuite, chaque défense posée s'améliore individuellement : on la touche sur la carte, puis « Améliorer celle-ci ». On peut ainsi ne faire évoluer que la mitrailleuse la mieux placée. Les prérequis qui demandaient un niveau de nid (Minimi, mitrailleuse 12,7 du bunker) pointent désormais vers ces nœuds d'option.

**Armes de l'homme simplifiées.** Chaque arme a 3 améliorations uniques, au lieu de 3 × 5 :

| Amélioration | Effet |
|---|---|
| Cadence | +50 % de tirs par seconde |
| Puissance | +80 % de dégâts |
| Zone | balles : traversent 2 cibles de plus ; fusil à pompe : +3 plombs ; explosifs : rayon +40 % ; lance-flammes : cône +35 % |

**Bonus à recharge.** Ils se débloquent dans l'arbre, ont 3 rangs chacun et se déclenchent pendant une vague avec les boutons ronds à droite de la carte.

| Bonus | Branche de l'arbre | Effet | Rang 1 | Rang 2 | Rang 3 |
|---|---|---|---|---|---|
| Super tir | Tir, sous le Glock | Multiplie les dégâts de l'arme de l'homme | ×4 pendant 3 s, recharge 180 s | ×5 pendant 4 s | ×6 pendant 5 s, recharge 120 s |
| Frappe d'artillerie | Appui, sous le mortier | On touche une zone, des obus y tombent | 6 obus | 9 obus | 12 obus |
| Réparation d'urgence | Fortification, sous le double muret | Rend une part des PV du bastion | 30 % | 45 % | 60 % |

**Directeur de vagues** (même principe que l'« AI Director » de Left 4 Dead). Il remplace la courbe fixe qui rendait le jeu trop facile puis infranchissable vers la vague 40.

1. **Puissance de feu** : estimée au début de chaque vague. On compte la meilleure arme de l'homme, plus toutes les tours, pièges et modules posés. Elle est affichée dans l'onglet Construire.
2. **Budget de la horde** : le total de PV de la horde est proportionnel à la puissance de feu (exposant 0,9), avec une lente montée dans le temps (+5 % par vague). Ce budget est réparti entre les zombies. Plus de zombies, c'est donc plus de zombies chacun moins résistants : l'effet horde est conservé, sans mur.
3. **Mesure après la vague** : le directeur regarde jusqu'où les zombies sont arrivés avant de mourir, et les dégâts subis par le bastion. Son objectif est que les zombies parcourent environ 60 % de la route avant de mourir, et que le bastion encaisse quelques dégâts réguliers.
   - **Défense trop confortable** : la horde suivante grossit, de 12 % au plus par vague.
   - **Trop de casse** : elle diminue franchement.
4. **Pas de partie infinie** : le répit ne descend jamais sous 80 % de la pression maximale déjà atteinte. La menace finit toujours par gagner, progressivement.

**Pourquoi pas un suivi total de la puissance de feu ?** Si la menace suivait la puissance à 100 %, chaque achat serait annulé aussitôt et la progression perdrait son sens. C'est pourquoi l'exposant est inférieur à 1 et la correction est bornée.

**Autres ajustements :**
- **Abomination allégée** (1 200 PV de base, 40 dégâts) : en simulation, chaque boss était un mur soudain.
- **Sauvegarde v4** : les anciennes sauvegardes ne sont pas reprises.

**Calibrage V1.4.** Trois profils de joueur automatique : équilibré, armes d'abord, génie d'abord. Les bonus sont utilisés automatiquement.
- **Fin de partie** : entre les vagues 30 et 70 selon le profil et la composition des hordes, avec une médiane d'environ 50.
- **Pertes** : elles apparaissent vers la vague 20, puis montent par paliers modérés.
- **Limite** : les joueurs automatiques jouent parfaitement. Un humain prendra des dégâts plus tôt, et le directeur l'accompagnera en conséquence.

## 2 sexies. Changement V1.8 : confirmation avec portée, menu des défenses posées

- **Construire en deux appuis** (Kingdom Rush) : le premier appui sur une défense du menu affiche sa portée sur la carte, le second construit. Le mortier montre aussi sa portée minimale (cercle rouge), la mine son rayon d'explosion.
- **Menu d'une défense posée**, sur la carte, au-dessus de sa silhouette :
  - **Améliorer** : en deux appuis. Le premier montre la portée du niveau suivant en vert. Si l'option n'est pas débloquée, le bouton mène au nœud de l'arbre ;
  - **Cible** : chaque appui passe à la priorité suivante (Premier, Plus fort, Plus proche, Blindé) ;
  - **Vendre** : en deux appuis, pour 60 % de ce qui a été investi ;
  - **Détails** : ouvre la fiche complète dans le panneau (statistiques, bilan).
- Toucher la même défense referme le menu. Le panneau du bas ne s'ouvre plus tout seul quand on touche une défense.

## 2 quinquies. Changement V1.7 : construction directe sur la carte

Retour de Renan : le réflexe est de toucher la case où l'on veut construire, pas d'aller dans le menu Construire. Comme dans Kingdom Rush, un appui sur un emplacement libre ouvre maintenant un menu au-dessus de la case :
- seulement les défenses compatibles (tours sur les dalles, pièges sur les marquages de la route), avec leur icône et leur prix ;
- un bouton grisé si l'argent manque, qui se réactive dès que l'argent arrive ;
- un raccourci vers l'arbre s'il reste des défenses de ce type à débloquer ;
- la case est surlignée tant que le menu est ouvert. Un appui ailleurs le ferme.

La construction directe fonctionne aussi pendant une vague. Le mode « Poser une défense » du panneau reste disponible pour enchaîner plusieurs poses du même type.

## 2 quater. Changements V1.6 : rogue-lite hybride

Choix validé par Renan : l'arbre reste dans la partie, avec son principe « débloquer ou améliorer ». Deux couches s'y ajoutent.

**Cartes de ravitaillement** (inspiration : Thronefall, Rogue Tower). Après les vagues 5, 10, 15, et ainsi de suite, on choisit une carte parmi 3, tirées au hasard. Elle vaut pour toute la partie. Une carte n'est proposée que si elle sert : pas de carte « Mines » sans mines débloquées. Un appel anticipé qui saute un multiple de 5 n'en fait pas perdre : les tirages dus s'enchaînent.

| Carte | Famille | Effet par exemplaire | Maximum |
|---|---|---|---|
| Munitions perforantes | Tir | balles de l'homme : +1 cible traversée | 2 |
| Main sûre | Tir | arme de l'homme : +20 % de cadence | 3 |
| Charge creuse | Tir | arme de l'homme : +25 % de dégâts | 3 |
| Bandes de cartouches | Appui | nids de mitrailleuse : +25 % de cadence | 2 |
| Lunettes de précision | Appui | tireurs d'élite : +30 % de dégâts, +15 % de portée | 2 |
| Obus à fragmentation | Appui | mortiers : rayon +25 % | 2 |
| Napalm | Appui | tous les lance-flammes : brûlure +50 % | 2 |
| Barbelé électrifié | Génie | barbelés, pièges et couronne : +100 % de dégâts | 2 |
| Charges renforcées | Génie | mines : +40 % de dégâts, réarmement -20 % | 2 |
| Fossés profonds | Génie | fossés : ralentissement +15 points | 1 |
| Sacs supplémentaires | Fortification | bastion : +20 % de PV max | 3 |
| Plaques de blindage | Fortification | bastion : armure +2 | 2 |
| Maçons | Fortification | réparations à moitié prix | 1 |
| Prime de chasse | Économie | +15 % d'argent par zombie | 3 |
| Ravitaillement | Économie | argent immédiat : 150 + 40 × la vague | sans limite |
| Intendance | Économie | défenses à poser : -15 % de prix | 2 |
| Opérateur radio | Bonus | recharge des bonus -25 % | 2 |

**Insignes et Quartier général.** Chaque vague tenue rapporte des insignes, conservés entre les parties : 0,5 en Facile, 1 en Normal, 1,5 en Cauchemar, plus 10 vagues de prime à la victoire. Ils sont gagnés vague par vague : abandonner une partie ne les fait pas perdre. Le Quartier général, sur l'écran titre, les échange contre des avantages permanents volontairement modestes, pour ne pas fausser la courbe de difficulté.

| Avantage | Rang 1 | Rang 2 | Rang 3 | Coût en insignes |
|---|---|---|---|---|
| Trésor de guerre | +100 d'argent au départ | +200 | +300 | 4, 8, 14 |
| Sacs de réserve | +10 % de PV du bastion | +20 % | +30 % | 5, 10, 18 |
| Récupération | +5 % d'argent par zombie | +10 % | +15 % | 6, 12, 20 |
| Relance | 1 relance de tirage par partie | 2 | 3 | 5, 10, 16 |
| Choix élargi | 4 cartes proposées au lieu de 3 | | | 20 |
| Briefing | une carte au choix dès le début | | | 15 |

Tout débloquer coûte 163 insignes, soit environ 4 à 6 parties en Normal.

**Calibrage V1.6** (cartes choisies au hasard par les joueurs automatiques, Normal à × 0,7) : les cartes renforcent nettement le joueur. Le joueur « achats toutes les 3 vagues » passe de 13 à 6 vagues avec pertes sur 40, et tombe aux vagues 60 à 70 au lieu de 56 à 60. Normal a donc été relevé à × 0,75 et Cauchemar à × 0,9. Ce réglage reste provisoire : il doit être confirmé par des parties humaines.

## 2 ter. Changements V1.5 (gameplay : un but, de l'information, des décisions)

**Pourquoi.** Le directeur de vagues V1.4 réglait la horde sur la puissance de feu du joueur : chaque amélioration était aussitôt compensée, et un bon placement n'était pas récompensé. Les références du genre (Kingdom Rush, Bloons TD 6) font l'inverse : des vagues fixes, identiques à chaque partie, qu'on bat en optimisant.

**Courbe fixe.** Le total de PV d'une vague ne dépend plus du joueur :

`budget(w) = 14 × Pref(w)^0,9 × (1 + 0,05 (w − 1)) × (1 + 0,2 × max(0, w − 10)) × difficulté × correction`

- `Pref(w) = 85 + 5,5 w²` : puissance de feu d'un joueur automatique appliqué à la vague w, mesurée par simulation (85 à la vague 1, 9 000 environ à la vague 40).
- Le terme en `0,2 × (w − 10)` reproduit la pression que les joueurs automatiques encaissaient sous le directeur V1.4.
- **Correction** : il reste un léger rattrapage, borné entre 0,85 et 1,15 (-7 % après une vague à plus de 30 % de pertes, +3 % après une vague sans dégâts).

**Trois difficultés**, choisies au lancement :

| Difficulté | PV des hordes | Dégâts des zombies | Argent |
|---|---|---|---|
| Facile | × 0,45 | × 0,85 | × 1,1 |
| Normal | × 0,75 (× 0,7 en V1.5) | × 1 | × 1 |
| Cauchemar | × 0,9 (× 0,85 en V1.5) | × 1,2 | × 0,95 |

**Victoire à la vague 40**, puis mode infini au choix. Records conservés par difficulté, avec la mention de victoire. Le compteur affiche « 12/40 ».

**Aperçu de la prochaine vague** (onglet Construire) : composition par type, routes, nouveaux types. La puissance de feu du joueur y est comparée à un repère pour la difficulté choisie ; un avertissement apparaît en dessous de 80 % du repère.

**Appel anticipé** (Kingdom Rush) : dès que tous les zombies d'une vague sont sortis, le bouton du bas permet d'appeler la suivante. Prime immédiate : la moitié de la prime de fin de la vague appelée. Les primes de fin de vague restent toutes versées.

**Statistiques et bilan** (Bloons TD 6) : chaque tour, piège, module et l'homme comptent leurs dégâts et éliminations. Après chaque vague, un bilan classe les sources par part des dégâts ; toucher une ligne sélectionne la défense. La fiche d'une défense affiche ses chiffres depuis le début et sur la dernière vague.

**Priorité de tir par tour** : Premier (le plus avancé), Plus fort (le plus de PV), Plus proche, Blindé. Le tireur d'élite démarre sur « Plus fort », les autres sur « Premier ».

**Calibrage V1.5** (joueurs automatiques parfaits, sans appel anticipé ni priorités de tir) :

| Profil | Normal | Cauchemar (× 0,9) |
|---|---|---|
| Équilibré | vague 57, pertes rares | vague 31 |
| Génie d'abord | vague 58 | vague 50, pertes fréquentes |
| Armes d'abord | vague 21 : mur brutal, bastion resté faible | vague 19 |
| Équilibré, achats toutes les 3 vagues (proche d'un humain) | vague 60, pertes dans 13 vagues sur 40 | non testé |
| Génie, achats toutes les 3 vagues | vague 56, pertes dans 12 vagues sur 40 | non testé |

Cauchemar a ensuite été adouci à × 0,85. **Limite connue** : un joueur qui délaisse tours, pièges et bastion tombe d'un coup vers la vague 20, sans pertes préalables. Les hordes rendent la défaite binaire. L'avertissement de l'aperçu est la parade actuelle.

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

**Affichage (V1.3).** L'écran « Améliorations » est un graphe que l'on fait glisser et que l'on pince pour zoomer. Il part d'un point central, et chaque élément y est représenté par une icône dessinée dans le jeu.
- **Au départ**, seul le point central est visible : le nid de mitrailleuse.
- **Révélation progressive** : seuls les éléments suivants apparaissent, en grisé avec leur prix. Tout le reste reste caché jusqu'à ce que son parent soit acquis, et les nouveaux éléments apparaissent avec une courte animation.
- **États** : un élément acquis est en couleur avec un cadre kaki. Un élément bloqué par un croisement porte un cadenas.
- **Fiche détail** : toucher un élément l'ouvre en bas de l'écran. On y débloque l'élément ou on l'améliore.

Ouvrir l'arbre pendant une vague met le jeu en pause. L'onglet « Construire » sert à poser les défenses débloquées, à changer d'arme et à réparer le bunker.

## 3 bis. Rendu (V1.3) : vue 3/4 de nuit, en portrait

- **Vue** : le sol est légèrement écrasé verticalement pour donner l'inclinaison de caméra. Tours, lampadaires, épaves et zombies sont dessinés debout et triés en profondeur. La carte remplit l'écran, et les routes se prolongent au-delà des bords.
- **Nuit** : un voile sombre, percé par les lampadaires le long des routes et par la lueur autour du bastion. Les tirs, explosions et flammes éclairent la scène en temps réel.
- **Routes** : asphalte avec bordures et ligne médiane, virages conservés. Un portail grillagé à chaque entrée : fermé avec un feu rouge tant que la route n'est pas ouverte, ouvert ensuite.
- **Tour centrale**, qui évolue avec la branche Fortification :

| Stade | Aspect | Position de l'homme |
|---|---|---|
| 1 | Anneau de sacs de sable | Il est debout derrière |
| 2 | Double muret | Il est debout dessus |
| 3 | Bastion HESCO | Il est sur les gabions |
| 4 | Casemate béton à meurtrière | Il est sur le toit |
| 5 | Bastion blindé sur plusieurs étages, avec fenêtres éclairées ou barricadées | Il est sur le toit |

  Les modules sont visibles sur la tour : mitrailleuse 12,7 et lance-roquettes sur le toit, lance-flammes en façade, Claymore au pied, couronne de barbelés au sol.
- **Lisibilité** :
  - les zombies cachés derrière la tour apparaissent en silhouette translucide ;
  - les barres de vie ne s'affichent que pour les gros zombies, sauf quand la horde est clairsemée ;
  - au-delà de 130 zombies, leur dessin est simplifié.
- **Performance mesurée** : environ 40 images/s avec 180 zombies dans un navigateur sans accélération graphique. Sur un téléphone, avec le processeur graphique, le rendu devrait être plus fluide.

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

- Rogue-lite hybride : fait en V1.6.

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
