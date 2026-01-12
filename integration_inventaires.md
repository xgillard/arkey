# Intégration des inventaires

Contrairement à ce qu'on pourrait penser, les inventaires d'archives ne se
contentent pas de faire un inventaire, un listing des documents repris dans un
fonds. Il s'agit en fait d'un travail beaucoup plus riche qui permet de
récupérer les informations suivantes:

* une présentation du producteur d'archives
* une présentation sous forme de texte continu du **contenu** d'un fonds.
* les conditions éventuelles pour pouvoir accéder au contenu des archives de ce fonds
* d'autres sources à consulter pour compléter les informations du fonds
* et enfin, un inventaire hiérarchique des pièces qui sont reprises dans le fonds.

> **Il s'agit donc d'une source particulièrement intéressante parce qu'elle 
> constitue généralement le point de départ d'une recherche archivistique 
> efficace !**

## A tester pour l'intégration des inventaires

Je vois essentiellement trois choses à tester pour l'intégration des inventaires
dans ask-agatha:

1. inclure l'outil de recherche d'inventaires en tant que tel et offrir la 
    possibilité aux gens de dire qu'ils veulent retrouver un fonds d'archive
    en particulier.

2. comme il y a environ 20 000 inventaires, et qu'on n'en rajoute pas tous les
    jours, il pourrait être intéressant d'implémenter une recherche sémantique
    sur la description des inventaires. Ca permettrait potentiellement 
    d'orienter l'utilisateur dans la bonne direction même si la question est
    mal formulée. On peut aussi imaginer d'implémenter cette recherche sémantique
    comme une recherche sémantique hiérarchique: 
    1. description de groupes d'inventaires (à générer, utile uniquement si 
        chercher directement parmi les descriptions impacte trop la latence
        car la recherche sémantique est essentiellement O(n) mais on pourrait
        aussi mitiger le probleme en utilisant une recherche approximate nearest
        neighbor plutot qu'une recherche exacte. Ce qui permettrait d'implémenter
        un index sur la db vectorielle -- par ex: hnsw).
    2. description des inventaires 
    3. description des groupes de pièces au sein de chaque inventaire.

3. la dernière chose (qui n'exclut ni la 1e méthode, ni la seconde) consiste
    à modifier le prompt système (voire carrément le graphe d'exécution -- mais
    ca, ca ne me semble pas spécialement être une bonne idée) pour que le bot
    soit au courant du fait que chercher dans les inventaires pour trouver s'il
    y a un fonds qui correspond à une thématique donnée peut être une bonne
    façon de commencer la recherche. Mais aussi de la terminer dans le cas
    où aucune ressource ne serait en ligne. (Dans ce cas, il faut alors rediriger
    l'utilisateur vers le dépôt dans lequel se trouve le fonds).

