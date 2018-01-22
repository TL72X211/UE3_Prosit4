# UE3_Prosit4 - Graphes et algorithmes de routage

## TO DO
  * Faire matrice d'adjacence du graphe du prosit
  * OSPF - Comment sont transmis les paquets

## Team
 * Animateur - Nicolas
 * Secrétaire - Hugo
 * Scribe - Fantou
 * Gestionnaire - Maxou

## Mots-Clés
 * Congestion : Condition dans laquelle une augmentation du trafic cause des ralentissement
 * Algorithme de Dijkstra : Algorithme du plus court chemin
 * Routage Dynamique : Routage réalisé automatiquement
 * Théorie des graphes : Représentation des problèmes sous forme de graphes - Branches des mathématiques
 * Algorithme distribué (à état de lien) : (OSPF / EIGRP) Protocole dont la métrique tient compte de la performance des liaisons
 * Protocole RIP : Fonctionne sur les vecteurs de distance
 * Protocole Propriétaire
 * Protocole Ouvert
 * Algorithme de type vercteur de distance : Ne connait pas tout le graphe - Compte les sauts pour arriver jusqu'à un routeur
 * Matrice d'adjacence
 * Authentification
 * Coût
 * Nombre de saut
 * Arretes et sommets

## Contexte
### Quoi ?
 * Problème de congestion

### Pourquoi ?
 * Eviter les problèmes de congestion
 * Fluidifier le trafic

### Comment ?
 * Calculer le chemin le plus court

## Contraintes
 * Matériel constructeur **Cisco**

## Problématique
 * Comment éviter les problèmes de congestion et fluidifier le trafic réseau via la théorie des graphes ?

## Généralisation
 * ~~MCO~~
 * Optimisation

## Hypothèses
 * Le chemin le plus court va réduire les congestions
 * Les différents protocoles disponibles utilisent chacun un moyen différent pour identifier la meilleur route
 * Chemin avec la plus grande bande passante
 * Différents critères pour le chemin le plus adapté
 * On peut utiliser plusieurs chemin pour un même **paquet**
 * On peut utiliser plusieurs chemin pour un même **segment**
 * Un routeur utilise les distances administratives pour sélectionner la route à utiliser
 * Parcours en profondeur
 * Dijkstra fait la sommes des poids des arretes et continu à avancer d'arrete en arrete tant que le poids des arretes parcourues est inférieure au poids total des poids des arretes précédentes

## Plan d'Action
### Etudes
 * Routage dynamique

 * Protocole dynamique (RIP, OSPF, IGRP, EIGRP) (Gilly + Emilien)
 	 * RIP : Message toutes les 30s, 15 sauts max, en v1 envoie en broadcast, en v2 envoie en multicast. Si pas de nouvelle, la route est placée avec une métrique infinie, si encore trop longtemps inactif alors "flush" et retiré de la table de routage. Limites : Consomme beaucoup de bande passante, pas de zone ni de frontière, est sensible aux boucles de routage (quand un message n'arrive jamais à destination). Converge lentement car tables envoyées toutes les 30 secondes.

 	 * OSPF : Aire, envoi de message "Hello". Routage intérieur, envoi de màj incrémentielle. Les routeurs se groupes dans des zones (area).  - BR - ABR - ASBR.  
 	 Envoi de message Hello ; Puis envoi de message DBA (DataBase) ; Les routeurs répondent avec leur base de donnée ; Le routeur demande les bouts qui lui manque (LSR) ; "Voila la donnée" (LSU) ; "J'ai bien reçu" (LSA). Pour plus de détails, voir WIKI.  
 	 S'appuie sur l'algo de Dijkstra.  
 	 Formule du calcul du métrique : (10 000 000 / plusPetiteBandePassante) + Somme(Délais)\*256  
 	 Si problème sur le réseau, le routeur remplace par la feasable distance ayant la plus petite métrique. Si il n'y en a pas, il rentre dans un processus de requête pour rechercher une nouvelle route.

 	 * IGRP : Propriétaire Cisco, 255 sauts max

 	 * EIGRP : Propriétaire Cisco, (l'EIGRP Cisco a une distance admin plus faible de EIGRP normal)

 * Métriques utilisés pour évaluer les routes
 	Permet de "choisir" la route la plus courte. Deux types principaux, le nombre de sauts et la bande passante. Les autres sont : Etat des liens, Fiabilité, Charge, Délais ...

 * Théorie des graphes :
	 * Algorithme destribué à l'état de lien : Belman-Ford, Dijkstra, A\*, Floid

	 * Dijkstra
	 Se base sur toutes les routes possible et cherche la plus courte. Cf [vidéo](https://www.youtube.com/watch?time_continue=2&v=rHylCtXtdNs). Plus l'algorithme avance, plus on avance vers le chemin le plus court. Les arrêtes doivent être pondérées (chiffrées) et si elles ne le sont pas, on met 1 sur toutes les arrêtes. Algo BFS est le plus simple, si on a des poids égaux sur chaque arrête ou pas de poids du tout.

 * Implémentation des protocoles chez Cisco
 	Voir Workshop

 * Distances administratives

 	 * Connected : 0
 	 * Statique : 1
 	 * PGP : 20
 	 * EIGRP interne : 90
 	 * IGRP : 100
 	 * OSPF : 110
 	 * ISIS : 115
 	 * RIP : 120
 	 * EGP : 140
 	 * DDR : 160
 	 * EIGRP externe : 170
 	 * BGP : 200
 	 * Inconnu : 250

### Réalisation
 * Matrice pour trouver le meilleur chemin
 * Workshop
 * Corbeille (*Avec Kim*)
 * *Facultatif : Dijkstra en Java*