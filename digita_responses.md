# Utiliser le modele digita pour les réponses de Agatha

Comme ça a déjà été évoqué [par ailleurs](./digita.md), le modèle digita a été
entrainé sur un corpus d'environ 17 000 emails de réponses à des question
d'utilisateurs des archives. Lorsqu'on réalise quelques tests, on se rend 
compte que si le modèle a bien incorporé des "connaissances" liées au fonctionnement
des archives, les réponses qu'il donne sont assez "pré-2024" en ce sens qu'il
mentionne encore search.arch.be (qui n'existe plus) plutôt qu'agatha.arch.be
qui serait la bonne plateforme. 

## Exemple

> **Question**
> Comment on fait pour trouver l'acte de naissance de mamy nicole qui est née
> à oultremont pendant les 2e guerres napoléoniennes? Je pige rien à votre
> nouveau brol agatha.

>> **Réponse** Bonjour Jean-Claude,
>>
>> Notre site web https://search.arch.be comprend plusieurs modules de
>> recherche et permet plusieurs méthodes de recherche.
>> 
>> 1. Module Personnes Il permet de faire des recherches dans une base de
>>    données décrivant des actes d'état civil, des registres paroissiaux, des
>>    minutes notariales. Attention: cette base est alimentée par des
>>    bénévoles, les Archives de l'Etat n'en contrôlent et n'en garantissent
>>    donc pas la qualité du contenu. Par ailleurs, les bénévoles choisissent
>>    les communes ou paroisses
>>    qu'ils souhaitent traiter. La base de données ne couvre absolument pas
>>    tous les actes conservés dans notre institution, loin de là. Il n'est pas
>>    possible d'imprimer ni de télécharger les fiches. Il est fort probable
>>    que, pour le moment, vous ne trouviez pas encore de fiches relatives aux
>>    personnes qui vous intéressent.
>> 
>> 2. Via la page d'accueil vous pouvez directement rechercher des images des
>>    registres paroissiaux et des actes d'état civil depuis les liens « Aperçu
>>    des registres paroissiaux »
>>    (https://search.arch.be/fr/themes/jalon/509-registres-paroissiaux-fr) et
>>    « Aperçu des registres d'état civil »
>>    (https://search.arch.be/fr/themes/jalon/518-etat-civil-fr). Suivez
>>    l’arborescence qui est définie par province, arrondissement, commune,
>>    type d'acte, dates. Attention : seuls les actes datant de plus de 100 ans
>>    sont mis en ligne. Par ailleurs, tous les registres ne sont pas encore
>>    numérisés. De plus, pour visualiser les images, vous devez être connecté
>>    avec votre compte utilisateur (identifiant + mot de passe). Vous ne
>>    pouvez pas imprimer ni télécharger les documents. Pour en obtenir une
>>    copie payante (papier ou numérique), vous devez en faire la commande par
>>    email aux Archives de l'Etat de la province concernée. Si le registre qui
>>    vous intéresse n'est pas en ligne, c'est aussi à ce service qu'il vous
>>    faudra commander la copie payante de l'acte.


Etant donné cela, il y a deux choses qu'on peut envisager: 

1. se dire que le modèle ne correspond pas à l'usage pour lequel on veut 
    l'utiliser et qu'il faut donc nécessairement le ré-entrainer.
    (ce qui devrait être possible bien que le volume de données postérieures
     à l'introduction de agatha me semble trop faible que pour fine-tuner
     un petit LLM - _8B_). Cette petite quantité de donnée pourrait être: 

     * soit laissée telle quelle. Il suffirait alors de fine tuner un plus gros
       LLM (paradoxalement lorsqu'on a peu de données, le fine tuning d'un gros
       LLM donne de meilleurs résultat qu'avec un petit). Mais ca augmenterait
       le cout à la fois du training et de l'inférence.

    * soit augmentée en utilisant un nouveau dump des données qui puisse prendre
        en compte les années 2024 et 2025. C'est probablement la meilleure 
        option, mais cela nécessitera une certaine quantité de travail manuel.

    * soit augmentée artificiellement en utilisant le script d'augmentation de
        données que j'ai écrit (mais le risque est ici de se retrouver avec 
        un modèle dégénéré qui overfitte les réponses de la ground truth).


2. se dire que le décalage qui existe entre le modèle qui a été entrainé et la
    nouvelle réalité du terrain n'est pas catastrophique. Qu'on va combler le
    problème via du prompting et en le groundant les réponses grace à des 
    fragments issus de sources authentiques.

    Il me semble que cette approche vaudrait la peine d'être expérimentée 
    (et comparée à un 2e entrainement du modèle sur des données plus récentes).

    En effet, un des gros avantages du système ask agatha tel qu'il a été conçu
    jusqu'à présent, c'est que toutes les interactions llm sont indépendantes.
    De ce fait, on peut changer le modèle utilisé lors de chacune des étapes
    ce qui veut aussi dire qu'on peut utiliser des modèles plus spécialisés 
    pour accomplir des taches qui sont elles aussi plus spécialisées. Dans le
    cas qui nous occupe, digita est un modèle qui a été spécialisé pour donner
    des réponses semblables à celles qu'on peut lire dans la mailbox digit.
    On pourrait donc arguer que digit est très probablement un bon candidat
    pour accomplir la phase finale du travail, lors de laquelle le modèle
    rédige une réponse à destination de l'utilisateur.


## DPO

Il y a en fait une troisième approche qu'on pourrait considérer. En effet,
cette dernière approche consisterait à se dire que digita constitue une bonne
base de travail, mais qu'il faut juste lui apprendre qu'on préfère avoir des
réponses contenant des données actuelles. Ce qui peut se faire avec une
optimisation directe des préférences DPO. Il s'agirait alors de créer un
nouveau corpus de texte constitué de triplets (question, reponse-pre-agatha,
reponse-post-agatha) et faire une courte phase d'entrainement (1 epoch) dans
laquelle on encourage le LLM à donner des réponses qui sont cohérentes avec la
nouvelle réalité (agatha existe, elle remplace les anciens systèmes, et c'est
mieux ainsi).

