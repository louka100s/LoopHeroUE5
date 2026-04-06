Le plateau principal
Le plateau est un circuit de cases en boucle. Le joueur lance un dé et avance son pion du nombre de cases correspondant. Certaines cases sont neutres, d'autres ont des effets sur les ressources (gain ou perte d'or, de points de vie). Une case spéciale, repérable visuellement, transporte le joueur vers le mini-jeu quand il atterrit dessus.
Le jeu tourne en boucle : à chaque passage par la case de départ, le joueur gagne un bonus et la difficulté augmente légèrement.
Le mini-jeu : Pierre Feuille Ciseaux
Quand le joueur arrive sur la case mini-jeu, le niveau change et un écran de jeu apparaît avec trois boutons : Pierre, Feuille et Ciseaux.
Le fonctionnement est simple :

Le joueur clique sur un des trois boutons pour faire son choix.
L'IA choisit aléatoirement entre Pierre, Feuille et Ciseaux.
Le gagnant est déterminé par les règles classiques : Pierre bat Ciseaux, Ciseaux bat Feuille, Feuille bat Pierre.
En cas d'égalité, la manche ne compte pas et le joueur rejoue.

La partie se joue en 3 manches gagnantes. Le score de chaque joueur est affiché à l'écran et se met à jour en temps réel. Quand l'un des deux atteint 3 victoires, la partie est terminée.
Résultat du mini-jeu :

Victoire si le joueur gagne 3 manches en premier) : +50 or rajoutés aux ressources du jeu principal.
Défaite si l'IA gagne 3 manches en premier) : -50 or retirés des ressources du jeu principal.

À la fin du mini-jeu, un bouton "Retour" apparaît et ramène le joueur sur le plateau principal. Les boutons Pierre/Feuille/Ciseaux sont désactivés pour éviter de continuer à jouer après la fin.
Lien entre le mini-jeu et le jeu principal
La persistance des données entre les niveaux est assurée par un Game Instance (BP_GameInstance). Ce Blueprint stocke l'or du joueur et le résultat du dernier mini-jeu. Quand le joueur quitte le mini-jeu et revient sur le plateau, le Game Instance applique le bonus ou le malus et met à jour le HUD.
Le passage vers le mini-jeu se fait par un changement de niveau (Open Level) déclenché quand le pion entre dans la zone de la case spéciale. Le retour fonctionne de la même façon en sens inverse.
Interface du mini-jeu
L'interface est entièrement gérée par un Widget Blueprint (WBP_RPS) :

Trois boutons centrés pour Pierre, Feuille et Ciseaux
Un texte de résultat qui indique "Tu gagnes !", "L'IA gagne !" ou "Egalite !" après chaque manche
Un texte de score mis à jour en direct (ex : "Toi : 2 — IA : 1")
Un bouton Retour qui n'apparaît qu'une fois la partie terminée

Contrôles

Plateau : déplacement du pion automatique selon le dé
Mini-jeu : clic souris sur les boutons Pierre, Feuille ou Ciseaux
