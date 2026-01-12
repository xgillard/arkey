# BERT-like routing

Dans le graphe de ask agatha tel qu'il existe actuellement, il y a beaucoup de
décisions à prendre (choisir un outil plutôt qu'un autre, extraire les
informations nécessaires pour lancer une recherche sur agatha depuis le
contexte, décider qu'on a suffisamment d'information pour répondre à
l'utilisateur, etc...). Pour toutes ces tâches on utilise actuellement un LLM
en mode génératif ce qui est assez long (et qui s'avèrera couteux à force
puisque les contextes sont de plus en plus longs).

Pour rendre le système efficace en pratique, je pense qu'il serait donc utile
de remplacer toutes ces interactions LLM par des taches utilisant des modèles
plus petits: 

* un modèle de type BERT pour faire la classification en vue d'organiser le 
    routage des requetes vers une portion du graphe ou une autre
    (`AutoModelForSequenceClassification.from_pretrained('...')`)

* un modèle de type BERT pour faire l'extraction d'information (NER avec un 
    modèle `AutoModelForTokenClassification`). Ca pourrait marcher. Mais ça 
    pourrait aussi être plus fragile car il est parfois nécessaire de reformuler
    le texte pour extraire une information qui est parfois implicite.

Utiliser des modèles de ce type pourrait s'avérer très utile car les décisions
sont prises beaucoup plus rapidement qu'avec un LLM qui doit générer une réponse
complète.
