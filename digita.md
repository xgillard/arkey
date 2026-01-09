# Digita

Digita est un artefact qui a été produit dans le cadre du développement de
[ask agatha](./ask_agatha.md). Il se base donc sur le même cas d'utilisation
et le même corpus de données que ce qui a été expliqué dans l'autre section.
J'ai toutefois décidé d'en faire un élément à part car c'est un résultat qui 
est d'une nature un peu différente. En effet, il s'agit ici du fine tuning
(adapter QLoRA) de llama sur base du même corpus. On a donc un llm 8B quantisé
(déployable en local même en n'ayant qu'un tout petit GPU à disposition) qui 
aura tendance à répondre plus ou moins de la même façon qu'Annelies et 
Raphaelle lorsqu'on lui pose des questions relatives aux archives de l'Etat.

> Il faut toutefois bien garder à l'esprit qu'il ne s'agit que d'un modèle
> très simple et qu'il va répondre quelque chose "dans le même style" que 
> les réponse qu'il a vu pendant l'entrainement. Mais qui ne sera pas forcément
> correct ou pertinent pour la question posée.


## Pour utiliser ce modèle

Pour utiliser ce modèle, il suffit de procéder comme ceci: 

```python
from unsloth import (
    FastLanguageModel, 
    train_on_responses_only,
)
from unsloth.chat_templates import (
    get_chat_template,
)
from transformers import TextStreamer

model, tok = FastLanguageModel.from_pretrained("xaviergillard/digita", load_in_4bit=True)
model = FastLanguageModel.for_inference(model)
```


## Sur le repository

Sur le repository github, on trouvera une série de ressources utiles.
Principalement des notebooks jupyter que j'ai utilisé pour: 

1. [structurer les données](https://github.com/xgillard/corpus_digit/blob/main/data_curation.ipynb)
2. [labeliser des données](https://github.com/xgillard/corpus_digit/blob/main/2nd_curation.ipynb)
3. [filtrer et augmenter les données](https://github.com/xgillard/corpus_digit/blob/main/data_augmentation.ipynb)
4. [faire le fine tuning à proprement parler du modèle.](https://github.com/xgillard/corpus_digit/blob/main/fine_tuning.ipynb)

Mais aussi des scripts pour:
* [quantiser le modèles en gguf](https://github.com/xgillard/corpus_digit/blob/main/convert_to_gguf.sh)
* [pousser le modèle sur huggingface](https://github.com/xgillard/corpus_digit/blob/main/push_to_hub.sh)


## Entrainement

L'entrainement complet a été réalisé sur ma machine personnelle équipée d'un
GPU NVIDIA RTX5080 (16g de VRAM).

Tout le [code](https://github.com/xgillard/corpus_digit) nécessaire pour
reproduire ce modèle est disponible sur le repository
[xgillard/corpus_digit](https://github.com/xgillard/corpus_digit). Le
[model](https://huggingface.co/xaviergillard/digita) en lui même est rendu
disponible sur huggingface sous le nom
[xaviergillard/digita](https://huggingface.co/xaviergillard/digita).


### Données d'entrainement

Pour réaliser l'entrainemnt et l'évaluation du modèle, j'ai utilisé un ensemble
de 17217 conversations (20659 si on compte les données augmentées) provenant
des années 2024, 2023, 2022. 

Il est toutefois important de réaliser que ces données -- même si elles sont de
bonne qualité -- ne sont pas non plus "gold standard" mais plutôt "silver
standard" parce que c'est un llm qui a fait la découpe en conversation. C'est
encore un llm qui a fait le tagging utilisé pour le filtrage des données (en
vue de ré-équilibrer le dataset) et c'est aussi un llm qui a réalisé
l'augmentation des données en générant des conversations de synthèse.


### Métriques d'entrainement

Toutes les métriques d'entrainement sont disponibles
[ici](https://www.comet.com/xaviergillard/digita/view/new/panels).

