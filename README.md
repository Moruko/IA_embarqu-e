# IA_embarquée

Dans les industries, la gestion de la logistique des produits à travers les différentes machines d'un workshop est un véritable casse-tête. En effet, de nombreux paramètres peuvent être ajustés afin d'optimiser le coût de production. On peut notamment modifier l'ordonnancement des produits ainsi que le nombre de produits traités.
Dans ce projet, nous nous intéressons à la gestion de la maintenance. Il est souvent difficile de déterminer le moment optimal pour effectuer une maintenance sur une machine donnée. Est-il plus pertinent de réaliser régulièrement des maintenances légères, ou vaut-il mieux attendre que la machine atteigne un état dégradé pour effectuer une intervention plus importante ? La maintenance représente un coût considérable pour les industries, ce qui rend essentiel l’optimisation de ces dépenses.
Pour répondre à ces enjeux, un nouvel outil gagne en popularité dans le secteur industriel : l'intelligence artificielle.

## L'intelligence artificielle dans le secteur de l'industrie

Dans ce contexte, l'objectif de l'intelligence artificielle est de prédire les différents types de défaillances qu’une machine pourrait rencontrer en fonction de son état actuel. Pour cela, nous devons créer un jumeau numérique, un outil qui permet de simuler l'état réel de la machine en récupérant ses données en temps réel.
Pour chaque machine, nous obtenons des mesures de l'environnement : la température de l'air, la température du processus, la vitesse de rotation, le couple et l'usure de l'outil. En plus de cela, nous acquérons pour chaque machine des informations indiquant si elle est défaillante ou non, et si oui, quels types de défaillances elle présente : usure de l'outil, dissipation thermique insuffisante, problème d'alimantation électrique, surcharge mécanique et défaillance imprévisible. Nous allons donc créer un modèle d'IA à partir de ces données, permettant d'acquérir en entrée les mesures de l'environnement et en sortie de prédire le type de défaillance susceptible de se produire.

## Conception du réseau de neurones profonds (DNN)

Pour créer notre réseau de neurones, nous utilisons le jeu de données "AI4I 2020 Predictive Maintenance Dataset". En commençant la conception du modèle, on se rend vite compte qu'un travail est nécessaire sur le jeu de données. En effet, on observe que certaines machines ayant une défaillance précise ne sont pas relevées comme étant défaillantes. Cela met en lumière l'incohérence que peuvent avoir des données reçues en temps réel.
Par ailleurs, nous avons observé une grande hétérogénéité dans la distribution des machines défaillantes entre les différentes catégories. Par exemple, le nombre de machines défaillantes dans la catégorie HDF est de 115, tandis que dans la catégorie RNF, il n’y en a que 19. Cette déséquilibre pose un problème, car le modèle n'a pas suffisamment de données équilibrées pour apprendre correctement. Il tend alors à se "spécialiser" sur la catégorie majoritaire, ce qui entraîne un phénomène appelé overfitting. Ainsi, le modèle risque de bien performer sur les données de la catégorie dominante, mais moins bien sur les autres, car il n'a pas appris de manière généralisée.

![image](https://github.com/user-attachments/assets/6f740bf3-27f6-46d5-bd75-e44e020705a9)

Le taux d'accuracy étant élevé, on pourrait penser que le réseau de neurones est performant. Pourtant, les graphes ci-dessous montre qu'il y a bien un overfitting.

# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!Problème de graphes !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Pour équilibrer la distribution des données, on effectue un suréchantillonnage. Cette méthode consiste à générer des données synthétiques pour les classes minoritaires afin d'augmenter leur nombre.On utilise la technique du SMOTE (Synthetic Minority Over-sampling Technique), qui crée de nouveaux exemples pour la classe minoritaire en interpolant entre les points de données existants.

![image](https://github.com/user-attachments/assets/34cd2170-6351-4b95-b5f6-fbb609910fd9)

# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!Problème d'accuracy et de graphes pour les données équilibrées!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Le modèle est ensuite enregistré dans un fichier .h5 qui sera exporté sur STM32CubeIde.

## Exportation du modèle sur STM32CubeIde


