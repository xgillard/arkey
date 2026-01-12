# Vade Mecum pour la reprise de Arkey

Bonjour à toi qui vient de prendre le relai sur le projet ARKEY.  Le but de ces
quelques lignes n'est certainement pas de te dire quoi faire ou comment le
faire. Mon but est simplement de te présenter ce qui a déjà été fait, ce que
j'avais en tête de faire avant mon départ et les
[ressources](./ressources_utiles.md) que j'ai trouvé utiles pour pouvoir
avancer sur le projet. Bref, mon but est simplement de te permettre de démarrer
plus facilement et de pouvoir réutiliser mes travaux précédents si tu le
souhaites.

## Les objectifs poursuivis

Pendant les 2 premières années de mon travail sur ARKEY, j'ai essentiellement
travaillé sur les sujets suivants: 

* Comparer les performances de modèles génératifs avec les performances de 
    modèles task-specific pour l'extraction d'entité nommées.
* Developper des plateformes RAG pour accéder aux archives de l'université
    (faculté FIAL) ou des archives de l'état (via AGATHA).


## Les résultats (artefacts) produits

En pratique, la poursuite des deux objectifs mentionnés ici au dessus m'a 
permis de développer les artefacts (datasets, outils, ...) suivants:

* **Résultats liés à l'objectif 1 (comparaison LLM vs modèles task-specific)**
  - [un corpus de textes anciens](./corpus_textes_anciens.md)
  - [AI-rchivist](./ai_rchivist.md), une interface web pour l'extraction de données.
  - [Expériences consécutives à AI-rchivist](./experiences_airchivist.md)

* **Résultats liés à l'objectif 2 (plateformes rag)**
  - [rag fial](./rag_fial.md) 
  - [ask agatha](./ask_agatha.md) 
  - [digita](./digita.md)


* **Autres productions**
  - [labo ia & archives](./labo.md)


## Ce que je pensais encore faire

Les prochaines étapes que je comptais (mais que je n'ai à l'heure actuelle
pas encore eu l'occasion d') entreprendre avant de quitter le projet
Arkey sont les suivantes:

* réaliser une [évaluation formelle du systeme](./evaluation.md) qui a été développé (commencer par là)
* [intégration du moteur lié aux inventaires](./integration_inventaires.md) (sans doute la chose la plus utile à faire après l'évaluation)
* [intégration des jalons de recherche](./integration_jalons.md)
* utiliser [xaviergillard/digita comme llm local pour la rédaction des réponses](./digita_responses.md)
* [entrainer des classifiers sur base de simple "bert-like" models afin
  de réaliser l'aiguillage des requetes plutot que d'avoir recours à des llm](./bert_like_routing.md)
