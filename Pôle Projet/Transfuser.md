# Transfuser : Multi-Modal Fusion Transformer for End-to-End Autonomous Driving
[ArXiv](https://arxiv.org/pdf/2104.09224v1.pdf)

Tags:
/
### Imitation Learning
##### Integration de la vision et du LIDAR grâce à des transformers

- Image only models : pas très robuste face à des situations inhabituelles, mais permet d'avoir des infos comme les feux de signalisation
- LIDAR only models : plus robuste car "vision" 3D de l'espace, mais incapable de voir un feu ou un panneau
- Combined models : la plupart du temps, les modèles qui combinent les 2 approches ont du mal à gérer les situations complexes, du fait de l'aspect très "focused" de leur extraction de features.

Transfuser+ implémente une méthode de focus multimodal, en glissant dans le réseau des Transformers qui permettent d'élargir l'attention, de passer en multi-modal.

L'apprentissage est fait en Behavior cloning
donnée : image de la cam frontale + LIDAR (We use a single time-step input since prior works on IL for autonomous driving have shown that using observation histories may not lead to performance gain)
label : les 4 coordonnées suivient par l'expert dans l'environnement à partir de l'image

Loss : $L_1$ distance entre la vraie traj et la traj suivie

Dans le modèle, le rôle des transfos est d'apporter de l'attention sur des endroits/données multiples de l'entrée. L'aspect "fuser" est juste là pour mettre en relation les 2 entrées.
Ces transfos sont utilisés comme des ViTs (vision transformers cf CV benchmark): les "images" sont segmentées puis alignées pour former les "tokens" de la séquence d'entrée du transfo. Cela fonctionne car les transfos ne font pas attention à la position des tokens dans la phrase.

Learnable positional embedding + velocity context

Feature extraction via ResNet34 (ImageNet Pretrained) et ResNet18 (raw), jusqu'à 8x8x512 puis avgpooling


A la fin de l'encodeur, réduction de dimension via MLP, puis feed dans l'hidden state initial d'un GRU.
Ce GRU prend en entrée la position du véhicule ((0,0)initialement car coordonnées centrées sur le véhicule) et les coords de la destination objectif.

La sortie est alors un petit déplacement à effectuer pendant la frame, traduit par un controleur PID en commandes pour l'accélération et la direction.