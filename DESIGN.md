# DERNIER BASTION — document de conception (V2.7)

Tower defense post-apocalyptique. Un homme seul tient un bastion central contre des hordes de zombies qui arrivent par plusieurs chemins. Toute la progression passe par **un arbre d'évolution unique** : armes de l'homme, tours, pièges et bunker.

Titre provisoire. Toutes les valeurs d'équilibrage sont regroupées en tête du script de `index.html` (WEAPONS, DEF, MODULES, BUNKER_LV, ZT, TREE, UNLOCK_COST).

---

## 1. Boucle de jeu

1. **Préparation** (pas de chrono) : développer l'arbre d'évolution, poser des défenses, réparer le bunker.
2. **Vague** : les zombies sortent des bords de la carte et suivent les chemins jusqu'au bunker. L'homme tire automatiquement sur la cible la plus avancée. Un appui sur un zombie force la cible.
3. **Récompense** : chaque zombie tué rapporte de l'argent, plus une prime de fin de vague.
4. **Escalade** : plus de zombies, plus résistants, plus variés, et de nouveaux chemins s'ouvrent.
5. **Victoire** : tenir la vague 25 (V2.1, auparavant 40). La partie peut continuer en mode infini.
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
| Sud | bord bas | 14 cases, en S | vague 4 |
| Est | bord droit, en bas | 10 cases, flanc court | vague 9 |

- Les **pièges** (barbelés, mines, fossés) se posent n'importe où **sur** le chemin.
- Les **tours** se posent **uniquement sur 10 emplacements balisés** (plateformes en béton) : 4 sur le chemin Nord, 3 sur le Sud, 2 sur l'Est et 1 au pied du bunker. Placer une tour devient un choix tactique : quel type sur quel emplacement, et lequel garder libre pour plus tard.
- Le chemin Est est volontairement court : c'est une brèche de fin de partie qui oblige à réorganiser la défense.

## 2 bis. Changements V1.4 (retours de Renan après une partie jusqu'à la vague 40)

**Emplacements limités pour les pièges aussi.** Les pièges ne se posent plus que sur 13 cases peintes sur la route, aux virages et aux abords du bastion. Les tours gardent leurs 10 emplacements balisés.

**Améliorations une à une.** L'arbre débloque les options : chaque défense a deux nœuds, niveau II et niveau III (par exemple mitrailleuse jumelée, puis mitrailleuse lourde 12,7). Ensuite, chaque défense posée s'améliore individuellement : on la touche sur la carte, puis « Améliorer celle-ci ». On peut ainsi ne faire évoluer que la mitrailleuse la mieux placée. Les prérequis qui demandaient un niveau de nid (mitrailleuse légère, mitrailleuse 12,7 du bunker) pointent désormais vers ces nœuds d'option.

**Armes de l'homme simplifiées.** Chaque arme a 3 améliorations uniques, au lieu de 3 × 5 :

| Amélioration | Effet |
|---|---|
| Cadence | +50 % de tirs par seconde |
| Puissance | +80 % de dégâts |
| Zone | balles : traversent 2 cibles de plus ; fusil à pompe : +3 plombs ; explosifs : rayon +40 % ; lance-flammes : cône +35 % |

**Bonus à recharge.** Ils se débloquent dans l'arbre, ont 3 rangs chacun et se déclenchent pendant une vague avec les boutons ronds à droite de la carte.

| Bonus | Branche de l'arbre | Effet | Rang 1 | Rang 2 | Rang 3 |
|---|---|---|---|---|---|
| Super tir | Tir, sous le pistolet | Multiplie les dégâts de l'arme de l'homme | ×4 pendant 3 s, recharge 180 s | ×5 pendant 4 s | ×6 pendant 5 s, recharge 120 s |
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

## 2 quindecies. Changement V2.7 : la nuit, les équipes et des noms génériques

**Demande de Renan** : les zombies attaquent de nuit et le jour revient à la fin de chaque vague. Il veut un éclairage améliorable, des lumières que les zombies peuvent casser et une usure des tireurs (stress et fatigue) qui fait partir des tirs à côté. Les armes doivent aussi porter le nom de leur catégorie plutôt que celui d'un fabricant.

**1. Le jour et la nuit.** On construit de jour et on se bat de nuit. Au lancement d'une vague, l'écran s'assombrit en une seconde environ et un bandeau annonce « La nuit tombe ». À la fin de la vague, un autre bandeau annonce « Le jour se lève » avec la prime. Un appel anticipé enchaîne deux nuits ; le bandeau prévient alors : « Pas de repos pour les équipes ». Le bouton de vague affiche « Vague N · nuit ».

**2. L'éclairage.** Les lampes de la carte éclairent dans un rayon de 56 et le bastion dans un rayon de 72. Tout le reste est dans le noir. Trois améliorations s'achètent dans la fiche du bastion (section « Éclairage de nuit ») :

| Niveau | Nom | Coût | Effet |
|---|---|---|---|
| 1 | Lampes renforcées | 250 | lampes 30 % plus puissantes et trois fois plus solides (90 PV au lieu de 30) |
| 2 | Projecteurs | 700 | le bastion éclaire à 4 cases ; chaque tour éclaire ses abords |
| 3 | Fusées éclairantes | 1 600 | toutes les 15 s, une fusée éclaire pendant 8 s la horde la plus proche du bastion |

**3. Lampes cassables.** Un zombie à moins de 30 d'une lampe l'abîme à la moitié de ses dégâts par seconde ; les Brutes et l'Abomination l'abîment à 80 par seconde. Une lampe cassée lâche des étincelles et n'éclaire plus. Toutes les lampes sont réparées à l'aube. Une route sans lumière devient donc plus dure au fil de la nuit, et les lampes renforcées tiennent plus longtemps.

