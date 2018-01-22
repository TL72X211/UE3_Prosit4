
# UE3_Prosit4 - Graphes et algorithmes de routage

## Team
 * Animateur - Nicolas
 * Secrétaire - Hugo
 * Scribe - Fantou
 * Gestionnaire - Maxou

## Mots-Clés
 * Congestion : Embouteillage
 * Algorithme de Dijkstra : /
 * Routage Dynamique : /
 * Théorie des graphs : /
 * Algorithme distribué (à état de lien) : /
 * Protocole RIP : /
 * Protocole Propriétaire : Protocole privé 
 * Protocole Ouvert :  Protocole disponible à tous 
 * Algorithme de type vercteur de distance : /
 * Matrice : /
 * Authentification : /
 * Coût : /
 * Nombre de saut : Nombre de routeurs à parcourir avant d'arriver à destination 
 * Arretes et sommets : Arrête = Liaison, sommets = intersection d'arrête

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

## 2- OSPF - Protocole à état de lien - Dikjstra

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

----


## 3 - EIGRP- Protocole à vecteur de distance avancé -DUAL

EIGRP (Enhanced Interior Gateway Routing Protocol) est développé par Cisco.
Il reprend les fondamentaux de l'OSPF qui est un "link state protocol", il fonctionne sur base d'un numéro de système autonome "Autonomous System Number" (ASN) pour dire qu'il pourra communiquer uniquement avec les routeurs qui ont le même ASN.

Lors de son activation, tout comme l'OSPF, il va découvrir ses voisins à l'aide des messages "Hello".
Lorsque deux routeurs reçoivent des "Hello" 'un de l'autre pour déterminer si ils deviennent voisins EIGRP :
- Fonctionner dans le même AS (Autonomous System)
- Correctement configurés pour communiquer
- Même subnet
- Pas être interface "passive"
- Les calcul métriques doivent correspondre (logique)
- Si l'authentification EIGRP est configuré, elle doit passer avec succès

SI la liason est effectuée, les deux routeurs se définissent comme voisin, et ajoutent cette relation dans leur table de voisinages et commencent à échanger. La première fois, la totalité des routes connues seront envoysé pour lesquelles il y'a une interface **active et configurée** dans EIGRP. Par la suite, ce ne sera que les modifs.

\\!/ Le routeurs s'échangent de manière régulières "Hello" et ont une durée de vie. Si avant la fin de la durée de vie, un des routeur n'a pas reçu de "Hello", le routeur voisin est considéré comme "défaillant".
La table de routage est mise à jour, retirant toutes les routes reçues par ce voisin.


**Calcul de la métrique**

Pour chacune des tables, EIGRP calcule la métrique en tenant compte de la bande passante et du délai des interfaces.

*Métrique = ( ( 10.000.000 / PPBP ) + S[délais] ) x 256*

- *PPBP : Plus petite bande passante vers le subnet en kbits/s*
- *S[délais]: Somme des délais des interfaces vers le subnet exprimé en 10µs (dizaine de micro s)*


**Mises à jour de la table de routage**

**FD (Feasible Distance) :** métrique pour un subnet du point de vue du routeur.

**RD (Reported Distance) :** est la métrique pour un subnet du point de vue du routeur voisin (annoncée par le routeur voisin)

Lorsque deux routeur s'échangent leur topologie, ils échangent des messages de type "update", contenant plusieurs informations :
- Subnet concerné
- Délai
- Bande passante
- Charge de l'interface
- Fiabilité de l'interface
- MTU
- Nombre de sauts

Le récepteur l'intègre dans sa table de topologie et ensuite calcule la RD du subnet et la FD pour ce subnet.


![](https://www.ciscomadesimple.be/wp-content/uploads/2010/03/eigrp-updt.png)

**Successor et Feasible Successor**

La particularité de l'EIGRP c'est qu'il conserve dans sa BDD toutes les infos de ses voisins, y compris celles qui n'ont pas étés intégrée.

Quand plusieurs routes existent, le "Successor" est la route qui a la plus petite métrique vers le subnet, et donc qui a la plus petite "Feasible Distance". Par défaut, cette route sera alors le "next-hop"

Dans certaines conditions, des routes peuvent-être considérés comme "valides" sans pour autant être les meilleurs, c'est ce qu'on appelle les "Feasible Successors". Si le Successor devait tomber, EIGRP irait chercher une alternative dans les Feasible.

**GESTION DES ACCIDENTS RÉSEAU**

1 - EIGRP analyse sa topologie, si un ou plusieurs Feasible existent, il remplace la route par celle parmis les FS ayant la plus petite FD

2 - Si EIGRP n'a pas de FS dans sa topologie, il entre dans un processus de "requête", i envoi des requêtes à ses voisins à la recherche d'une nouvelle route vers le subnet perdu. Si pas de solutions de la part des voisins, les voisins demandent au voisin...

**CONFIGURATION** 
/

## Synthèse mathématique :

**Bellman Ford** (RIP)

Calcul des plus courts chemins depuis un sommet source donné dans un graphe orienté pondéré.
Autorise la présence de certains arcs de poids négatif et permet de détecter l'existence d'un circuit absorbant, c'est-à-dire de poids total strictement négatif, accessible depuis le sommet source.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/23/Directed_graph_no_background.svg/220px-Directed_graph_no_background.svg.png)
*Graphe orienté pondéré*

https://fr.wikipedia.org/wiki/Algorithme_de_Bellman-Ford


**Dijkstra**(OSPF)
sert à résoudre le problème du plus court chemin.
Il calcule des plus courts chemins à partir d'une source dans un graphe orienté pondéré par des réels positifs. On peut aussi l'utiliser pour calculer un plus court chemin entre un sommet de départ et un sommet d'arrivée.

Cet algorithme est de complexité polynomiale.

-> On fait un tableau avec toute les villes & étapes à la fin
-> voir vidéo pour tuto

![](https://github.com/TL72X211/UE3_Prosit4/blob/Emilien/Dijkstra.PNG)










