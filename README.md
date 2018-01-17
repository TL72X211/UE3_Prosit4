# UE3_Prosit4 - Graphes et algorithmes de routage

## Team
 * Animateur - Nicolas
 * Secrétaire - Hugo
 * Scribe - Fantou
 * Gestionnaire - Maxou

## Mots-Clés
 * Congestion : 
 * Algorithme de Dijkstra : 
 * Routage Dynamique : 
 * Théorie des graphs : 
 * Algorithme distribué (à état de lien) : 
 * Protocole RIP : 
 * Protocole Propriétaire : 
 * Protocole Ouvert : 
 * Algorithme de type vercteur de distance : 
 * Matrice : 
 * Authentification : 
 * Coût : 
 * Nombre de saut : 
 * Arretes et sommets : 

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

 * Protocole dynamique (RIP, OSPF, IGRP, EIGRP)

 * Métriques utilisés pour évaluer les routes

 * Théorie des graphes :
 Un graphe est constitué de sommets et d'arrêtes reliant les sommets entre eux, les rendant adjacent. Les arêtes peuvent également former des **boucles** en partant d'un somment pour y retourner.  
 Si les arêtes n'ont pas d'orientation, le graphe est dit non orienté. A contrario, si les arêtes ont un sens, le graphe est dit orienté.  
 L'ordre d'un graphe, cela correspond à son nombre de sommets.  
 Le degré d'un sommet, cela correspond au nombre d'arrete dont il est l'extrémité. (**Attention**, dans les boucles, le sommet est l'extrémité du départ de la boucle et de son arrivé, son degré augmente donc de deux et non pas d'un)  
 Pour qu'un graphe soit dit "**complet**" : Si c'est un graphe non orienté, chaque couple de sommets doit être relié par **une** arête unique et ne pas contenir de boucle. Si c'est un graphe orienté, tous les sommets doivent être adjacents et il ne doit pas y avoir de boucle.
 Un sous graphe c'est un sous ensemble de sommet ainsi que les arêtes les reliants (y compris les boucles)
 ![Base des graphes](imgFantou/graphe_base.png)

  * Algorithme destribué à l'état de lien

  * Dijkstra

 * Implémentation des protocoles chez Cisco

 * Distances administratives

### Réalisation
 * Matrice pour trouver le meilleur chemin
 * Workshop
 * Corbeille (*Avec Kim*)
 * *Facultatif : Dijkstra en Java*