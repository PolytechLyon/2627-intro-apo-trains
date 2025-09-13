# Calcul du chemin le plus court

Dans cet exercice, vous allez réaliser une application Java qui calcule le chemin le moins coûteux entre un point de
départ et un point d'arrivée.

## Modalités
* Cet exercice est noté.
* Il est à réaliser en binôme.
* Sauf exception, approuvée par l'enseignant, vous ne devez pas réaliser cet exercice seul.
* Un compte rendu, mentionnant impérativement le nom et prénom de chaque membre de binôme, est à fournir
[ici](Rapport.md).

## Algorithme de Dijkstra
Étant donné un graphe orienté $G = (V, E)$ où :
* $V$ est un ensemble fini de sommets,
* $E \subseteq V \times V \times \Bbb{R}^+$ est un ensemble fini des arcs pondérés par des réels positifs.

L'algorithme de Dijkstra sert à trouver les chemins les plus courts à partir d'un sommet $v_{0} \in V$ vers tous les
autres sommets du graphe.
La distance parcourue par un chemin liant deux sommets est calculée en fonction de la somme des poids des arcs qui
constituent ce chemin.
Le chemin entre deux sommets est donc le plus court quand cette distance est minimale.

L'algorithme peut être décrit par le pseudocode suivant. $U$ représente l'ensemble des sommets déjà traités, et
$distances$ le tableau des distances minimales connues à chaque itération.

> **entrée** : $G = (V, E)$, $v_{0} \in V$ (sommet de départ)
> * $U := \phi{} $
> * **pour chaque** $v \in V$
>   * $distances[v] := +\infty{} $
> * $distances[v_0] := 0$
> * **tant que** $V \setminus U \neq \phi{}$
>   * $v := \underset{x \in V \setminus U}{\arg \min} ~(distances[x])$ (le sommet dans $V \setminus U$ où $distances[v]$ est minimal)
>   * $U := U \cup \\{ \{ v \} \\}$
>   * **pour chaque** $u \in V \setminus U$ où $\exists{} (v, u, w) \in E $
>     * $d := distances[v] + w$
>     * **si** $d < distances[u]$
>       * $distances[u] := d$

## Objectif
Nous voulons réaliser une application Java qui permet de visualiser certaines informations concernant un réseau ferré.
L'application lit en entrée la topologie du réseau au format textuel. 

L'application doit permettre de réaliser les actions suivantes à partir des données d'entrée :
1. Afficher une liste de toutes les connexions directes réalisées par le réseau.
2. À partir d'une ville donnée, calculer les durées des trajets les plus courts vers les autres villes en utilisant
l'algorithme de Dijkstra.
3. À partir d'une ville donnée, calculer les chemins des trajets les plus courts vers les autres villes en utilisant une
variante de l'algorithme de Dijkstra.

### Contraintes
Un jeu de tests est mis à votre disposition pour permettre un retour automatique sur l'avancement de votre travail.
Pour permettre ce retour, les contraintes suivantes doivent être respectées :
* Votre code doit résider dans le dossier `src` uniquement. C'est le dossier racine du code.
* La classe principale de votre application doit avoir le nom qualifié `com.example.trains.Application`.
* Le format de fichier d'entrée doit être respecté. Il est interdit de modifier ce fichier.
* Le format d'affichage doit être respecté, comme précisé dans les exemples fournis ci-dessous.

Bien que la violation de l'une de ces contraintes puisse entraîner une perte de points, la note attribuée par le jeu de
tests n'est donnée qu'à titre indicatif.
Votre note finale peut varier à la hausse ou à la baisse en fonction de la qualité de votre code et de votre compte
rendu.

## Afficher les connexions directes entre deux villes
Le premier objectif consiste à lister les paires des villes qui partagent une connexion directe dans notre réseau.
Ces informations sont disponibles dans le fichier d'entrée `input.txt`.

### Format de fichier d'entrée
Le fichier `input.txt` décrit les connexions par train entre différentes villes.
Il a le format suivant :
1. Une liste des noms de toutes les villes desservies. Les noms sont séparés par des retours à la ligne.
2. Une ligne vide séparant les noms de villes de leurs connexions.
3. Une liste des connexions entre les villes. Les connexions sont séparées par des retours à la ligne.
   Chaque connexion est un triplet des élements suivants, dans l'ordre :
   * L'index de la ville de départ, dans la liste des villes, en commençant à zéro,
   * L'index de la ville de destination, dans la liste des villes, en commençant à zéro,
   * La durée de trajet en heure.


### Structure de données

Pour pouvoir afficher la liste des connexions directes entre les villes de réseau ferré, il convient de représenter ces
connexions par un graphe orienté, dont les sommets sont les villes et les arcs sont les connexions.

Proposer des structures de données adaptées permettant la représentation des données d'entrée par un graphe orienté.

### Construction du graphe

Réaliser un code qui construit, en mémoire, le graphe décrit dans le fichier `input.txt` selon la structure de données
proposée.

Vous devez écrire un code qui lit le fichier `input.txt` et remplit dynamiquement votre structure de données.

