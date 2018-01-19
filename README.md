
# UE3_Prosit4 - Graphes et algorithmes de routage

## Team
 * Animateur - Nicolas
 * Secrétaire - Hugo
 * Scribe - Fantou
 * Gestionnaire - Maxou

## Mots-Clés
 * Congestion : Embouteillage
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
  * Algorithme destribué à l'état de lien
  * Dijkstra
 * Implémentation des protocoles chez Cisco
 * Distances administratives

### Réalisation
 * Matrice pour trouver le meilleur chemin
 * Workshop
 * Corbeille (*Avec Kim*)
 * *Facultatif : Dijkstra en Java*
 
## 1 - Routage dynamique RIP - Protocole à vecteur de distance + Belman Ford

RIP est un protocole qui permet de router dynamiquement son réseau, c'est à dire faire transiter les informations de routages à travers les routeurs. 

On retrouve **RIP** et **IGRP** comme étant représentatifs. 

**EIGRP** est aussi un protocole à vecteur de distance optimisé par cisco, qui présente moins de désavantages.


**Qu'est ce que le RIP ? :**

Il est disponible en trois versions :
- RIPv1 (1988) : Broadcast, classfull, UDP520
- RIPv2(1993,1998) : Multicast 224.0.0.9, authentification, classless, UDP520
- RIPng : Support IPV6, authentification IPSEC, multicast FF02::9, UDP 521

**Mises à jour RIP**
Les mises à jours s'éffectuent de routeurs en routeurs périodiquement (toutes les trente secondes).
Le principe consiste à envoyer les tables de routages **entières**
- MAJ envoyés à l'adresse de diffusion 255.255.255.555 en RIPv1
- MAJ envoyés à l'adresse de Multicast 224.0.0.6 en RIPv2

**Métrique RIP**

RIP utilise l'algorithme de Bellman Fort pour calculer les meilleures routes.
- La distance administrative de RIP est de 120 par défaut
- La métrique est basée sur le nombre de sauts (Un saut = nombre de routeurs à parcourir)
- La métrique base maximale est 15
- La métrique infinie est 16 : Empoisonne une route.

**Vecteur de distance**

