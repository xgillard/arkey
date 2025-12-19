# Resources Utiles

Le but ici n'est évidemment pas de te faire une bibliographie exhaustive de 
tous les livres et articles que j'ai lu, de tous les cours que j'ai suivi,
ou de toutes les technologies que j'ai testé. L'idée ici, c'est vraiment juste
de te donner les pointeurs vers les trucs qui m'ont été réellement utiles.
Certains sont plus _fondamentaux/théoriques_ que d'autres, mais c'est la 
combinaison des deux qui m'a permis de développer une suffisamment bonne 
compréhension pour ne pas me dire que je testais des choses à l'aveugle.

## Natural Language Processing

### Livres

J'ai trouvé que les bouquins suivants étaient vraiment intéressants et utiles
pour comprendre à la fois la théorie du NLP et la mise en pratique dans des 
systèmes réels.

* "Natural Language Processing with Transformers", 
    Lewis Tunstall, Leandro von Werra, and Thomas Wolf -- O'Reilly, 2022. 
    ISBN: 978-1-09813-679-6
* "LLM Engineer's Handbook", 
    Paul Iusztin, and Maxime Labonne -- Packt, 2024. 
    ISBN: 978-1-83620-007-9
* "Learning Langchain",
    Mayo Oshin, and Nuno Campos -- O'Reilly, 2025. 
    ISBN: 978-1-83620-007-9
* "Speech and Language Processing. (2nd ed)", 
    Dan Jurafsky, and James H. Martin -- Pearson, 2009. 
    ISBN: 978-0135041963. 
    
    Celui-ci reste une excellente référence même s'il commence à vieillir un 
    peu.


### Cours en ligne

En plus des livres, les cours suivants ont été assez intéressants. Parmi ceux-ci, 
certains cours sont gratuits (ceux sur deeplearning.ai, et les cours de Andrej 
Karpathy sur youtube), mais d'autres sont payants (coursera).

* "Natural Language Processing" ([spécialisation sur coursera](https://www.coursera.org/specializations/natural-language-processing))
* "Quantization Fundamentals with Hugging Face" ([short course gratuit](https://learn.deeplearning.ai/courses/quantization-fundamentals?startTime=0) sur deeplearning.ai)
* "Quantization in Depth" (encore un [short course gratuit](https://learn.deeplearning.ai/courses/quantization-in-depth?startTime=0) sur deeplearning.ai)
* ["Let's build ChatGPT: from scratch, in code, spelled out", Andrej Karpathyi](https://youtu.be/kCc8FmEb1nY?si=UX62dTXIQLsBQ4rr)


### Leaderboards & benchmarks

* [MMLU Leaderboard](https://www.kaggle.com/benchmarks/open-benchmarks/mmlu) 
    Pour comparer "à la grosse louche" la performance attendue de LLM génératifs
* [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard)
    Pour comparer et choisir les modèles d'embedding à tester.
* [IFEval Leaderboard](https://huggingface.co/spaces/Krisseck/IFEval-Leaderboard)
    Pour comparer la performance de modèles en matière d'instruction following 
    (encore plus nécessaire comme on travaille avec de petits modèles)
* [Alpaca Eval](https://tatsu-lab.github.io/alpaca_eval/) 
    Idem (même si je l'ai moins utilisé).
* [GAIA Leaderboard](https://huggingface.co/spaces/gaia-benchmark/leaderboard). 
    Finalement je n'ai pas utilisé ce LB là car les tools fournis on top of 
    AGATHA sont trop complexes (même en branchant gemini-3-pro-preview). 
    Ma solution a donc consisté découper le travail en petites tâches et de
    les orchestrer avec langgraph.

### Documentation
* La documentation de huggingface
* La documentation de sentence-transformers ([sbert.net](https://sbert.net))
* La documentation de langchain et langgraph
* La documentation de Ollama (très pratique pour prototyper)


## Deep Learning 

Voici qq bouquins que j'aime bien consulter régulièrement et qui s'avèrent 
encore régulièrement être assez utile:

* "Hands-on Machine Learning with Scikit-Learn, Keras & Tensorflow (3rd ed)", 
    Aurélien Géron -- O'Reilly, 2025. 
    ISBN: 978-1-098-12597-4
* "Fluent Python (2nd ed)", 
    Luciano Ramalho -- O'Reilly, 2022. 
    ISBN: 978-1-492-05635-5
* "Analyse de données avec Python", 
    Wes McKinney -- O'Reilly, 2021. 
    ISBN: 978-2-41206-918-9


## Outils / Bibliothèques

* L'écosysteme huggingface: transformers, datasets, accelerate, peft,
    bitsandbytes, sentence-transformers (attention, la [documentation](https://sbert.net/) 
    ne se trouve pas directement sur le site de huggingface mais sur un site 
    dédié).
* unsloth pour le fine tuning de llm génératifs (adapters QLoRa & sauvegarde
    mergées en GGUF pour inférence locale)
* ONNX runtime: pour optimiser l'inférence de certains modèles. (Attention:
    c'est bien, mais parfois un peu fragile ou difficile à faire fonctionner).
* Ollama: Fait le serving de LLM quantisés. C'est **le plus simple à utiliser
    et je te conseille de continuer avec ça**.
* Langchain: pour abstraire les interactions LLM, intégrer des outils externes
    (eg. pour la collecte et chunking des données), pour le post-processing 
    avant ingest dans la vector DB
* Langgraph: pour concevoir, organiser et orchestrer des flux complexes
    d'interaction avec LLM
* Langsmith: pour observer les résultats, évaluer les prompts et diagnostiquer
    les erreurs
* Postgres + [PgVector](https://github.com/pgvector/pgvector): Vector Database
    assez robuste
* ChromaDB: Vector Database assez light. Parfaite pour prototyper en local
* Docker-Compose & Docker: pour éviter les dependency hell & faciliter le
    déploiement de systemes comprenant plusieurs sous-systtemes.
* [uv](https://github.com/astral-sh/uv): gestion de projet & dépendances
    efficace en python.
* React pour tout ce qui est développement frontend un peu avancé
* Streamlit pour offrir un prototype de frontend avec très peu d'efforts.
* Labelbox pour organiser la création de corpus taggués par des collaborateurs
    non-techniciens


