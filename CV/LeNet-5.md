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
Pour chaque feature map, les éléments calculés sont issus du même jeu de paramètres. C'est l'une des innovations principales de la ComputerVision. On applique la même opération, la même observation à chaque endroit de l'image. Le réseau de neurones ne reconnait plus des pixels comme dans un perceptron, MLP, il reconnait des motifs, des patterns. Ce n'est pas sa position dans l'image qui compte, c'est sa position par rapport aux autres features de l'image. Ainsi on généralise beaucoup mieux et naturellement la connaissance acquise.
### Le sub-sampling
Dans ce papier, le but du sub-sampling est de réduire encore l'importance de la position des features dans l'image. Ainsi le but est de réduire la résolution des features map pour augmenter les relations entres les features. Les créateurs du papier ont donc décider d'utiliser des couches qui moyennent 4 par 4 les pixels de chaque feature map, avant de la multipliée par un poids et d'ajouter un biais, puis d'y appliquer une sigmoïde.


#### MNIST
On introduit aussi pour la première fois le dataset MNIST

Sources:
- [Gradient Based Learning Applied to Document  
Recognition](http://vision.stanford.edu/cs598_spring07/papers/Lecun98.pdf)
- [TowardsDataScience](https://towardsdatascience.com/understanding-and-implementing-lenet-5-cnn-architecture-deep-learning-a2d531ebc342)