Loop Hero — Jeu Unreal Engine 5

Concept

Plateau type Loop Hero en 3D où on joue un personnage Third Person qui se déplace de case en case sur un circuit en boucle. Selon la case sur laquelle on tombe, il se passe différents trucs : gain de ressources, perte de vie, dialogues, ou déclenchement d'un mini-jeu. Le jeu inclut deux mini-jeux qui font gagner ou perdre de l'or.
Le plateau
Le circuit est composé de cases en boucle. On lance un dé (Random Integer in Range entre 1 et 6) et le pion avance du nombre correspondant. À chaque case on déclenche un Custom Event "DispatchVerifCase" qui prend en paramètre le type de la case en string, et un Switch on String oriente vers la bonne logique :

Artifact : on récupère un artéfact, le compteur Artifacts augmente. Quand il atteint 3, on déclenche une victoire avec un Widget WVictory.
Ruine : on gagne des ressources (de l'or)
Combat : ouvre le niveau du cache-cache
Dialog : affiche un widget Visit Dialog Case avec un texte narratif
Default : ouvre le niveau du Pierre Feuille Ciseaux

À chaque tour, après le déplacement, on vérifie les conditions de victoire (3 artéfacts récupérés) et de défaite (ressources à 0). Si les ressources tombent en négatif, le widget WDefaut s'affiche et la partie est terminée.
Le jeu tourne en boucle : à chaque passage sur la case de départ, le joueur gagne un bonus et la difficulté monte un peu.
Mini-jeu n°1 : cache-cache
Quand le pion atterrit sur une case Combat, un Open Level charge le niveau HideSeek. Le joueur arrive avec son ThirdPersonCharacter dans une zone fermée où patrouille BP_ArcherIA, une IA armée d'un arc qui cherche à attraper le joueur.
Le but est d'atteindre une zone verte (BP_SafeZone) avant de se faire toucher. La SafeZone est un Box Collision en trigger : quand le ThirdPersonCharacter rentre dedans, le widget de victoire s'affiche.
L'IA fonctionne avec deux events principaux :

Patrol (Custom Event déclenché au BeginPlay) : règle la vitesse à 250, prend un point aléatoire avec Get Random Reachable Point in Radius (rayon 1000), puis fait un AI MoveTo vers ce point. Un Delay de 0.5s rappelle Patrol après l'arrivée pour boucler.
OnSeePawn (component PawnSensing) : déclenche un Sequence. Then 0 cast vers le ThirdPersonCharacter, monte la vitesse à 1000 et fait un AI MoveTo direct sur le joueur. Then 1 utilise un Retriggerable Delay de 1 seconde qui rappelle Patrol seulement si l'IA arrête de voir le joueur.

Le Retriggerable Delay est central dans ce système : tant que l'IA voit le joueur, le délai se reset à chaque détection donc Patrol n'est jamais rappelé. Dès qu'elle perd le joueur 1 seconde sans interruption, le délai se finit et l'IA repart en patrouille.
Une Sphere Collision attachée à l'IA détecte quand elle attrape le joueur : ActorBeginOverlap → Cast vers ThirdPersonCharacter → widget de défaite.
Conditions :

Victoire : atteindre la zone verte → +50 or
Défaite : se faire toucher par l'IA → -50 or

À la fin, un widget plein écran (WBP_HideSeekResult) affiche le résultat avec un bouton "Retour au plateau" qui fait un Open Level vers L_MainMap.
Mini-jeu n°2 : Pierre Feuille Ciseaux
Quand le pion atterrit sur la case mini-jeu RPS, un Open Level charge le niveau dédié et un Widget Blueprint (WBP_RPS) s'affiche en plein écran avec trois boutons.
Fonctionnement :

Le joueur clique sur Pierre, Feuille ou Ciseaux
L'IA choisit au hasard avec Random Integer in Range (0, 2)
On compare avec les règles classiques. La formule (PlayerChoice + 1) % 3 == AIChoice détermine si le joueur gagne, sinon l'IA gagne (sauf en cas d'égalité où la manche ne compte pas)
Le score se met à jour en temps réel à l'écran

La partie se joue en 3 manches gagnantes. Quand l'un des deux atteint 3, les boutons sont désactivés (Set Is Enabled = false) pour pas continuer après la fin, et un bouton Retour apparaît.
Toute la logique tourne dans une fonction PlayRound qui prend en paramètre le choix du joueur (Integer 0, 1 ou 2). Les trois boutons appellent cette même fonction avec leur valeur respective.
Conditions :

Victoire : 3 manches gagnées avant l'IA → +50 or
Défaite : l'IA gagne 3 manches en premier → -50 or

Lien entre les mini-jeux et le jeu principal
La persistance entre les niveaux est gérée par un Game Instance (BP_GameInstance). Ce Blueprint stocke l'or du joueur, sa position sur le plateau (LastTilePosition), et le résultat du dernier mini-jeu (MiniGameWon).
Quand on entre dans un mini-jeu, on sauvegarde la position du pion dans LastTilePosition avant d'ouvrir le niveau. Quand on revient, le Level Blueprint du plateau lit le Game Instance, applique le bonus ou le malus avec ApplyMiniGameResult, et téléporte le pion à sa position d'avant.
Sans le Game Instance, l'or et la position seraient perdus à chaque Open Level puisqu'Unreal recharge tout. C'est pour ça qu'on l'a configuré dans Project Settings → Maps & Modes → Game Instance Class.
Architecture des Blueprints
Le jeu est presque entièrement codé dans le Blueprint du Player et le Level Blueprint du plateau. La logique principale tourne autour de trois Custom Events :

DispatchVerifCase : reçoit le type de la case et fait le Switch on String
Avancer : déplace le pion d'une case à la fois avec un Sequence et des Set Actor Location, jusqu'à ce que DeplacementsRestants tombe à 0
RollDe : génère le nombre aléatoire et stocke dans DeplacementsRestants

À l'arrivée sur la case finale, le Switch on String déclenche soit un Open Level (pour les mini-jeux), soit un Create Widget (pour les dialogues, victoires, défaites).
Niveaux

L_MainMap : plateau principal Loop Hero avec le ThirdPersonCharacter
L_HideSeek : mini-jeu cache-cache avec BP_ArcherIA et BP_SafeZone
L_RPS : mini-jeu Pierre Feuille Ciseaux (essentiellement un widget plein écran)