Pour vous aider à réaliser la lecture de fichier, la classe `DotConvertor`,
fournie dans [ce dépôt](https://github.com/PolytechLyon/2627-intro-apo-convertor), donne un exemple d'un code qui lit
un fichier du même format que le fichier `input.txt` et le convertit en format dot, un format compris par des outils de
construction visuelle de graphes tels que `graphviz`.

### Format d'affichage

Les noms de villes dans une paire doivent être séparés par une virgule.
Les paires doivent être séparées par des retours à la ligne.

Suite à une réalisation réussite de cet objectif,
votre application doit afficher la liste suivante, sans prise en compte de l'ordre :

```
Lyon, Paris
Lyon, Grenoble
Lyon, Dijon
Lyon, Valence
Lyon, Montpellier
Paris, Lyon
Paris, Montpellier
Paris, Bordeaux
Paris, Toulouse
Grenoble, Lyon
Grenoble, Valence
Dijon, Lyon
Valence, Lyon
Valence, Grenoble
Montpellier, Lyon
Montpellier, Paris
Montpellier, Narbonne
Bordeaux, Paris
Toulouse, Paris
Toulouse, Narbonne
Narbonne, Montpellier
Narbonne, Toulouse
```

## Calcul de la durée du plus court trajet

Nous voulons pouvoir calculer le temps du trajet optimal, c'est-à-dire avec la durée totale la plus courte, à partir
d'une ville donnée et vers toutes les autres villes accessibles par train.

La durée totale est calculée à partir de la somme des durées individuelles de chaque voyage, et sans prendre en compte
le temps d'attente lors d'une connexion.

### Passer le nom de ville en argument du programme

Vous devez faire en sorte que le programme accepte en argument optionnel le nom d'une ville.

Si un tel argument est fourni, au lieu d'afficher la liste des connexions directes l'application affiche les durées
des trajets les plus courts pour atteindre les autres villes à partir de la ville dont le nom est indiqué par
l'argument.

Avant d'afficher cette liste, et en première ligne de la sortie standard, l'application affiche, par une phrase
succincte, la ville de départ. Par exemple, lorsque l'on fournit "Grenoble" comme argument, l'application peut afficher
"Calculer les durées depuis Grenoble...".

### Déterminer la plus courte durée pour atteindre une destination

Vous devez écrire un code qui réalise l'algorithme de Dijkstra pour calculer la durée optimale pour atteindre chaque
autre destination accessible à partir de la ville dont le nom est passé en entrée.

Vous serez probablement amenés à modifier votre structure de données afin d'optimiser l'exécution de l'algorithme.
Par exemple, la structure d'un sommet peut contenir une valeur booléenne pour indiquer si le sommet est visité,
c'est-à-dire s'il appartient à l'ensemble $U$.
Une autre valeur peut indiquer la distance calculée à chaque itération, c'est-à-dire les valeurs du tableau $distances$.

### Format d'affichage
Le nom de ville de destination doit être séparé de la durée du trajet optimal par une virgule.
Les paires doivent être séparées par des retours à la ligne.

Suite à une réalisation réussite de cet objectif avec le nom "Grenoble" en entrée, votre application doit afficher la
liste suivante, sans prise en compte de l'ordre :

```
Calculer les durées depuis Grenoble...
Lyon, 1.40
Paris, 3.30
Grenoble, 0.00
Dijon, 3.00
Valence, 1.00
Montpellier, 3.10
Bordeaux, 5.80
Toulouse, 5.85
Narbonne, 4.10
```

## Calcul du chemin du plus court trajet

Une variante de l'algorithme décrit ci-dessus peut être utilisée pour déterminer le chemin le plus court, en plus de son
coût optimal.
Cela peut se faire en ajustant l'algorithme et éventuellement la structure de données associée.

Vous devez modifier le code pour pouvoir afficher le chemin le plus court vers chaque autre ville, en plus de leurs durées
respectives, en partant d'une ville donnée lorsque le nom d'une telle ville est fourni en entrée.

Vous serez probablement amenés à adapter votre structure de données au nouvel algorithme.
Par exemple, la structure d'un sommet peut contenir une référence au sommet précédent dans le chemin optimal.

### Format d'affichage
Chaque trajet doit être affiché au format d'une liste des valeurs séparées par des virgules.
La liste se constitue des noms de villes du trajet dans l'ordre inversé, en partant de la destination.
La dernière valeur de la liste reste la durée du trajet.
Les trajets doivent être séparés par des retours à la ligne.

Suite à une réalisation réussite de cet objectif avec le nom "Grenoble" en entrée, votre application doit afficher la
liste suivante, sans prise en compte de l'ordre :

```
Calculer les durées depuis Grenoble...
Lyon, Grenoble, 1.40
Paris, Lyon, Grenoble, 3.30
Grenoble, 0.00
Dijon, Lyon, Grenoble, 3.00
Valence, Grenoble, 1.00
Montpellier, Lyon, Grenoble, 3.10
Bordeaux, Paris, Lyon, Grenoble, 5.80
Toulouse, Narbonne, Montpellier, Lyon, Grenoble, 5.85
Narbonne, Montpellier, Lyon, Grenoble, 4.10
```

## Jeu de tests à titre indicatif
À chaque soumission, une note indicative, sur 31 points, est attribuée à votre travail par un jeu de tests unitaires.
Les tests sont les suivants :
* Le code contient une classe principale dont le nom qualifié est `com.example.trains.Application` (1 point).
* L'application affiche le nom de ville passé en argument sur la première ligne de sortie standard (2 points).
* L'application affiche la liste des connexions directes entre deux villes (16 points).
* L'application affiche la liste des durées des trajets les plus courts à partir de chaque ville (8 points).
* L'application affiche la liste des chemins des trajets les plus courts à partir de chaque ville (4 points).

## Compte rendu
Le compte rendu doit impérativement contenir le nom et prénom de chaque membre de binôme. À défaut de ces informations,
les étudiants ne pourront pas être identifiés et risquent de perdre la totalité de la note de cet exercice.








