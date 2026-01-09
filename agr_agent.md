# Ask Agatha

Le point de départ de ce projet, c'est que les personnes (essentiellement
Annelies et Raphaelle) qui gèrent la mailbox digit des AGR font face à une
grosse charge de travail pour la gestion de cette mailbox ... qui contient
beaucoup de questions assez similaires les unes aux autres. L'idée initiale
était donc de développer un truc qui permette de leur alléger le travail. Au
fil du temps, l'outil a évolué pour devenir un rag agentique qui interagit avec
[AGATHA](https://agatha.arch.be/).

L'objectif premier était donc d'être capable de lire les emails dans la mailbox
puis renvoyer une réponse via SMTP. Ce qui avait le double avantage de: 

1. ne pas changer les habitudes des utilisateurs: ils continuent à envoyer un
   mail à la même adresse et recoivent une réponse avec un certain délai.

2. donner plus de temps à l'outil pour générer sa réponse puisque les archives
   ne sont pas dotées de GPU pour réaliser l'inférence et que, étant donné le
   caractère très sensible des données (par ex: "je veux les infos a propos 
   de mon grand père collabo") et les finances des archives, on ne peut pas 
   permis d'utiliser des services cloud.

Au commencement, je suis donc parti d'un dump de la mailbox digit. Le dump qui
m'a été fourni contient beaucoup plus d'info que ce qui est nécessaire. En effet,
le corpus de mail qui nous intéresse ne comprend que les emails qui ont été
**envoyés depuis la mailbox digit**. Le reste ne nous intéresse pas, puisque ce
qu'on cherche, c'est à faire un outil qui donne plus ou moins les mêmes réponses
qu'Annelies et Raphaelle. Tous les mails qui ne sont pas des _réponses_ n'ont
donc aucun intérêt dans ce contexte-ci. 

Par ailleurs, dans l'archive qui m'a été donnée, tous les mails sont fournis 
dans deux formats différents:

* **.eml** un format propriétaire de Microsoft 
* **.txt** un dump en texte brut


## Chargement des emails

Ma première idée a consisté à chercher un `DocumentLoader` qui soit capable de
parser les conversations encodées dans les fichiers `*.eml` afin de pouvoir
récupérer des conversations structurées et auxquelles je pourrais appliquer un
chat template si je veux. La solution à ce problème a donc été d'utiliser
`UnstructuredEmailLoader` qui fournit un binding entre `langchain` et
`unstructured` pour parser le contenu de fichiers `*.eml`. 

Si le parsing fonctionne, il y a toutefois **deux problèmes** auxquels j'ai été
confronté: 

1. le parsing lance parfois des exceptions à cause de l'encodage un peu 
    exotique de certains mails.
2. le parsing des emails donne des objets qui sont structurés, mais le contenu
    de la conversation reste, lui, parfaitement déstructuré.

J'ai donc changé mon fusil d'épaule et j'ai commencé à travailler avec les 
fichiers `*.txt` qui sont -- je trouve, plus facile à manipuler.


## Embedding des emails

> [!WARNING]
> Cette approche n'est pas la bonne ! Je l'explique pour que tu ne penses pas 
> à refaire la même erreur que moi. Le code correspondant à tout ça se trouve
> sur le repo [xgillard/ragagr](https://github.com/xgillard/ragagr).


Une fois les emails chargés, ma première intuition a été de chercher à calculer
des embeddings pour les emails, les stocker dans la DB, 
afin de faire une recherche sémantique qui aurait pu permettre de retrouver la
ou les réponses pertinentes avant de générer une réponse pour l'utilisateur.

Comme il est nécessaire de faire un chunking pour les mails avant de pouvoir 
calculer leur embedding, j'ai procédé en suivant la même approche que ce qui
fonctionnait bien pour le prototype [rag fial](./rag_fial.md). A savoir: utiliser
un chunking recursif tel qu'implémenté par `RecursiveCharacterTextSplitter`.

### Code

Tout le code de ce dont je vais parler ci dessous est disponible sur le 
repository github [xgillard/agr_agent](https://github.com/xgillard/agr_agent).

Avant d'entamer le développement de ask-agatha dont le code est dans le
repository que je viens de mentionner
[xgillard/agr_agent](https://github.com/xgillard/agr_agent), j'ai aussi fait
une série d'expérimentations pendant lesquelles j'ai cherché à développer un
premier rag qui permette de répondre à ces questions. Le code de ces premières
expérimentations est disponible sur le repository
[xgillard/ragagr](https://github.com/xgillard/ragagr).


