# AI-rchivist

Il s'agit d'une expérience que j'ai initialement développé pour pouvoir
comparer la performance des modèles génératifs avec la performance des modèles
de NLP plus classiques (BERT / RoBERTa) pour l'extraction d'entités nommées
(personnes, lieux, dates).

#### Interface Web

J'ai préparé une petite interface web
[ai-rchivist](https://github.com/xgillard/ai-rchivist) qui permette de demander
aux différents LLMs de réaliser l'extraction de données via du prompting.

J'ai fait plusieurs démonstrations, et les gens étaient à chaque fois
hyper enthousiastes. Les éléments qui me semblaient revenir le plus
souvent étaient ceux-ci:

* le système permet d'avoir très rapidement, des résumés du texte
  transcrit dans 4 langues (fr, nl, en, de). Si les résumés
  n'étaient pas parfaits (présence d'hallucinations), ca reste une
  feature qui est très appréciée et demandée.

* il est possible d'éditer tous les champs, partout dans
  l'interface web. Et le système est présenté comme une **aide**
  pour les archivistes (un outil qui les maintient dans leur role
  de garants de la qualité des données tout en leur permettant un
  traitement plus rapide -- au niveau de chaque pièce), et non pas
  comme une "menace": un outil qui viendrait les supplanter dans
  leur job.

* quelqu'un essaie d'utiliser les nouvelles technologies pour
  améliorer leurs conditions de travail.

Il y a eu plusieurs versions différentes de AI-rchivist (l'interface
web). La dernière version est la plus robuste et la plus simple à 
maintenir. Il s'agit d'un frontend programmé en Javascript avec
React et d'un backend programmé en python avec fastapi. Le fait de 
servir les (petits) LLM localement (sans passer par un provider cloud)
est réalisé en utilisant ollama.

Tout le code de ai-rchivist est packagé comme une application 
docker compose. Ce qui veut dire qu'en une commande, tu peux lancer ou
stopper les trois conteneurs nécessaires. Cela permet aussi de pouvoir
déployer l'application partout sans être confronté à des problèmes
liés à des versions incompatibles entre elles etc...

**Notes importantes** 
1. Il est généralement plus simple de travailler directement sous linux
    que depuis une machine Windows.
2. Si tu as accès à un GPU sur la machine que tu utilises pour déployer
    ai-rchivist, tu devras alors installer le 
    [toolkit nvidia pour docker](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)
3. Si tu n'as vraiment accès à aucun GPU sur la machine que tu 
    comptes utiliser pour déployer ai-rchivist, il te faudra supprimer
    les lignes suivantes dans le fichier `compose.yml`

    ```
    deploy:
      resources:
        reservations:
          devices:
            - capabilities: ["gpu"]
    ```

    Note cependant que n'avoir aucun GPU sur ta machine va être 
    problématique et te poser d'importantes limites. Même pour 
    faire tourner de petits LLM, le temps nécessaire va s'avérer 
    **très** long avant qu'une réponse ne soit disponible.