![](https://www.lucidchart.com/publicSegments/view/8fc33029-8b59-45d3-8e5a-71b1c4ada82a/image.png)

Dans cet exemple, chacun ajoute une distance de +1 , et donc la métrique s'implémente.

**Boucle de routage**

Une boucle de routage est une route pour que les paquets **n'arrivent jamais à leur destination**, sont **répétés** sur le même noeud du réseau. Ce phénomène est dû à une convergence lente des informations de routage.

*Un routeur éloigné fait croire à des routeurs (bien informés d'une route modifiée) qu'il dispose d'une nouvelle route (à coût plus élevé) vers ce réseau.*

**Solutions aux boucles de routages :**
- Définir un nombre maximum de saut (15 max) ou métrique infinie (16)
- Route poisoning : Lorsqu'une rouse tombe, le réseau doit-être immédiatement averti d'une métrique de distance infinie (le maximum de sauts +1), plus aucune incrémentation n'est possible.
- Split horizon : Split horizon empêche à un routeur d'envoyer des informations (de métrique plus élevée) à travers l'interface de laquelle elle a pris l'information.
- Triggered Update : Une MAJ est envoyé immédiatement plutôt qu'avant l'expiration du compteur lorsqu'une route est tombée, à l'aide de la métrique infinie. Tout les routeurs seront alors immédiatement avertis.
- Split Horizon avec Poison Reverse ; Le protocole de routage avertit de toutes les routes sortant d'une interface mais celles qui ont été apprises d'une mise à jour plus récente venant dans cette interface sont marquées d'une métrique de distance infinie.

**Timer RIP**

- **Update** : délai de MAJ = 30s par défaut.
- **Invalid** : Si pas d'annonce pendant un certain délai -> Marquée d'une métrique infinie, 180s par défaut.
- **Flush :** délai après lequel une route est retirée de la table de routage (240s par défaut, toujours > Invalid)
- **Holddown :** Délai d'attente après lequel une **route à métrique plus élevée** est prise en compte : 180s par défaut.

**Compteur de retenue**

Cdr ou HDW (Hold Down Timer) est crée par Cisco.

Après avoir retenu qu'une route vers un réseau est tombée, il y a une période avant de "croire" n'importe quelle autre information de routage à propos de ce réseau. Il faut la réinscrire par un routeur voisin qui a appris sa réinscription.


**Bilan des temps sur un routeur, utilisés par RIP**

![](https://cisco.goffinet.org/content/images/2017/09/compteurs_rip.png)

**Les limites de RIP** 
- Sa limite de 15 pour les sauts, inutilisable sur les réseau plus grand
- VLSM est préférable à RIPv1 en vue de ces manque d'@ IP et de sa flexibilité
- Consomme une grande quantité de bande passante pour envoyer les tables de routages en entier
- Convergence assez lente où le besoin est haut
- RIP ne prend pas en compte les délai et les coups, c'est uniquement le nombre de sauts, quelque soit la bande passante.
- Pas de concept de "zone" ou de "frontières", pas de "classless", ni d'utilisation intelligente de "l'agrégation" ni de la summarization des routes.
- RIP est sensible aux boucles de routages

**Positif**
- Facile à déployer, léger
- RIPv2 supporte le VLSM et permet l'authentification et les MAJ routages mutlicast


### Comment mettre en place le RIP

https://cisco.goffinet.org/routage-dynamique-ripv2/

- 1 : Configurer les interface Gigabit vers les autres routeurs avec une IP et les activer 
- 2 :  
	- router rip: Accéder au mode de config de RIP
	- version 2
	- network [@IP des R@ connectés] : Fait de la pub pour segments réseau concernés, active la capacité des interfaces à envoyer des MAJ vers ce réseau.
- 3 : Compléments de  configuration
	- ip dhcp pool [NomLan]
	- default-router [@IP du R@]
	- passive-interface [Interface relié à la LAN] : Désactive l'envoi de messages de routages, l’interface peut toujours en accepter de manière crédule. Il est conseillé de mettre en passive toute celles qui relient vers de LAN pour éviter de se "noyer".

**Vérifier sa config**

show ip protocols
debug ip rip -> lance les logs RIP dans la console par défaut
show ip route
ping / traceroute / tracert

## OSPF - Protocole à état de lien - Dijstra

Open Shortest Path First est un protocole qui a été développé par l'IETF pour le **routage intérieur** (IGP, Internal Gateway Protocol).
Il utilise un protocole à algorithme à état de lien, plus efficace que RIP.

Au lieu d'envoyer des MAJ entière lors d'un changement topologique, OSPF envoie des mises à jour incrémentielles.
Il utilise la notion d'area (zone) pour désigner un groupe de routeurs, très pratique pour séparer plusieurs zones.
On parle de communication inter-area lorsqu'on souhaite communiquer avec une autre zone.

Compatible avec le VLSM et la  "summarization" manuelle des routes. 

Un routeur OSPF remplit un rôle et une responsabilité particulière qui dépend de la hiérarchie OSPF établie :
 - **Internal Routeur (IR) :**
Ce type de routeur doit entrenir sa BDD d'états de liens (qui est identique sur chaque IR).
- **Backbone Router (BR) :** Un BR est un routeur qui est connecté dans la zone 0 (Backbone area), la zone de base.
 - **Area Border Router (ABR) :** 
L'ABR est un routeur qui connecte au moins deux zones (dont l'area 0). Il possèdent autant de liens qu'il y a d'interface connectés aux différentes zones, regroupe les bases des données des zones et les "summarize" et une seule route IP. Ces "summarize" peuvent ensuite être distribué. C'est un système qui permet de réduire la taille des MAJ.
- **Autonomous System Boundary Router (ASBR) : **
Comme OSPF est un protocole interne, il va devoir être connecté au reste de l'internet par d'autres AS.
Ce type de routeur fera passerelle vers un ou plusieurs AS.

![](https://www.lucidchart.com/publicSegments/view/8e4d2147-50b0-4afc-9d42-6f7ac8dd082c/image.png)


**Fonctionnement d'une zone :**
Pour chaque area, on a une table à état de lien qui est construite et maintenue. A partir de cette BDD, on construit une table de routage grâce à l'algorithme de routage SPF :
- **Etape 1 : Découverte des voisins :** Un routeur doit trouver ses voisins à l'aide de paquets "Hello", dès son initialisation ou à la suite d'un changement dans la topologie, un router va générer un link-state advertismement (LSA) qui va collecter tous les états de liens de voisinage du routeur.
- **Etape 2 : Innondations & mises à jour :** 
Les états de liens vont se faire par innondation. Chaque routeur qui reçoit une MAJ (link-state update) en garde une copie dans sa BDD et le propage auprès des autres routeurs.
- **Etape 3 : Calul des routes :**
A l'aide des informations de la BDD, chacun va calculer l'arbre du chemin le plus court (Shortest Path tree) vers toutes les destinations à l'aide de l'algorithme de Dijkstra, se faisant, il crée une table de routage en choisissant les meilleurs routes à inscrire.
- **Etape 4 : maintenance des routes :**
Si pas de modifications, très discret, sinon échange d'information par paquets d'états de lien, et l'algorithme de Dijkstra recalculera les chemins les plus courts à inscrire dans la table de routage.

**Qu'est ce qu'un Etat de lien ?**
Un lien peut-être considéré comme l'interface d'un routeur. Son état est donc la description de cette interface et la relation qu'elle entretient avec ses voisins. On retrouve donc son @IP, masque, type de R@, routeurs connectés.... L'ensemble de ces états de liens forment la "link-state database", identique sur tous les routeurs d'une area.


*Exemple état de lien :*

![](https://cisco.goffinet.org/content/images/ospf-lsa-1.png)



------
**La distance administrative** est le poids administratif d'une route apprise par un protocole de rouage. Plus elle est faible, plus elle est préférée.
Les distances administratives ont une valeur par défaut. Des protocoles seront préférés à d'autres, par exemple EIGRP sera préféré à RIP, statique préférée à dynamique.

