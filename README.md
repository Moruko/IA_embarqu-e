# IA_embarquée

## Introduction
Dans les industries, la gestion de la logistique des produits à travers les différentes machines d'un workshop est un véritable casse-tête. En effet, de nombreux paramètres peuvent être ajustés afin d'optimiser le coût de production. On peut notamment modifier l'ordonnancement des produits ainsi que le nombre de produits traités.
Dans ce projet, nous nous intéressons à la gestion de la maintenance. Il est souvent difficile de déterminer le moment optimal pour effectuer une maintenance sur une machine donnée. Est-il plus pertinent de réaliser régulièrement des maintenances légères, ou vaut-il mieux attendre que la machine atteigne un état dégradé pour effectuer une intervention plus importante ? La maintenance représente un coût considérable pour les industries, ce qui rend essentiel l’optimisation de ces dépenses.
Pour répondre à ces enjeux, un nouvel outil gagne en popularité dans le secteur industriel : l'intelligence artificielle.

## L'intelligence artificielle dans le secteur de l'industrie

Dans ce contexte, l'objectif de l'intelligence artificielle est de prédire les différents types de défaillances qu’une machine pourrait rencontrer en fonction de son état actuel. Pour cela, nous devons créer un jumeau numérique, un outil qui permet de simuler l'état réel de la machine en récupérant ses données en temps réel.
Pour chaque machine, nous obtenons des mesures de l'environnement : la température de l'air, la température du processus, la vitesse de rotation, le couple et l'usure de l'outil. En plus de cela, nous acquérons pour chaque machine des informations indiquant si elle est défaillante ou non, et si oui, quels types de défaillances elle présente : usure de l'outil, dissipation thermique insuffisante, problème d'alimantation électrique, surcharge mécanique et défaillance imprévisible. Nous allons donc créer un modèle d'IA à partir de ces données, permettant d'acquérir en entrée les mesures de l'environnement et en sortie de prédire le type de défaillance susceptible de se produire.

## Conception du réseau de neurones profonds (DNN)

Pour créer notre réseau de neurones, nous utilisons le jeu de données "AI4I 2020 Predictive Maintenance Dataset". En commençant la conception du modèle, on se rend vite compte qu'un travail est nécessaire sur le jeu de données. En effet, on observe que certaines machines ayant une défaillance précise ne sont pas relevées comme étant défaillantes. Cela met en lumière l'incohérence que peuvent avoir des données reçues en temps réel.
Par ailleurs, nous avons observé une grande hétérogénéité dans la distribution des machines défaillantes entre les différentes catégories. Par exemple, le nombre de machines défaillantes dans la catégorie HDF est de 115, tandis que dans la catégorie RNF, il n’y en a qu'un seul. 
![image](https://github.com/user-attachments/assets/bfea6772-809c-4c37-aee5-43520e7c58fe)

Ce déséquilibre pose un problème, car le modèle n'a pas suffisamment de données équilibrées pour apprendre correctement. Il tend alors à se "spécialiser" sur la catégorie majoritaire, ce qui entraîne un phénomène appelé overfitting. Ainsi, le modèle risque de bien performer sur les données de la catégorie dominante, mais moins bien sur les autres, car il n'a pas appris de manière généralisée.

![image](https://github.com/user-attachments/assets/6f740bf3-27f6-46d5-bd75-e44e020705a9)

Le taux d'accuracy étant élevé, on pourrait penser que le réseau de neurones est performant. En effet, les graphes ci-dessous montre que l'amélioration est totalement linéaire et que l'accuracy atteint des valeurs élevées très rapidement.

![image](https://github.com/user-attachments/assets/32c19395-bc0f-4db5-8adc-d162c554e7bc)


Pour équilibrer la distribution des données, on effectue un suréchantillonnage. Cette méthode consiste à générer des données synthétiques pour les classes minoritaires afin d'augmenter leur nombre.On utilise la technique du SMOTE (Synthetic Minority Over-sampling Technique), qui crée de nouveaux exemples pour la classe minoritaire en interpolant entre les points de données existants.

![image](https://github.com/user-attachments/assets/34cd2170-6351-4b95-b5f6-fbb609910fd9)

![image](https://github.com/user-attachments/assets/39fd0b6f-4dcb-4d1e-a74f-2a959fac5e32)

On observe que l'accuracy pendant l'entrainement est de 90% tandis qu'elle est de 67% pour la phase de test. Le modèle n'est donc pas optimal et pourrait être amélioré.

![image](https://github.com/user-attachments/assets/8978f33c-a6cb-475e-86ca-8d8f33908471)

Le modèle est ensuite enregistré dans un fichier .h5 qui sera exporté sur STM32CubeIde.

## Inférence du modèle sur STM32CubeIde

Le réseau de neurones est transféré sur STM32CubeIde, qui dont les performances peut être analysées et évaluées.
![image](https://github.com/user-attachments/assets/7f1df509-2cdf-423f-b13b-b6d9b5f65181)

![graphe maintenance](https://github.com/user-attachments/assets/470af8f1-a722-488b-be91-7ab5e3b4282a)

L'accuracy de 100% révèle l'overfitting de notre modèle. Il n'est donc pas optimal.

## Implémentation du modèle sur la carte STM32L4R9
Un code C implémente le modèle d'IA sur un microcontrôleur STM32 en utilisant X-CUBE-AI, avec une communication UART pour échanger des données avec un périphérique externe.
Les fonctions acquire_and_process_data et post_process assurent la conversion des données d'entrée et de sortie, en veillant à la compatibilité entre les formats UART (octets) et ceux attendus par le modèle (flottants). Le projet est initialisé comme suit :

![image](https://github.com/user-attachments/assets/0254958d-f402-4c5e-ac75-d8f589e40c1b)

## Validation du comportement
Pour évaluer le bon fonctionnement du système, un script Python joue le rôle d'interface de test. Son objectif est double : envoyer des données de test au microcontrôleur et analyser les résultats renvoyés. La synchronisation précise entre les deux programmes est cruciale, c'est pourquoi les fonctions synchronize_UART (STM32) et synchronise_UART (Python) mettent en place un protocole d'échange pour initier proprement chaque transmission.

À cause d’un problème lié à la carte, le programme Python analyse les données lors de la première itération, puis s’interrompt. Nous ne pouvons donc pas évaluer l’ensemble du modèle final. Cependant, nous avons testé un modèle antérieur (sans équilibrage des données) en utilisant une carte AREM. Avec ce matériel, nous avons vérifié le bon fonctionnement du microcontrôleur.

![Capture d’écran IA Embarqué](https://github.com/user-attachments/assets/3445faa1-6ffe-4115-9e60-53ae61c39b40)

Le réseau de neurones utilisé présentant de l’overfitting, nous obtenons une accuracy de 98 %.

## Conclusion
Ce projet valide la faisabilité d’un système embarqué de maintenance prédictive, avec une communication fonctionnelle entre Python et la STM32 via UART. Cependant, le modèle souffre d'overfitting et les problèmes matériels ont limité les tests finaux. Bien que prometteuse pour réduire les coûts industriels, cette approche nécessite encore des améliorations, notamment sur la fiabilité du modèle et du matériel, avant un déploiement à grande échelle. Une analyse du coût de l'utilisation de l'IA serait intéressante pour évaluer son intérêt économique réel.
