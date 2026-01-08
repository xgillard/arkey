# Experiences consécutives à AI-rchivist

Après avoir implémenté [AI-rchivist](./ai_rchivist.md), j'ai cherché à mesurer
l'efficacité de la méthode et à la comparer à une approche plus tradditionnele
(named entity recognition aka NER). Pour cela, j'ai du: 

1. collecter un corpus de données annotées
2. entrainer une série de petits modèles visant à faire la NER
3. réaliser une première comparaison entre les deux approches en utilisant des métriques "dures" (approche ML)
4. réaliser une seconde évaluation qui cherche à mettre en évidence les préférences réelles des archivistes.

## Collecte d'un corpus annoté

J'ai cherché à trouver un corpus de données annotées qui me permette
d'entrainer un modèle de classification de tokens pour réaliser la
reconnaissance d'entités nommées. Pour ca, j'ai commencé par utiliser un corpus
que j'ai scrappé depuis les ressources en ligne de l'université de Tours. Comme
ce corpus n'est pas sensé être scrappé et re-publié ailleurs que sur le site de
l'université de tours, je n'ai pas publié ce dataset comme un dataset sur
huggingface. Mais je t'ai laissé tous les scripts nécessaires pour pouvoir
re-créer ce dataset ou reproduire ce que j'ai fait.

