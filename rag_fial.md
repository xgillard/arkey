# RAG pour la faculté FIAL

Initialement, ce prototype a été développé parce que la faculté FIAL avait émis
une demande aux archives de l'université afin d'avoir un outil qui leur
permette d'interroger leurs archives (essentiellements PV de bureau, mais aussi
d'autres archives). L'objectif étant de pouvoir tracer l'historique des
décisions qui ont été prises et répondre rapidement aux questions de savoir
pourquoi une telle décision a été prise et comment le compromis a émergé.


### Note importante

Les archives dont il est question ne peuvent pas être rendues publiques car
elles contiennent des informations à caractères personnelles et le détail de
débats qui doivent rester dans un cadre purement privés. Sans ces garanties, il
y aurait un risque que les producteurs d'archives vident les PV de leur
substance soit lors de la rédaction des PV, soit avant le versement des
archives.


## Le prototype

Dans ce contexte, un prototype de plateforme RAG a été développé. Tout le code
de celui ci est disponible publiquement sur le repo github
[xgillard/rag](https://github.com/xgillard/rag). Ce repository (et le code)
sont normalement assez largement documentés donc il devrait être assez facile
de s'y replonger. Voici toutefois un bref apperçu de la structure de ce qui a
été fait.

Tout le code est conçu comme un docker compose, ce qui signifie qu'on peut lancer
tous les services et avoir une infrastructure cohérente en tapant une seule
commande `docker-compose up` (comme si le projet était un monolithe).

L'architecture comprend 3+1 services distincts: 

1. [database](https://github.com/xgillard/rag/uclouvain/database) qui fournit 
    une vector database basée sur postgres (+pgvector) permettant de stocker
    les chunks de textes et leurs embeddings. C'est cette base de données qui
    rend possible la recherche sémantique au sein du corpus de documents qui 
    a été traité.

2. [backend](https://github.com/xgillard/rag/uclouvain/backend) qui implémente
    la logique du RAG. Il s'agit d'un microservice implémenté en python avec
    [FastAPI](https://fastapi.tiangolo.com/) et qui expose les endpoints suivants:
    - **/chunks** qui sert plus à réaliser le chunking d'un document uploadé.
    - **/indexation** qui sert à faire l'indexation d'un document (càd: le 
        chunking + le calcul des embeddings et stockage dans la database).
    - **/retrieval** qui sert à montrer les chunks qui ont été retrouvés pour
        une requête donnée.
    - **/generation** qui sert simplement à générer le texte sur base d'un
        contexte (prompt + données retrouvées).
    - **/rag** qui fait non seulement la phase de retrieval, mais qui génère
        aussi une réponse à la question posée en utilisant les résultats trouvés
        dans la database.

3. [frontend](https://github.com/xgillard/rag/uclouvain/frontend) qui
   implémente une petite interface utilisateur en react afin de rendre le
    système utilisable pour un utilisateur non technique.


Le dernier service (le +1 que je mentionnais au dessus) pourrait être
d'utiliser une plateforme d'inférence locale pour servir le LLM, par exemple:
[ollama](https://ollama.com/),
[llama.cpp](https://github.com/ggml-org/llama.cpp), ou
[vllm](https://docs.vllm.ai/en/latest/). Durant la phase de développement/prototypage,
le plus simple est probablement de simplement utiliser ollama car c'est le 
plus facile à configurer. Lors d'un vrai passage en production, utiliser une 
plateforme comme vllm aurait sans doute plus de sens.

**TOUTEFOIS** dans son état actuel, ce dernier service n'est **pas utilisé**
car l'inférence du LLM est entièrement réalisé dans le backend **en utilisant
directement les librairies de huggingface**. Commme j'ai pas mal expérimenté
pour chercher à améliorer la latence de la génération de texte, le code contient
encode tout ce qu'il faut pour réaliser la génération en utilisant 
[ONNX Runtime](https://onnxruntime.ai/).

La codebase contient aussi un service
[tunnelling](https://github.com/xgillard/rag/uclouvain/tunnelling). Ce service
n'apporte en fait rien du tout au prototype. C'est juste un faux service que
j'utilisais pendant le développement afin de pouvoir me brancher directement
sur la database contenant les documents chunked et embedded sans avoir à les
recalculer ni à tout stocker sur ma machine.


## Etat d'avancement + Retours à propos du prototype.

Le prototype, tel qu'il est, a fait l'état d'une démonstration à la faculté.
Ce qui en est ressorti, c'est que: 

1. Les gens sont en fait assez peu intéressés par le fait d'avoir une réponse
    qui soit générée pour faire la synthèse de ce qui a été retrouvé et qui
    pourrait répondre à leur question. Ce qu'ils cherchent surtout, c'est 
    un système qui leur permette de retrouver efficacement les documents ou
    les paragraphes intéressants dans un document. En soi, ce n'est pas une
    trop mauvaise nouvelle car l'inférence d'un LLM en local et sans GPU rend 
    l'approche RAG impraticable. Un ElasticSearch ou Solr me semble donc plus
    approprié au besoin qu'ils ont exprimé.

2. Lors de cette réunion, il a aussi été décidé que les gens allaient collecter
    les questions qu'ils auraient aimé poser à l'outil en développement ainsi
    que les réponses que l'outil aurait du apporter (à posteriori). Le but étant
    de me créer un corpus que je puisse utiliser pour benchmarker la qualité
    des résultats obtenus. **Ce corpus n'a jamais été créé** et la liste des
    questions et réponses est toujours vide.

Depuis cette réunion, il n'y a plus eu aucun progrès sur ce prototype et mon 
attention s'est portée sur le problème de la mailbox digit des archives
([agr_agent](./agr_agent.md), [digita](./digita.md)).

## Mon avis a posteriori à propos du prototype

Je pense que ce prototype est bien trop simple et bien trop proche d'une 
approche RAG 'vanilla'. Si on veut faire en sorte que l'outil soit fonctionnel,
il me semble essentiel d'y incorporer une série de techniques plus avancées. 
Et notamment: 

1. **Query Expansion** pour générer N versions différentes de la question qui
    est exprimée afin de lancer N recherches dans la DB.

2. **Recherche plus intelligente dans la DB** il serait à mon avis utile de
    chercher à filtrer les records avant d'entamer la recherche sur base de 
    la similarité cosinus dans la DB. Ca éviterait de retrouver des chunks
    qui sont sémantiquement similaires mais pas pertinents pour la recherche.

3. **Alternativement à la query expansion** on pourrait faire le recherche 
    sémantique sur base d'une réponse putative. Cad, on demanderait à un LLM
    de halluciner N réponses possibles à la question posée puis trouver les
    chunks qui sont les plus proches de ces réponses putatives.

4. **Reranking** sur base des N * k chunks retrouvés par les requêtes qui ont
    été faites dans la db, utiliser un modele cross_encoder (voir
    [sentence_transformers](https://sbert.net/)) pour réordonner les chunks
    et ne garder que ceux qui sont le plus pertinents pour répondre à la question.

5. **Combiner avec une recherche hybride**. Comme ce qui intéresse le plus les
    personnes c'est une recherche plus classique qui peut se baser sur des
    mots clés précis, combiner les approches précédentes avec un moteur de 
    recherche plaintext comme solr me semblerait être une bonne idée.


#### Malheureusement

Malheureusement, la plupart des techniques de j'ai mentionné ci dessous nécessitent
d'avoir recours à un LLM (pas forcément un gros, pas forcément puissant, mais
**sans GPU c'est inenvisageable**).


#### Note technique à posteriori

A posteriori, je conseillerais de plutot réimplémenter le projet en utilisant
l'écosysteme de langchain. Une fois qu'on connait les autres bibliothèques c'est
super simple et comme les interfaces sont standardisées ca permet de changer 
plus facilement des modules sans tout casser.



