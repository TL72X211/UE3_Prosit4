
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
 
## 1 - Routage dynamique RIP

**1 - Protocole à vecteur de distance :**

- Ces protocoles sont sensibles aux boucles de routages
- Dans ce type de protocole, aucun routeur ne remplit de fonction particulière.
- Ils convergent lentement (se propage lentement vers le même point)
- On citera **RIP** et **IGRP** comme étant représentatifs. **EIGRP** est aussi un protocole à vecteur de distance optimisé par cisco, qui ne présente pas tout les désavantages cités ci-dessus. 


**Qu'est ce que le RIP ? :**

Il est disponible en trois versions :
- RIPv1 (1988) : Broadcast, classfull, UDP520
- RIPv2(1993,1998) : Multicast 224.0.0.9, authentification, classless, UDP520
- RIPng : Support IPV6, authentification IPSEC, multicast FF02::9, UDP 521

**Mises à jour RIP**
- Les mises à jour s'effectuent de routeurs en routeurs
- Les mises à jour s'effectuent périodiquement, toutes les trente secondes.
- MAJ consistent à envoyer les tables de **routages entières**
- MAJ envoyés à l'adresse de diffusion 255.255.255.555 en RIPv1
- MAJ envoyés à l'adresse de Multicast 224.0.0.6 en RIPv2

**Métrique RIP**

RIP utilise l'algorithme de Bellman Fort pour calculelr les meilleures routes.
- La distance administrative de RIP est de 120 par défaut
- La métrique est basée sur le nombre de sauts
- La métrique base maximale est 15
- La métrique infinie est 16 : Empoisonne une route.

**Vecteur de distance**

https://www.lucidchart.com/publicSegments/view/8fc33029-8b59-45d3-8e5a-71b1c4ada82a/image.png

Dans cet exemple, chacun ajoute une distance de +1 , et donc la métrique s'implémente.

**Boucle de routage**

Une boucle de routage est une route pour que les paquets n'arrivent jamais à leur destination, sont répétés sur le même noeud du réseau.
Un routeur éloigné fait croire à des routeurs (bien informés d'une route modifiée) qu'il dispose d'une nouvelle route (à coût plus élevé) vers ce réseau.

Solutions aux boucles de routages :
- Définir un nombre maximum de saut (15 max) ou métrique infinie (16)
- Route poisoning : Lorsqu'une rouse tombe, le réseau doit-être immédiatement averti d'une métrique de distance infinie (le maximum de sauts +1), plus aucune incrémentation n'est possible.
- Split horizon : Split horizon empêche à un routeur d'envoyer des informations (de métrique plus élevée) à travers l'interface de laquelle elle a pris l'information.
- Triggered Update : Une MAJ est envoyé immédiatement plutôt qu'avant l'expiration du compteur lorsqu'une route est tombée, à l'aide de la métrique infinie. Tout les routeurs seront alors immédiatement avertis.
- Split Horizon avec Poison Reverse ; Le protocole de routage avertit de toutes les routes sortant d'une interface mais celles qui ont été apprises d'une mise à jour plus récente venant dans cette interface sont marquées d'une métrique de distance infinie.