Les données scrappées depuis l'université de Tours sont utiles
car il s'agit aussi de documents administratifs, d'archives, datant
des XVe, XVIe. Mais elles ne sont que faiblement alignées avec le 
corpus de données cible (le corpus brabant-xvii dont j'ai parlé plus tot).
En effet, les données du corpus TEI sont exclusivement en français de
cette période, alors que les documents issus des lettres de pardon et
de sentence du conseil de brabant sont essentiellement en néérlandais.

Dans un 2e temps, j'ai préparé une plateforme basée sur labelbox et 
que j'ai configuré pour demander à des archivistes capables de lire
et comprendre ces textes anciens de réaliser le tagging de ces textes.
La plateforme utilisée à cet effet est disponible sur 
[labelling.arch.be](labelling.arch.be)


## Entrainement de petits modèles

Après avoir collecté mon dataset, j'ai réalisé l'entrainement de plusieurs 
types modèles distincts: 

* le premier est une version de xlm roberta que j'ai fine tuné en
    n'utilisant que le corpus de données (corpus TEI) provenant des
    ressources en ligne de l'université de Tours. L'idée était ici de voir
    s'il était possible de se baser sur les capacités de transfert cross
    linguistique de XLM ROBERTA pour obtenir un modèle qui ait des
    performances décentes.
    
    Une version de ce test réalisé à partir de xlm-roberta-base est
    [disponible ici](https://huggingface.co/xaviergillard/xlm-roberta-base-vieille-france).
    Une version obtenue à partir de xlm-roberta-large est 
    [disponible ici](https://huggingface.co/xaviergillard/xlm-roberta-large-vieille-france).

* le 2e type de modèle entrainé a consisté à faire un pretraining from
    scratch pour un modèle de type BERT, mais qui aurait été entrainé
    uniquement sur les données du vrai corpus cible. Ce meme modele ayant
    ensuite été entrainé sur la tache de classification de tokens afin de
    réaliser la NER à partir de ce même jeu de données. 

    Méthodologiquement, c'est assez moyen et il y a un risque d'overfiting
    important. Mon intention était toutefois de pouvoir voir si un 
    modèle qui aurait été entrainé en utilisant un tokenizer basé sur
    ce type de texte anciens serait plus à même de bien se comporter.
    (**spoiler:** non ce n'est pas le cas, et fine tuner d'autres modèles
    donne de meilleurs résultats).

    Le [modele obtenu](https://huggingface.co/xaviergillard/brabant-xvii-from-scratch)
    est disponible sur huggingface.

* j'ai aussi essayé de fine tuner
    [gysbert](https://huggingface.co/emanjavacas/GysBERT) et
    [gysbert-v2](https://huggingface.co/emanjavacas/GysBERT-v2) sur ces
    données. Pour cela, j'ai testé plusieurs stratégies: un réentrainement
    profond, ou un réentrainement qui freeze les couches inférieures
    du modèle. Cette étape n'a globalement donné aucun résultat. Il 
    n'y a pas de vraie différence de performance entre Gysbert, 
    Gysbert-v2 ou les versions fine tunées.

* Par ailleurs, j'ai aussi fait le fine tuning de XLM-Roberta
    - en utilisant les données du corpus TEI
    - en utilisant les données du corpus [brabant-xvii](https://huggingface.co/xaviergillard/brabrant-xvii-ner)
    - en utilisant les données des [deux corpus](https://huggingface.co/xaviergillard/xlm-roberta-large-vieille-france-v2).


## Evaluation "tradditionelle" avec des métriques "dures"

J'ai calculé des métriques "dures" (precision, recall, f1) que j'ai du adapter
pour rendre les résultats a peu près comparables pour tous les modèles
considérés.

Les résultats obtenus montrent que les petits modèles se comportent 
nettement mieux que les LLM génératifs tout en ne représentant qu'une
fraction du cout de calcul.

![hard metrics](./hard_metrics.png "Hard Metrics")

**Important** 
Il faut toutefois être bien conscient que les métriques qui ont été
ainsi calculées sont loin d'être parfaites et sont même biaisées en 
faveur des petits modèles. En effet, les métriques ne tiennent pas 
compte du fait qu'un LLM pourrait avoir trouvé une réponse correcte
mais en la reformulant (par exemple dire "le 2 février 1532" plutot que
"februar ii mdxxii").


## Evaluation des vraies préférences

Pour compenser le fait que les métriques dure sont biaisées en faveur des
petits modèles, et pour voir si les métriques ainsi obtenues correspondent aux
préférences réelles des archivistes en matière d'extraction d'entité nommées,
j'ai développé une petite plateforme visant à réaliser un ranking qualitatif
sur une échelle allant de 0 à 5 des modèles entrainés. Cette plateforme est
disponible publiquement sur
[https://modelranking.arch.be/](https://modelranking.arch.be/)

Afin de tenir compte du fait que ce sont les mêmes évaluateurs qui 
évaluent leur préférence sur les mêmes textes, j'ai utilisé un 
modèle à effets mixtes (mixed effect model) pour pouvoir mesurer les
préférences tout en quantifiant l'erreur.

Ces résultats montrent que les évaluateurs préfèrent généralement 
les résultats donnés par les modèles brabant-xvii-ner ou 
vieille-france-v2. La préférence est assez franche même en tenant 
compte de la marge d'incertitude (intervalle de confiance = 95%).

![mixed effect model](./mixed_efffect.png "Mixed Effect Model")

Il faut toutefois tempérer les conclusions qu'on voudrait pouvoir tirer 
sur base de cette expérience et du résultat du modele à effets mixtes
car la participation à cette partie de l'expérience s'est avérée 
bien trop faible même si le résultat du test est positif (2 évaluateurs).

#### Code et Résultats

Le code que j'ai écrit durant ma phase de collecte, d'exploration des
données, le code que j'ai utilisé pour l'entrainement des modèles (que j'ai
malgré tout du modifier avant de pouvoir l'exécuter réellement sur les
cluster du CECI), le code que j'ai écrit pour la plateforme d'évaluation
qualitative et tous mes résultats liés à cette partie de mon travail sont
disponibles sur le repository
[https://github.com/xgillard/xp-airchivist](https://github.com/xgillard/xp-airchivist).

Le plus intéressant à consulter dans pour comprendre ce qui a été fait, 
et comment cela a été fait, sera de consulter le folder "recap" qui se
trouve à la racine du repository. Toutes les étapes y sont groupées au sein
de notebooks qui progressent chronologiquement vers l'obtention des 
résultats.

Le code que j'ai écrit pour réaliser la plateforme d'évaluation qualitative
est disponible dans le folder "model_ranking" du même repository. Son
architecture est assez similaire à celle de ai-rchivist évoquée plus haut.
Pour la lancer, il suffit à nouveau de lancer le docker compose qui se
chargera d'exécuter les conteneurs pour le frontend et pour le backend.
Par facilité, tous les résultats collectés durant la phase d'évaluation
sont stockés dans une base de données sqlite.

Je pense que l'output le plus conclusif et le plus intéressnt qui soit
sorti de l'évaluation des performances des différents modèles m'a été
fourni par _Klaas Van Gelder_ à l'issue de l'évaluation qualitative. En
résumé, ce qu'il disait était que **si les LLM donnent des éléments
intéressants, ils hallucinent beaucoup trop**. Et même si les
petits modèles dédiés sont assez bruités, leur output est généralement
meilleur (puisque sourcé directement dans le texte). Un autre élément
intéressant qui soit sorti du debrief post mortem de cette expérience est
que **le fait d'avoir plusieurs prédictions fournies par plusieurs modèles
différents pour les memes textes était intéressante** puisqu'elle permettait
de mettre en évidence les éléments importants, et potentiellement
difficiles à cerner sans cela. J'imagine donc qu'il pourrait être
intéressant de voir si on obtient de meilleurs résultat en utilisant une
méthode d'ensemble mais pour tester ça, le problème sera de trouver 
suffisamment de personnes pour participer à l'étude.


