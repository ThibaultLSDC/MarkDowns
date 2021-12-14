# NEAT : Neural Attention Fields for End-to-End Autonomous Driving

Tags:
[[Autonomous Driving]]


Ajout de tâche auxiliaires pour orienter/améliorer l'apprentissage: CILRS proposait de  faire prédire au réseau la vélocité du véhicule en self-supervised
Autres tâches essayées:
- Image-autoencoding
- 2D semantic segmentation
- BEV semantic segmentation
- 2D semantic prediction
- BEV semantic prediction
Ce dernier donne les meilleurs résultats : on prédit la segmentation de BEV dans les frames à venir, et le modèle incorpore mieux la structure de son environnement
MAIS : cette tâche est souvent réalisée avec un LIDAR plus BEV en entrée -> la représentation en BEV se fait naturellement
Ici on travaille en image only

A partir d'une coordonnée BEV dans l'espace et le temps $(x, y, t)$ il n'est pas aisé de dire quels pixels de l'image sont à associer à la-dite position.
C'est ce que NEAT field tant à résoudre.
On cherche à apprendre chaque position de la BEV avec un seul vecteur d'attention tiré de l'encodage.
La tâche est donc réalisée en prédisant des waypoints offset pour la trajectoire, et la BEV semantic prediction en auxiliaire.
On rajoute $(x', y')$ aux données d'entrées, qui représente la destination globale.
On a donc en entrée de NEAT des vecteurs dans $R^5$: $p=(x, y, t, x', y')$, et en sortie $o$ dans $R^2$, qui décrit le vecteur entre chaque point $x, y$ et le prochain waypoint.

On définit maintenant les $(c_n)_{n=1,...,N}$ par :

$\begin{equation}
    c_n = \left\{
    \begin{array}{ll}
        \mathbb{I} \cdot c \in \mathbb{R}^C \mbox{ si } n = 0 \\
        a_{n-1}\cdot c \mbox{ si } n \ne 0
    \end{array}
    \right.
    \mbox{   avec }
    \left\{
        \begin{array}{ll}
            \mathbb{I} \in \mathbb{R}^{1\times C} \mbox{ est une matrice ligne remplie de 1} \\
            a_{n-1} \in \mathbb{R}^{1\times C} \mbox{ est un vecteur d'attention calculé depuis } p \mbox{ et } c_{n-1}
        \end{array}
    \right.
\end{equation}$

$a_n$ est donné par un MLP appliqué à p, avec de la conditionnal batchnorm conditionnée sur $c_{n-1}$.

Il donne un vecteur d'attention dans $R^N$ qui permet de calculer $c_{n+1}$ (moyenne pondérée par $a_n$ des lignes de $c$)
	
Decoder:
Le décodeur est juste un MLP sur $p$ conditionné par $c_i$, qui renvoie l'offset $o$ ainsi que la classe de la zone pour la tâche auxiliaire de BEV. Tous les $c_i$ sont utilisés pour l'entrainement.
Seul $c_final=c_N$ est gardé en phase d'inférence.

Phase d'entrainement:
Le papier exploite CARLA au maximum: le simulateur permet de générer les labels, et chaque point en face du véhicule est classé en none, road, obstacle, red light et green light.

Au résultat on a 2 loss, une L1 sur les offsets et une CE sur la classe de la zone étudiée, pour chacun des points de la grille sur les N itérations de NEAT.

Controller:
Les offsets sont fournis à un controleur PID, qui controle l'accélérateur/frein ainsi que le volant pour diriger la voiture vers le prochain waypoint. Le PID est bloqué si le decodeur détecte un feu rouge (en gros).


[ArXiv](https://arxiv.org/pdf/2109.04456.pdf)