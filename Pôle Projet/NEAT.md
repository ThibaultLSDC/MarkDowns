# NEAT : Neural Attention Fields for End-to-End Autonomous Driving
[ArXiv](https://arxiv.org/pdf/2109.04456.pdf)

Tags:
[[Autonomous Driving]]

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

