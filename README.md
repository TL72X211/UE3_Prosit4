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

# Plan d'Action
##Etudes

### Routage dynamique

L’idée du routage dynamique est de ne plus laisser la configuration du routage à un individu mais aux routeurs. Le problème du routage fait a la main par un individu est le temps de réaction élevé ainsi que le propension aux erreurs.
Les routeurs n’ont juste qu’à, à l’aide de protocoles de routage dynamique, identifier à quels réseaux sont connectées ses interfaces puis de notifier les routeurs voisins. 
Il existe 3 grands types de protocoles de routages dynamique :
- A état de liens (OSPF, IS-IS)
- A vecteur de distance (RIP, IGRP)
- Hybride (EIGRP)

### Protocole dynamique 

####RIP :
RIP transmet à ses voisins les adresses réseaux qu’il connait ainsi que les distances pour y accéder. Le couple d’adresse/distance est appelé « Vecteur de distance". Il diffuse les routes toutes les 30s. Il limite le nbre de sauts à 15 pour éviter que les routes bouclent. Il existe 2 versions (RIPv1 et RIPv2)

####OSPF :
Le protocole OSPF fonctionne dans une délimitation d’un réseau nommé « aire ». Tous les routeurs d’un réseau envoient des messages « hello » à leurs voisins immédiats à intervalle régulier. En réponse, chaque routeurs voisins revoient un message « Link-state advertissement » (LSA). Tout les LSA forment une base de données nommée « LSDB ». Le routeur utilise ensuite l’algorithme de Dijkstra pour calculer la route la plus rapide.

####IGRP :
C’est un protocole propriétaire CISCO. Il a été utilisé pour remplacer le protocole RIP. Il permet aux routeurs de s’échanger les tables de routages. Le nombre maximal de sauts (hops) est de 255.

####EIGRP :
Il est aussi un protocole de routage propriétaire CISCO. Il est l’amélioration du protocole IGRP. Ses optimisations sont basées sur l’algorithme « Diffusing Update Algorithm (DUAL) » qui garanti l’absence de boucles en garantissant les « sauts à l’infini ».
EIGRP calcule les métriques sur base de 4 paramètres combinés à 5 coefficients : le délai, la bande passante, la fiabilité, la charge.

### Métriques utilisés pour évaluer les routes

« Métrique : indique la valeur attribuée pour atteindre le réseau distant. Les valeurs les plus faibles indiquent les routes préférées. La mesure pour les routes statiques et les routes connectées est de 0. » CCNA2 chapitre 3

La métrique peut correspondre :
   	- Au nombre de sauts IP nécessaires pour atteindre le réseau destination, comme dans RIP ;
    	- A un coût numérique qui dépend de la bande passante des liens franchis, comme dans OSPF ;
    	- Au résultat d'un calcul plus complexe, qui tient compte de la charge, du délai, du MTU, etc.

### Théorie des graphes :
	
### Algorithme distribué à l'état de lien

Algorithme de Bellma 

Algorithme de Floyd-Warshall n-Ford

Algorithme A*


### Etudier Dijkstra

Dijkstra est un algorithme de calcul de chemin le plus court. Il est utilisé dans un graphe non orienté avec un sommet de départ et d’arrivé ainsi que des arrêtes pondérées avec des réels positifs. Il est basé sur un parcours en largeur.
On peut, par exemple s’en servir dans un graphe où les villes sont représentées par des sommets et les routes par les arrêtes pour calculer le chemin le plus court entre la ville A et la ville B. Il est souvent utilisé dans les calculs des itinéraires routiers pour calculer le chemin le plus court en fonction de la distance, de la consommation de carburant etc…
On l’utilise en informatique pour les protocoles de routage à état de liens comme OSPF (Open Shortest Path First) ou IS-IS.

### Implémentation des protocoles chez Cisco
####OSPF :
Configuration :
R1(config)#router ospf 100					(Création du num de processus)
R1(config-router)#network 10.0.3.0 0.0.0.255 area 0		(Ajout des réseaux distants)
R1(config-router)#network 10.0.0.0 0.0.0.255 area 0		(Ajout des réseaux distants)
R2(config-router)#redistribute connected			(Redistribution des routes connectés)
Ou
R1(config-router)#default-information originate		(Redistribution d'une route par défaut)

R1#show ip ospf						(Informations concernant ospf)
R1#show ip ospf neighbor					(Afficher les voisins ospf)

Authentication:

R1(config)#router ospf 100
R1(config-router)#area 0 authentication message-digest
R1(config-router)#exit
R1(config)#int fa 1/0
R1(config-if)#ip ospf message-digest-key 1 md5 passworD
R1(config-if)#int fa 2/0
R1(config-if)#ip ospf message-digest-key 1 md5 passworD

####IGRP :
R1(config)#router igrp 100					(Numéro de processus)
R1(config-router)#network 10.0.0.0				(Ajout des réseaux connectés) 
R1(config-router)#network 20.0.0.0				(Ajout des réseaux connectés) 
R1(config-router)#exit

####EIGRP :
R1(config)#router eigrp 1					(Numéro de processus)
R1(config-router)#network 10.0.0.0 0.0.0.255			(Ajout des réseaux connectés)
R1#show ip eigrp neighbors					(Information des voisins)



###Etudier les distances administratives :

« Distance administrative (AD) : identifie la fiabilité de la source de la route. L’AD pour les routes statiques équivaut à 1 et pour les routes connectées, elle équivaut à 0. Les protocoles de routage dynamique disposent d’une AD supérieure à 1 selon le protocole. » CCNA2 chapitre 3

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

Si la distance administrative est de 255, le routeur ne croit pas à la source de ce routage et n'installe pas le routage dans la table de routage

##Réalisation
 * Matrice pour trouver le meilleur chemin
 * Workshop
 * Corbeille (*Avec Kim*)
 * *Facultatif : Dijkstra en Java*