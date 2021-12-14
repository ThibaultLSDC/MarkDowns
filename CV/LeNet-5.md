# LeNet-5
Tags:
[[CNN]]

![[LeNet-5.PNG]]

LeNet-5 marque la première apparition des réseaux à convolution, les CNN. Il introduit 3 idées fondamentales:
- Les champs de perceptions
- Le partage de paramètres
- Le sub-sampling

### Les champs de perception
Les champs de perception locale permettent l'extraction de caractéristiques visuelles élémentaires, les regroupant en cartes de caractéristiques (feature maps). Ces feature maps sont ensuite elles-même traitées par des champs de perception dans la suite du réseau, pour petit-à-petit extraire des features de haut-niveau d'abstraction. Cette opération peut être effectuée de multiple fois sur la même couche pour créer des feature maps avec plusieurs channels.
### Le partage de paramètres
Pour chaque feature map, les éléments calculés sont issus du même jeu de paramètres. C'est l'une des innovations principales

Sources:
- [Gradient Based Learning Applied to Document  
Recognition](http://vision.stanford.edu/cs598_spring07/papers/Lecun98.pdf)
- [TowardsDataScience](https://towardsdatascience.com/understanding-and-implementing-lenet-5-cnn-architecture-deep-learning-a2d531ebc342)