**4. Stress et fatigue.** Deux sortes d'équipes s'usent : celle du bastion (l'homme et les armes du bastion) et celle de chaque tour.
- **Stress** : il dépend de la menace dans un rayon d'environ une case et demie (Abomination 4, Brute 2, autres zombies 1, plafond à 12). Il est majoré dans le noir (facteur de 0,6 en pleine lumière à 1 dans le noir complet). Il monte vite et redescend lentement. Chaque coup reçu par le bastion ajoute 3 % au stress de son équipe.
- **Fatigue** : elle augmente de 0,6 % par seconde quand l'équipe tire et de 0,15 % sinon. Elle ne retombe pas pendant la nuit.
- **Aube** : le stress revient à zéro et la fatigue est réduite de 65 %. Enchaîner les vagues par appels anticipés coûte donc de la précision.

**5. Tirs à côté.** À chaque tir, la probabilité de rater vaut :
3 % de base (2 % pour le tireur d'élite)
+ 22 % × obscurité sur la cible
+ 12 % × stress + 15 % × fatigue,
avec un plafond à 65 %.

Le raté est simulé physiquement, sans tirage caché :
- une balle part avec un angle qui la fait passer juste à côté du zombie visé, et peut toucher un autre zombie ;
- la gerbe du fusil à pompe est déviée ;
- l'obus, la grenade ou la roquette tombe à côté du point visé ;
- les lance-flammes ne ratent pas ;
- un zombie en feu se voit dans le noir et compte comme éclairé.

**6. Ce que voit le joueur.**
- Le sélecteur d'arme indique « précision X % » dès qu'elle passe sous 92 %.
- L'en-tête du menu d'une tour donne sa précision pendant la vague.
- La fiche d'une défense a une ligne « Équipe » : stress, fatigue, précision.
- Une pastille d'alerte apparaît sur la carte quand une équipe passe sous 82 % de précision ; elle devient rouge sous 65 %.
- Le bilan de vague affiche « Tirs partis à côté cette nuit ».
- Les lampes ont trois états : ampoule éteinte le jour, halo la nuit, ampoule brisée.

**7. Trois cartes de ravitaillement.**
- Jumelles de vision nocturne : la pénalité d'obscurité est divisée par deux (1 exemplaire au plus).
- Discipline de feu : le stress baisse de 40 % par carte (2 au plus).
- Café et rations : la fatigue monte deux fois moins vite (1 au plus).

**8. Noms génériques.** Plus aucun nom de fabricant ni de modèle dans le jeu.

| Avant | Maintenant (nom court) |
|---|---|
| arme de départ | Pistolet 9 mm (Pistolet) |
| PM | Pistolet-mitrailleur 9 mm (PM 9 mm) |
| fusil à pompe | Fusil de combat calibre 12 (Fusil à pompe) |
| fusil d'assaut | Fusil d'assaut 5,56 mm |
| fusil de précision de l'homme | Fusil de précision 7,62 mm (Fusil 7,62) |
| mitrailleuse légère | Mitrailleuse légère 5,56 mm |
| lance-grenades | Lance-grenades 40 mm |
| fusil lourd | Fusil anti-matériel 12,7 mm (Anti-matériel) |
| lance-flammes de l'homme | Lance-flammes dorsal |
| arme antichar | Canon sans recul 84 mm (Sans recul) |
| arme finale | Lance-roquettes thermobarique (Thermobarique) |
| nid de mitrailleuse, niv. 1 à 3 | Mitrailleuse 7,62, Mitrailleuse jumelée, Mitrailleuse lourde 12,7 |
| tireur d'élite, niv. 1 à 3 | Fusil de précision, Fusil de précision semi-auto, Fusil anti-matériel |

**9. Calibrage.** Le jeu reste joué par des joueurs automatiques qui achètent à chaque vague. Le joueur « équilibré » achète l'éclairage aux vagues 2, 5 et 8.
- **Taux de ratés mesurés** : 23 à 30 % sans éclairage, 13 à 18 % avec les trois niveaux.
- **Compensation** : sans elle, la nuit retirait 20 à 30 % de puissance de feu réelle. Le budget des hordes est donc multiplié par `NIGHT_K = 0,76` : à puissance égale, les hordes sont 24 % moins résistantes. Avec 0,85 puis 0,8, la partie Normal tombait aux vagues 13 à 26. Avec 0,76, elle tient jusqu'à 28 avec de lourdes pertes après la vague 20, comme en V2.6.
- **Arrivée des Brutes** : la première Brute arrive désormais seule. Auparavant, trois Brutes arrivaient d'un coup, plus une quatrième toutes les 3 vagues. En Cauchemar, une partie sur deux mourait à la vague 9, sans aucun dégât avant. Ce défaut existait déjà en V2.6 (mesuré : 9 et 18). Depuis la correction, le Cauchemar tombe aux vagues 17 et 20, avec de premières pertes à la vague 9.
- **Mission 1** : la résistance des hordes passe de 1,25 à 1,1. Le joueur automatique qui achète à chaque vague mourait une fois sur trois à la vague 11, d'un coup. Sur 8 parties, il gagne désormais 7 fois.

| Cas | V2.6 | V2.7 |
|---|---|---|
| Normal, joueur équilibré | tient jusqu'à 28 | tient jusqu'à 28 (2 parties sur 2), pertes fréquentes après la vague 20, jusqu'à 98 % en une vague |
| Cauchemar | morts aux vagues 9 et 18 | morts aux vagues 17 et 20 |
| Mission 1 | 2 morts à la vague 9 (achats à chaque vague) | 7 victoires sur 8, défis Génie et mitrailleuses gagnés 2 sur 2 |
| Mission 2 | 2 victoires sur 2 | 2 victoires sur 2 (1 étoile, 65 et 78 % de PV perdus) |
| Mission 3 | 1 victoire sur 2 | 2 victoires sur 2 (1 et 2 étoiles, 56 à 69 % de PV perdus) ; défi Génie 2 sur 2 |

**10. Limites connues.**
- **Joueur humain** : le joueur automatique ne vise pas lui-même, il ne place pas ses lampes et ne fait pas de choix tactiques de nuit. Un humain qui éclaire les bonnes routes ratera moins ; un humain qui enchaîne les appels anticipés ratera plus. Le réglage de `NIGHT_K` reste à vérifier sur les retours de Renan.
- **Spirale de stress** : quand une horde atteint le bastion, le stress monte, la précision baisse et la horde avance encore. Le joueur peut perdre beaucoup de PV en une seule vague. La pastille d'alerte et les fusées éclairantes donnent un signal d'alarme et un moyen d'agir, mais l'effet reste brutal.
- **Mission 1** : quand elle tourne mal, la défaite tombe encore en une seule vague (la 11), sans dégâts avant.

## 2 quattuordecies. Changement V2.6 : moteur de cartes et campagne « Apocalypse »

**Décision de Renan** : rendre d'abord les cartes interchangeables, puis faire 3 missions d'essai de 12 à 15 vagues, avec des défis par mission. Le monde Far West viendra plus tard sur le même moteur, une fois les missions validées.

**1. Moteur de cartes.** La carte n'est plus écrite en dur : `MAPS` décrit pour chaque carte le bastion, les routes (avec leur vague d'ouverture), les emplacements de tours, les emplacements de pièges et les graines du décor et du sol. `loadMap(id, ouvertures)` reconstruit routes, emplacements, décor, sol et lumières. La partie libre et le défi du jour restent sur « Le carrefour », identique à la V2.5.

**Règles de dessin d'une carte** (rappelées dans le code) :
- grille de 9 x 13 cases, routes en lignes droites qui finissent au bastion ;
- coins du haut libres : une entrée par le haut seulement dans les colonnes 2 à 4, et une entrée par le bas à partir de la colonne 3 (le sélecteur d'arme occupe le coin bas gauche) ;
- un fort de 3 x 3 cases autour du bastion ;
- des pièges espacés d'au moins une case, aucun juste au-dessus du bastion ;
- des tours au bord des routes, en privilégiant les cases qui couvrent deux routes.

**2. Les trois missions**

| Mission | Carte | Vagues | Routes | Nouveautés | Abominations |
|---|---|---|---|---|---|
| 1. Le poste avancé | bastion bas, longue route du nord en lacets, route de l'est à la vague 6 | 12 | 2 | arbre réduit : nid, tireur d'élite, barbelés, mines ; Coureurs, Rampants, Bouffis | 1 à la vague 12 |
| 2. La raffinerie | bastion haut, routes ouest et est dès le départ, sud à la vague 7 | 14 | 3 | mortier, fossé, bunker à étage ; Blindés, Cracheurs | 1 à la vague 7, 2 à la vague 14 |
| 3. Le carrefour | la carte de la partie libre, est à la vague 7 | 15 | 3 | tout l'arsenal ; Hurleurs, Brutes | 1 à la vague 8, 2 à la 12, 3 à la 15 |

Chaque mission a sa propre échelle de difficulté :
- `eff0` (point de départ) et `pace` (progression par vague), sur la même échelle que la partie libre ;
- son argent de départ : 400, 700 et 1 000 ;
- la résistance des hordes : 1,25, 1,0 et 0,64 ;
- les nœuds de l'arbre absents, ainsi que tout ce qui en dépend.

En mission, l'Abomination compte pour 55 % de son poids habituel dans le budget de la vague. Sans cela, les vagues ordinaires étaient triviales et toute la mission se jouait sur le boss final.

**3. Étoiles.** Elles se calculent sur les PV perdus par le bastion sur toute la mission, rapportés à ses PV maximum en fin de mission :
- 3 étoiles sous 20 % ;
- 2 étoiles sous 60 % ;
- 1 étoile au-delà.

Chaque nouvelle étoile rapporte 5 insignes, en plus des insignes gagnés par vague. Une mission s'ouvre quand la précédente est gagnée.

**4. Défis**, ouverts après une première victoire sur la mission (15 insignes la première fois) :
- **Génie** : aucune tour. Pièges, bastion (couronne, Claymore) et l'homme. La racine de l'arbre est offerte. Les hordes y sont 15 % moins résistantes.
- **Arsenal unique** : une seule sorte de tour (mitrailleuses en mission 1, mortiers en mission 2, tireurs d'élite en mission 3). Pièges et bastion permis.

**5. Écrans.**
- Titre : « Campagne » et « Partie libre ».
- Campagne : une fiche par mission, avec son numéro, ses étoiles, les boutons Jouer ou Rejouer, les défis et l'état réussi.
- Briefing : le contexte, « Au programme », la règle des étoiles ou celle du défi.
- Fin de mission : grandes étoiles, ce qu'il faut pour l'étoile suivante, les récompenses, puis « Mission suivante », « Campagne », « Rejouer » et « Partager ».
- En cas de défaite en mission : « Rejouer la mission » et « Campagne ».
- Une mission en cours se sauvegarde et se reprend comme une partie libre.

**Calibrage** (joueurs automatiques, 2 parties par cas) :
- **Mission 1** (achats toutes les 2 vagues) : profil équilibré, 3 puis 2 étoiles. Défi Génie gagné 2 fois sur 2 (1 étoile). Mitrailleuses seules : 2 sur 2.
- **Mission 2** (achats à chaque vague) : profil équilibré, 1 puis 2 étoiles. Défi Génie gagné 1 fois sur 2. Mortiers seuls : 2 sur 2, de justesse.
- **Mission 3** (achats à chaque vague) : profil équilibré, 1 victoire (1 étoile) sur 2. Avec des achats toutes les 2 vagues, défaite aux vagues 8 à 10. Défi Génie gagné 1 fois sur 2. Tireurs d'élite seuls : 2 sur 2 (2 étoiles).
- **Limite** : les vagues du début restent faciles pour un joueur qui construit vite. Les retours de Renan régleront la mission 1, qui sert d'apprentissage.

## 2 terdecies. Changement V2.5 : lisibilité autour du bastion

**Retours de Renan** :
- un texte restait sous les compteurs de zombies des routes ;
- une rose des vents aiderait à s'orienter ;
- les deux emplacements de pièges collés au bastion (routes Nord et Sud) et les trois nids autour du bastion se mélangeaient visuellement avec la couronne de barbelés.

**Corrections.**
- **Compteurs des routes** : seulement le crâne et le nombre. Le nom de la route reste lu par les lecteurs d'écran et apparaît dans la bulle quand on touche le compteur.
- **Rose des vents** dans le coin haut gauche (N, S, E, O ; le Nord en doré), en face des pouvoirs en haut à droite. Elle ne capte pas les appuis.
  - *Règle pour les cartes futures* : garder libre le coin haut gauche (colonnes 0 et 1, au-dessus de la rangée 0) comme le coin haut droit.
- **Couronne de barbelés redessinée.** Ce n'est plus un cercle de 1,5 case qui traversait les nids et les pièges. C'est un carré aux coins arrondis qui suit le bord extérieur du « fort » : le bastion et ses 8 cases voisines.
  - Les trois nids d'angle et le piège collé au Sud sont à l'intérieur ; la couronne passe derrière les sacs de sable.
  - Elle croise les routes à 60 unités du centre, là où commence son effet (rayon de ralentissement de 60 à 68).
  - Une seule bande, posée sur la limite des cases, avec des piquets hors des routes : elle ne déborde plus sur les emplacements voisins. Le niveau se lit à la densité des spires (1 à 3 brins).
- **Emplacement de piège Nord déplacé** de la case collée au bastion (4,5), cachée par le bâtiment dès qu'il grandit, au virage de la route Nord (4,4). Une partie sauvegardée avec un piège en (4,5) le retrouve en (4,4).
- **Pièges de la route Nord espacés** (retour de Renan : deux cases collées après le déplacement). Ils passent en (5,1), (7,2), (6,4) et (4,4), toujours séparés d'au moins une case. Les pièges des parties sauvegardées suivent.
- **Barre du haut** : la vie du bastion affiche le nombre seul (la barre montre la proportion) pour ne plus être coupée au-delà de 1 000 PV.

## 2 duodecies. Changement V2.4 : refonte visuelle

**Demande de Renan** : une barre du haut aussi belle que celle du bas, des menus d'évolution et un bilan plus beaux, et un travail visuel d'ensemble, sur le modèle des meilleurs jeux.

**Principes retenus** (pratiques reconnues du jeu mobile) :
- les ressources s'affichent en icône plus chiffre, ancrées dans les coins (Bloons TD 6, Kingdom Rush) ;
- peu d'informations à l'écran pendant l'action ;
- des chiffres gros et gras ;
- un style unique partout (Clash Royale) : panneaux sombres à liseré, boutons épais à biseau, une seule couleur d'action (kaki doré) ;
- vert pour ce qui va bien, orange pour l'alerte, rouge pour le danger.

**Barre du haut.**
- Trois blocs : Vague (crâne), Argent (pièce, chiffre doré), Bastion (bouclier, PV en gros et barre de vie dessous, qui clignote sous 35 % pendant une vague).
- À droite, la vitesse et un bouton Pause à icône. La pause ouvre un menu (Reprendre, Son activé ou coupé) : le bouton Son quitte la barre pour laisser de la place à la vie du bastion.
- Sur un écran étroit (moins de 370 px), les petits libellés disparaissent.

**Arbre d'évolution.**
- En-tête : titre, argent dans une pastille, fermeture par une croix ronde. Zoom par boutons ronds à icône.
- Nœuds arrondis. Un nœud acquis est doré et lumineux. Un nœud achetable avec l'argent disponible pulse en doré et son prix devient une pastille dorée. Un nœud verrouillé est estompé, et le nœud sélectionné est cerclé de blanc chaud.
- Fiche du nœud : grande vignette, statut en pastille (Acquis, Disponible, Verrouillé), niveaux en cartes, et un grand bouton doré « Débloquer · prix ».

**Bilan.**
- Le bandeau de fin de vague porte une icône (coche verte si le bastion est intact, sinon alerte rouge), le résultat et la prime. Il a un bouton « Bilan » doré et une croix. Il se cache quand un menu de la carte est ouvert.
- La fiche du bilan ouvre sur trois tuiles (abattus, état du bastion, primes). Suit « Qui a tenu la ligne » : un classement numéroté, le premier en or, avec une barre de part des dégâts.
- La prochaine vague s'affiche en pastilles de couleur par type de zombie. Une jauge de puissance de feu porte un repère (vert au-dessus, orange un peu sous, rouge nettement sous).

**Ensemble.**
- Fiches : en-tête plus marqué, fermeture par croix ronde (les doubles « Fermer » ont été retirés), titres de section soulignés d'un filet.
- Cartes et menus de la carte : arrondis, liseré, prix en pastille dorée.
- Écrans (titre, difficulté, ravitaillement, Quartier général, défaite, victoire, pause) : présentés dans un cadre.
- Les cartes de ravitaillement gagnent un relief et une catégorie en pastille. Le bandeau de vague passe en majuscules.
- Toutes les animations respectent le réglage « réduire les animations » de l'appareil.

## 2 undecies. Changement V2.3 : rythme des achats, réalisme étendu, nouvelle barre de commande

**Retours de Renan** (victoire en Normal en V2.2, avec quelques dégâts rattrapés par les réparations) :
- l'arbre ne doit s'ouvrir qu'entre deux vagues, mais ce qui est déjà débloqué doit rester achetable pendant l'assaut ;
- les tireurs d'élite doivent recharger, et les armes du bastion suivre les mêmes règles que les tours ;
- le texte de l'option du lance-flammes débordait du bouton ;
- les pouvoirs doivent aller en haut à droite, et cet endroit doit rester libre sur les cartes futures ;
- la fin de partie doit punir un mauvais placement en Normal, et le Cauchemar doit éliminer, dur dès la vague 15 ;
- la fiche du bas recouvrait à moitié les trois boutons ;
- l'arme de l'homme doit se choisir dans un menu déroulant, et la barre du bas doit être plus belle, sur le modèle des bons jeux mobiles.

**1. L'arbre seulement entre deux vagues.** Pendant une vague, l'onglet Arbre affiche un cadenas et refuse de s'ouvrir avec un message. Tout achat de nœud est refusé : étage du bastion, installation d'un module, déblocage d'un bonus ou d'une arme. Reste achetable pendant l'assaut tout ce qui est déjà débloqué :
- construire une défense débloquée ;
- monter une défense jusqu'au niveau débloqué ;
- les options par tour (canon, réservoir, chargeur) ;
- les améliorations de l'arme de l'homme ;
- les niveaux des modules et les rangs des bonus ;
- la réparation.

Un niveau non débloqué affiche « arbre après la vague ». La règle « l'arbre débloque, la carte améliore » devient aussi une règle de rythme.

**2. Tireurs d'élite : chargeurs.**
- Fusil de précision : 10 coups, rechargement en 3 s. Semi-auto : 20 coups, 2,8 s. Anti-matériel : 7 coups, 3,5 s.
- Rechargement tactique quand la tour n'a plus de cible depuis 1 s et que son chargeur est entamé de 30 %.
- Option « Grand chargeur » (350) : chargeur +50 %, rechargement 35 % plus rapide.
- Sur la carte, une barre de munitions sous la tour, puis un cercle doré pendant le rechargement.

**3. Armes du bastion : mêmes règles que les tours.**
- La 12,7 du bastion se comporte comme la mitrailleuse lourde du nid : 70 tirs, puis canon changé en 5 s.
- Le lance-flammes fixe se comporte comme la tour lance-flammes du même niveau.
- Les options « Canon de rechange » (300) et « Grand réservoir » (450) s'achètent dans la fiche Bastion.
- Mêmes jauges que les tours.

**4. Menus.** Les options par tour ont des libellés courts (« Canon de rechange », « Réservoir +50 % », « Chargeur +50 % »). Les boutons du menu d'une défense se partagent la largeur et le menu reste dans l'écran.

**5. Pouvoirs en haut à droite**, sous la barre du haut, au-dessus de la carte. Leur taille s'adapte (34 à 46 px).
- **Règle pour les cartes futures** : garder libre le coin haut droit, c'est-à-dire la bande au-dessus de la rangée 0, colonnes 5 à 8. Aucune entrée de route ni aucun emplacement à cet endroit.

**6. Fin de partie** : PV des hordes multipliés en fin de partie (`lateMult`).

| Difficulté | Base | Montée en fin de partie | Effet |
|---|---|---|---|
| Normal | 0,72 | +3,5 % par vague après la vague 19 | x1,21 à la vague 25, x1,32 à la vague 28 |
| Cauchemar | 0,79 | +5 % par vague après la vague 12 | x1,15 à la vague 15, x1,40 à la vague 20, x1,65 à la vague 25 |
| Facile | 0,35 | aucune | |

Calibrage (joueurs automatiques) :
- **Normal**, achats à chaque vague : 2 parties sur 2 tenues jusqu'à la vague 28. Chacune a 6 vagues à plus de 20 % de pertes à partir de la vague 18, avec des pics à 41 et 48 %.
- **Normal**, achats toutes les 2 vagues : balayé aux vagues 11 et 12.
- Un essai à 0,74 avec la montée dès la vague 18 faisait tomber le joueur automatique fort aux vagues 22 et 25. C'était plus qu'une hausse légère, donc ce réglage n'a pas été retenu.
- **Cauchemar**, achats à chaque vague : éliminé aux vagues 17 et 19.
- **Facile**, achats toutes les 2 vagues : victoire, sans perte ou presque.

**7. Fiche du bas.** Fermée, la fiche glissait sous la carte et recouvrait la barre du bas. C'est corrigé : la scène découpe ce qui dépasse, et la fiche est masquée une fois fermée. On peut aussi la fermer en glissant son en-tête vers le bas.

**8. Barre de commande** (inspirée de Kingdom Rush, de Bloons TD 6 et des barres de navigation de Clash Royale) :
- **Deux onglets à icône** à gauche, l'Arbre (nœuds reliés) et le Bastion (tour crénelée). L'onglet actif est souligné et coloré en kaki. L'Arbre porte une pastille (nombre de déblocages abordables) ou un cadenas pendant la vague.
- **Un grand bouton d'action** à droite, sous le pouce, avec le crâne des entrées de route. Il a quatre états :
  - « Lancer la vague / Vague 7 », kaki, avec un léger battement (supprimé si l'appareil demande moins d'animations) ;
  - « Dernière vague » avant la victoire ;
  - pendant la vague, « Vague 7 en cours / 95 zombies restants », sombre, avec le remplissage de la progression ;
  - « Appeler maintenant / Vague 8 · +prime », orange.
- **Sélecteur d'arme de l'homme** : un bouton en bas à gauche (icône, nom, munitions en direct ou « rechargement... »). Il déroule vers le haut la liste des armes possédées avec leurs caractéristiques, l'arme équipée en kaki. Un appui sur la carte referme la liste.

## 2 decies. Changement V2.2 : réalisme des armes et difficulté relevée

**Retour de Renan** : victoire en Normal sans qu'un seul zombie atteigne le bastion, avec 72 508 d'argent non dépensé à la vague 28. Les trois pouvoirs, en bas à droite, cachaient l'arrivée des routes Sud et Est, et leurs libellés n'apportaient rien. La réparation d'urgence pouvait être déclenchée à pleins PV, ce qui gaspillait son temps de recharge.

**Diagnostic** (partie simulée) : le joueur automatique avait tout acheté vers la vague 24 ; au-delà, l'argent ne servait plus à rien. Les tours tiraient sans interruption. Le calibrage V2.1 supposait un humain moins fort que les joueurs automatiques : c'était faux pour un bon joueur.

**Choix de Renan** : les trois mécaniques réalistes, une « victoire exigeante » en Normal (un bon joueur gagne, mais perd des PV sur les vagues de boss et peut échouer une partie sur trois), et pour l'économie, des revenus plus bas ainsi que de nouvelles dépenses.

**1. Chargeurs de l'homme.** Capacités réelles et temps de rechargement :

| Arme | Chargeur | Rechargement |
|---|---|---|
| Pistolet 9 mm | 17 | 1,6 s |
| PM 9 mm | 30 | 2,2 s |
| Fusil à pompe | 7 | 3,5 s |
| Fusil d'assaut | 30 | 2,4 s |
| Fusil 7,62 | 20 | 2,8 s |
| Mitrailleuse légère | bande de 200 | 6 s |
| Lance-grenades | 6 | 4 s |
| Anti-matériel | 7 | 3,5 s |
| Lance-flammes dorsal | réservoir de 8 s | 6 s |
| Sans recul, thermobarique | 1 | 2,4 s, 3 s |

- Pendant une accalmie (aucune cible depuis 0,8 s), l'homme recharge de lui-même si son chargeur est entamé de plus de 30 %.
- Une quatrième amélioration par arme, « Chargeur » (« Réservoir » pour le M2) : chargeur +50 % et rechargement 35 % plus rapide.
- Sur la carte, une barre au-dessus de l'homme montre les munitions, et un cercle doré la progression du rechargement.

**2. Surchauffe des mitrailleuses.**
- Les nids de mitrailleuse surchauffent après 60 tirs pour la mitrailleuse 7,62, 100 pour la jumelée et 70 pour la lourde 12,7. Le canon est alors changé en 4 s, ou 5 s pour la 12,7.
- Le canon refroidit dès que la tour cesse de tirer.
- Achat par tour, dans son menu : « Canon de rechange » (300). La surchauffe survient 40 % plus tard et le canon est changé deux fois plus vite.
- La mitrailleuse 12,7 du bastion suit la même règle : 80 tirs, puis 5 s d'arrêt.
- Sur la carte, une barre de chaleur s'affiche sous la tour, puis un cercle rouge pendant le changement de canon.

**3. Réservoirs des lance-flammes.**
- Les tours lance-flammes tirent 7, 8 ou 9 s selon le niveau, puis remplissent leur réservoir en 5 à 5,5 s. Elles se rechargent à moitié vitesse quand elles ne tirent pas.
- Achat par tour : « Réservoir supplémentaire » (450), réservoir +50 % et remplissage 35 % plus rapide.
- Le lance-flammes fixe du bastion tire 9 s, puis s'arrête 5 s.
- Entre deux vagues, tout est rechargé, refroidi et rempli.

Ces trois règles réduisent la puissance de feu réelle de 20 à 50 % selon l'arme (le lance-grenades est le plus touché). Elles créent surtout des trous dans le tir pendant les grosses hordes : alterner les types de tours et les placer pour qu'elles se relaient devient utile. La puissance de feu affichée en tient compte, et le repère conseillé a été abaissé en conséquence (x0,8).

**4. Économie.** Les revenus sont réduits de 8 % au début, puis de 28 % dès la vague 19 (`eco(w)`). Cela vaut pour les primes par zombie, la prime de fin de vague, l'appel anticipé et la carte Ravitaillement. Le début de partie reste abordable, et la fin n'est plus saturée. En simulation, le joueur automatique dépense désormais tout son argent jusqu'à la vague 24.

**5. Difficulté** (PV des hordes) :

| Difficulté | Avant | Après |
|---|---|---|
| Facile | 0,38 | 0,35 |
| Normal | 0,63 | 0,72 |
| Cauchemar | 0,76 | 0,79 |

**Calibrage V2.2** (joueurs automatiques, jusqu'à la vague 26) :
- **Normal**, achats à chaque vague, profils équilibré et Génie : 4 victoires sur 4. Chaque partie a des pertes sur 8 à 12 vagues, dont 2 à 5 vagues à plus de 20 %, avec un pic de 78 %.
- **Normal**, achats toutes les 2 vagues : 1 victoire sur 2 (avec 7 vagues à plus de 20 %). L'autre partie a été balayée en une vague, à la vague 11.
- **Cauchemar**, achats à chaque vague : 1 victoire sur 2. L'autre partie a été balayée à la vague 13.
- **Facile**, achats toutes les 2 vagues, mesuré à 0,38 avant la baisse à 0,35 : victoire sans perte pour le profil équilibré. Le profil Génie tombe à la vague 24.
- Le directeur de vagues amortit encore : il baisse jusqu'à 15 % les PV des vagues suivantes quand le bastion souffre. Un très bon joueur ne profite pas de cet amortissement.

**6. Interface.**
- Les pouvoirs forment une colonne à gauche, entre les routes Nord et Sud : cette zone n'a ni chemin ni emplacement. Ils n'ont plus de libellé, seul le nom reste lu par les lecteurs d'écran.
- La réparation d'urgence est grisée et refuse de partir quand le bastion est à pleins PV.

## 2 nonies. Changement V2.1 : donner envie de rejouer

**Demande de Renan** : rendre le jeu plus « addictif », avec des leviers appuyés sur des études. Les cinq leviers proposés ont été ajoutés.

**Garde-fous retenus.** Aucune série quotidienne punie si on la rate, aucun compte à rebours pour créer la peur de manquer, aucune jauge d'énergie, aucun tirage payant. Les messages de « presque gagné » n'affichent que des faits réels de la partie. Le but est l'envie de rejouer, pas la compulsion.

**1. Une partie plus courte : victoire à la vague 25.** Une victoire à 40 vagues demandait une longue session ; une partie courte se relance plus facilement.
- Chaque vague vaut 1,6 vague de l'ancienne échelle (`eff(w) = 1 + 1,6 (w - 1)`). La difficulté, la taille des hordes, les dégâts et les revenus suivent cette échelle : la vague 25 correspond à l'ancienne vague 39.
- Routes : Sud à la vague 4, Est à la vague 9. Abomination toutes les 6 vagues (une de plus toutes les 6). Brutes toutes les 3 vagues à partir de la vague 9. Carte de ravitaillement toutes les 3 vagues.
- Nouveaux records (les anciens étaient comptés sur 40 vagues).

**2. Un écran de défaite qui apprend quelque chose et relance.**
- Ce qui a percé : la route qui a fait le plus de dégâts au bastion (en %) et les deux types de zombies en cause, avec une piste concrète selon le type.
- Les « presque » réels : nouveau record, écart avec le record s'il est de 3 vagues ou moins, vagues restantes avant la victoire s'il en reste 5 ou moins, PV restants de l'Abomination s'il lui en restait moins de 35 %.
- Le prochain avantage du Quartier général et le nombre d'insignes qui manquent (effet de gradient vers le but).
- « Rejouer » en un appui avec les mêmes réglages ; « Difficulté », « Quartier général » et « Partager » à côté.

**3. Niveaux de menace (sur le modèle de l'Ascension de Slay the Spire et de la Chaleur de Hades).** Cinq contraintes cumulées, débloquées une à une en gagnant au niveau précédent (Normal ou Cauchemar) :

| Niveau | Contrainte |
|---|---|
| 1 | zombies 10 % plus rapides |
| 2 | PV du bastion -15 % |
| 3 | argent -10 % |
| 4 | PV des hordes +12 % |
| 5 | une Abomination de plus à chaque vague de boss |

Insignes : +25 % par niveau. Le niveau se choisit sur l'écran de difficulté ; les records gardent le plus haut niveau gagné.

**4. Dix défis secondaires**, récompensés une seule fois en insignes (5 à 20) : gagner en Normal, en Cauchemar, sans tireur d'élite, sans réparer le bastion, en Menace 3 ; achever une Abomination au lance-flammes ; appeler 10 vagues en avance ; tenir 8 vagues de suite sans perdre un PV ; atteindre la vague 8 avec le seul pistolet ; terminer un défi du jour. Ils donnent des buts variés et poussent à essayer d'autres façons de jouer (théorie de l'autodétermination : sentiment de compétence et d'autonomie).

**5. Défi du jour.** La même partie pour tout le monde le même jour : composition des vagues tirée d'une graine liée à la date, une contrainte du jour parmi cinq (zombies rapides, bastion fragile, disette, grande meute, nuit des Abominations), difficulté Normal, sans les avantages du Quartier général. Le meilleur résultat du jour est conservé ; le bouton « Partager » envoie le résultat (partage du téléphone, sinon copie). Rater un jour ne fait rien perdre.

**Calibrage V2.1** (joueurs automatiques, 2 parties par cas, jusqu'à la vague 30) :
- Normal, achats à chaque vague ou toutes les 2-3 vagues, profils équilibré et Génie : 6 victoires sur 7. En mode infini, morts aux vagues 36 et 40, ou survie jusqu'à la fin du test (40 ou 45). C'est proche de la V2.0 (morts entre 59 et 70 sur l'ancienne échelle, soit 37 à 44 sur la nouvelle).
- Effondrements possibles avant la victoire, comme avant : Génie avec achats toutes les 3 vagues (vague 18), Armes d'abord (vague 16).
- Cauchemar, achats toutes les 2 vagues : 4 victoires sur 4, avec 2 à 5 vagues à plus de 20 % de pertes.
- Menace 5 en Normal, achats toutes les 2 vagues : profil équilibré 2 victoires sur 2 (jusqu'à 43 % de pertes sur une vague) ; profil Génie mort 2 fois à la vague 12, la vague à 3 Abominations.
- Limite : les joueurs automatiques dépensent mieux qu'un humain. Les retours de partie restent nécessaires pour régler la menace et le Cauchemar.

## 2 octies. Changement V2.0 : transitions de phase et ergonomie

**Retour de Renan** : l'évolution était éparpillée entre la carte, le panneau et l'arbre. La carte rétrécissait puis grandissait à chaque ouverture du panneau. Le menu n'était pas pratique.

**Références consultées** :
- les deux phases nettes du genre (préparation, puis vague) ;
- les vagues lancées par le joueur et le choix d'un bonus parmi 3 (Orcs Must Die Deathtrap) ;
- le démarrage automatique optionnel (Bloons TD 6) ;
- l'icône de vague à l'entrée de la route, qui montre la composition et permet l'appel anticipé (Kingdom Rush) ;
- l'information à la demande, avec les détails dans des menus contextuels (Defender's Quest, Lars Doucet) ;
- des transitions courtes et marquées.

**Règle retenue : l'arbre débloque, la carte améliore.**

- **Carte stable.** Le panneau devient une feuille qui glisse par-dessus la carte : l'échelle du plan ne change plus jamais. La barre d'armes passe aussi en surimpression. Un appui sur la carte referme la feuille.
- **Fin de vague.** Une bannière centrale (« Vague 12 repoussée », prime, état du bastion) s'affiche pendant 1,9 s. Un bilan compact reste en haut : abattus, PV perdus, alerte si la puissance de feu est sous le repère. Le bouton « Bilan » ouvre le détail ; le panneau ne s'ouvre plus tout seul.
- **Préparation.**
  - Un marqueur à tête de mort se place à l'entrée de chaque route attaquée par la prochaine vague, avec le nombre de zombies. Le toucher affiche la composition de cette route et un bouton « Lancer la vague ».
  - Un badge sur l'onglet Arbre compte les déblocages à portée de bourse.
- **Bastion.** L'onglet Construire devient Bastion. Le toucher, ou toucher le bastion sur la carte, ouvre une fiche qui regroupe :
  - les PV et la réparation ;
  - l'étage suivant, achetable directement s'il est disponible ;
  - l'arme de l'homme et ses trois améliorations ;
  - les modules et bonus à installer ou améliorer ;
  - les cartes de la partie.

  Les achats faits ici sont les mêmes que dans l'arbre.
- **Lancement.** Une bannière rouge (« Vague 13 », nombre de zombies, routes, ouverture d'une route, Abomination), puis 2 s d'approche avant les premiers zombies.
- **Pendant la vague.** Une barre de progression en haut de la carte. Dès que tous les zombies sont sortis, les marqueurs passent à l'orange avec la prime d'appel anticipé : les toucher permet d'appeler la vague suivante.

## 2 septies. Changement V1.9 : portées revues

Constat de Renan : avec l'aperçu de portée, le mortier couvrait presque toute la carte, et le tireur d'élite aussi. Le placement ne comptait plus. Une échelle réaliste au mètre près est impossible : la carte ne représente que quelques centaines de mètres, et un mortier de 120 mm porte à plus de 7 km. Les portées sont donc fixées par paliers, dans le bon ordre, pour que chaque emplacement couvre une partie seulement des routes. Une case vaut 40 unités ; la carte fait 9 × 13 cases et le bastion est au centre.

| Palier | Portée | Défenses |
|---|---|---|
| Contact | 1,5 à 2 cases | lance-flammes (tour, module, M2), couronne de barbelés, Claymore |
| Courte | 2,5 à 3 cases | nid de mitrailleuse, pistolets, PM 9 mm, fusil à pompe |
| Moyenne | 3,5 à 4,5 cases | mortier (avec zone morte de 1,4 à 1,6 case), mitrailleuse 12,7 du bastion, fusil d'assaut, mitrailleuse légère, lance-grenades |
| Longue | 4 à 5,5 cases | tireur d'élite, fusil 7,62, sans recul, thermobarique, lance-roquettes du bastion, anti-matériel (la plus longue : 5,5 cases) |

| Élément | Avant (unités) | Après (unités) |
|---|---|---|
| Tireur d'élite niv. 1 à 3 | 210 / 250 / 330 | 160 / 180 / 200 |
| Mortier niv. 1 à 3 | 230 / 260 / 300, zone morte 45 à 50 | 150 / 165 / 180, zone morte 55 / 60 / 65 |
| Nid de mitrailleuse niv. 1 à 3 | 105 / 115 / 135 | 100 / 110 / 120 |
| Tour lance-flammes niv. 1 à 3 | 62 / 72 / 82 | 60 / 68 / 76 |
| Lance-roquettes du bastion | 260 / 280 / 300 | 170 / 185 / 200 |
| Mitrailleuse 12,7 du bastion | 160 / 170 / 185 | 140 / 150 / 160 |
| Claymore | 95 / 100 / 110 | 80 / 85 / 90 |
| Anti-matériel (homme) | 420 | 220 |
| Fusil 7,62, sans recul, thermobarique (homme) | 260, 270, 290 | 190, 190, 200 |
| Lance-grenades, mitrailleuse légère, fusil d'assaut (homme) | 210, 180, 175 | 170, 165, 160 |

Le fusil à pompe garde 110 : il doit atteindre le Cracheur, qui crache à 100 du bastion.

**Recalibrage.** Les tours engagent moins longtemps, donc défendent moins. En simulation, le joueur automatique « achats toutes les 3 vagues » tombait 3 fois sur 6 avant la vague 40 (vagues 21, 21 et 30). Les PV des hordes ont donc été baissés de 16 % dans les trois difficultés. Résultat en Normal, sur 4 parties : fin entre les vagues 59 et 70, avec des pertes dans 7 à 11 vagues sur 40, comme en V1.6.

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
| Facile | × 0,38 (× 0,45 avant la V1.9) | × 0,85 | × 1,1 |
| Normal | × 0,63 (× 0,7 en V1.5, × 0,75 en V1.6) | × 1 | × 1 |
| Cauchemar | × 0,76 (× 0,85 en V1.5, × 0,9 en V1.6) | × 1,2 | × 0,95 |

**Victoire à la vague 25** depuis la V2.1 (40 auparavant), puis mode infini au choix. Records conservés par difficulté, avec la mention de victoire et le niveau de menace. Le compteur affiche « 12/25 ».

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

**Principe.** Au départ, un seul achat possible : le **nid de mitrailleuse**. L'homme a seulement son pistolet 9 mm et des sacs de sable. Chaque nœud acheté ouvre ses enfants, et le joueur part dans la direction de son choix. À chaque étape, deux options :
- **Ouvrir** un nouvel élément : arme, tour, piège, niveau de bunker ou module.
- **Améliorer** un élément déjà acquis, à l'intérieur de son nœud : cadence, puissance et zone pour une arme ; niveaux 2 et 3 pour une défense ou un module.

**Quatre directions** partent du nid de mitrailleuse :

| Direction | Contenu | Enchaînement |
|---|---|---|
| Tir | Les 11 armes de l'homme | pistolet → PM 9 mm → fusil à pompe (puis lance-grenades, lance-flammes dorsal) ou fusil d'assaut (puis fusil 7,62, mitrailleuse légère) → sans recul, anti-matériel, thermobarique |
| Appui | Tours | Tireur d'élite → Mortier ; Tour lance-flammes |
| Génie | Pièges | Barbelés → Mine, Fossé |
| Fortification | Bunker et modules | Double muret → HESCO → Casemate → Blindé, avec un module greffé à chaque palier |

**Croisements entre directions.** Les nœuds avancés exigent une autre direction : c'est ce qui crée des parcours différents d'une partie à l'autre.

| Nœud | Exige aussi |
|---|---|
| Lance-grenades | Mine |
| Canon sans recul | Mortier |
| Lance-roquettes thermobarique | Lance-flammes dorsal |
| Lance-flammes dorsal | Tour lance-flammes |
| Fusil anti-matériel | Tireur d'élite |
| Mitrailleuse légère | Nid de mitrailleuse niveau 2 |
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

L'ordre suit la puissance. Chaque arme se débloque après la précédente. L'homme ne porte qu'une arme active à la fois et en change d'un appui. Le choix compte : fusil à pompe contre les foules proches, anti-matériel contre les blindés, etc.

| # | Arme | Référence réelle | Rôle dans le jeu |
|---|---|---|---|
| 1 | Pistolet 9 mm | Arme de poing réglementaire | Arme de départ |
| 2 | Pistolet-mitrailleur 9 mm | Pistolet-mitrailleur 9 mm des unités d'intervention | Cadence élevée, courte portée |
| 3 | Fusil de combat calibre 12 | Fusil à pompe semi-automatique | 6 plombs en éventail, dévastateur de près |
| 4 | Fusil d'assaut 5,56 mm | Fusil d'assaut réglementaire | Polyvalent |
| 5 | Fusil de précision 7,62 mm | Fusil de précision semi-automatique 7,62 mm | Perforant (traverse 2 cibles) |
| 6 | Mitrailleuse légère 5,56 mm | Mitrailleuse légère 5,56 mm | Arrosage continu |
| 7 | Lance-grenades 40 mm | Revolver 40 mm à 6 coups | Dégâts de zone |
| 8 | Fusil anti-matériel 12,7 mm | Fusil de très longue portée | Ignore l'essentiel de l'armure, traverse 4 cibles |
| 9 | Lance-flammes dorsal | Lance-flammes dorsal (portée efficace d'environ 20 m) | Cône de feu, brûlure, très courte portée |
| 10 | Canon sans recul 84 mm | Arme multirôle | Gros dégâts de zone |
| 11 | Lance-roquettes thermobarique | Lance-roquettes thermobarique portable | Arme finale, souffle énorme et brûlure |

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
| Mitrailleuse lourde 12,7 | niv. 3 | Tourelle automatique |
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
| Nid de mitrailleuse | Mitrailleuse 7,62 | Mitrailleuse jumelée | Mitrailleuse lourde 12,7 |
| Tireur d'élite | Fusil de précision | Fusil de précision semi-auto | Fusil anti-matériel (perce l'armure) |
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
| Blindé (ex-forces de l'ordre) | vague 8 | Armure élevée | Anti-matériel, tireur d'élite, explosifs |
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
- **Lance-roquettes thermobarique en arme finale** : c'est l'arme portable non guidée au souffle le plus puissant de la liste. Un missile guidé aurait moins de sens contre des zombies.

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
- Armure du Blindé ramenée de 6 à 4 : le PM 9 mm ne lui faisait plus qu'un point de dégât par balle.
- La pente des points de vie se durcit après la vague 15 : en simulation, le jeu devenait trop facile en fin de partie, avec 180 000 d'argent en banque à la vague 60.

Version 1 :

Ajustements faits après simulation :
- Bouffi moins destructeur au contact.
- Cracheur à portée de toutes les armes sauf le lance-flammes : le fusil à pompe ne pouvait plus l'atteindre.
- Premier boss allégé : il était quasi invulnérable aux armes disponibles à la vague 10.
- Brute un peu moins résistante.

## 11. Pistes V2

- Rogue-lite hybride : fait en V1.6.
- Leviers de rejouabilité (partie courte, défaite utile, menace, défis, défi du jour) : faits en V2.1.
- Munitions et rechargement : faits en V2.2 (chargeurs de l'homme, surchauffe, réservoirs).

- Défenses qui s'usent : barbelés arrachés par les brutes, à réparer.
- Autres cartes, avec des chemins différents.
- Graphismes en sprites et sons plus riches.
- Classement des meilleurs scores.

---

## Sources (modèles réels ayant servi de référence ; le jeu n'utilise que des noms génériques depuis la V2.7)

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
