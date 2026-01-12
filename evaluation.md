# Evaluation

Pour savoir ce qui marche, ce qui ne marche pas et ce qui améliore ou dégrade
les performances, il est important de faire une évaluation un peu formelle et
un peu structurée de chacune des étapes mais aussi du système dans son ensemble 
(end to end).


## Evaluer le modele digita (si on l'utilise)

Je pense qu'il serait intéressant de s'assurer que le modèle digita est ok.
Les métriques qui ont été prises pendant l'entrainement sont bonnes et tout
a l'air de s'être bien passé. Il faudrait toutefois s'assurer qu'on n'a pas
provoqué de _catastrophic forgetting_ lors du fine tuning. Pour ca, je pense
qu'il faudrait mesurer la performance de digita sur des bench de référence puis
les comparer avec les résultats rapportés par meta pour llama-8b.

En particulier, je pense que les benchmarks
[MMLU](https://huggingface.co/datasets/cais/mmlu) et
[IFeval](https://github.com/EleutherAI/lm-evaluation-harness/tree/main/lm_eval/tasks/ifeval)
sont intéressants à tester (par ex en utilisant le framework [lighteval de
huggingface](https://github.com/huggingface/lighteval)) car ils tentent de
mesurer les deux capacités dont on a besoin dans le cadre d'une application IA
telle que ask agatha. A savoir: la compréhension langagière et la capacité du
modèle à suivre des instructions.

Si tout s'est bien passé, les performances des deux modèles devraient être
à peu près similaires sur les différents benches. La grosse diff devrait apparaitre
si on créait un nouveau benchmark qui mesure spécifiquement la capacité à 
répondre à des questions liées aux archives (tq gérées par les arcgives de l'état).

## Evaluer le retrieval

**Cette étape nécessite de créer un dataset suffisamment grand qui reprenne
des questions et les fragments de textes à retrouver dans les bonnes sources**

On pourra ici utiliser des metriques assez classiques: precision@k, recall@k 
pour évaluer l'efficacité du retrieval. 

Il pourrait aussi être intéressant de mesurer la similarité cosinus de entre 
les chunks retrouvés et ceux de la ground truth.


## Si on entraine des classifier pour le routage

On pourra alors ce contenter de faire une simple évaluation sur base d'un 
précision/recall et F1. **Mais ca veut aussi dire qu'il faudra constituer un 
dataset de contextes, et avec en vis a vis le choix à poser dans chaque cas**.


## Evaluer la performance du modèle sur les étapes individuelles

Pour les étapes lors desquelles on demande au modèle de faire une synthèse, 
de résumer, planifier ou produire du contenu: on peut évaluer la performance
de deux façons: 

1. soit on a une ground truth et on peut utiliser des métriques comme BLEU & ROUGE.
2. soit on peut utiliser un LLM as judge pour donner un score aux réponses qui
    sont données.

Pour les étapes qui répondent à une question (partielle) de l'utilisateur, il
serait sans doute utile d'appliquer un framework de test pour un RAG complet
tel que [RAGAS](https://docs.ragas.io/en/stable/)


## Evaluer ssytème complet (end to end)

On peut procéder exactement de la même façon que pour évaluer une étape
générative individuelle (BLEU & ROUGE si possible) + RAGAS.

### Trajectoire

Mais comme c'est un système qui est agentique, il serait aussi intéressant de
faire une analyse de trajectoire complète (càd, voir que le système a appelé 
les bons outils avant d'arriver à la conclusion finale). On peut procéder de
plusieurs façons pour faire ce test: 

- trajectoire exacte (bons outils, dans le bon ordre, le bon nombre de fois)
- overlap binaire (tous les bons outils ont été appelés; parmi tous les outils
    qu'il fallait appeler, combien ont effectivement été appelés)
