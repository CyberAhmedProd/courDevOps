Maintenant que vous avez compris l'intérêt des branches dans un contexte local et que vous savez les manipuler, nous allons étudier un peu plus en détail dans ce chapitre la notion de _branches distantes_.

Création de branche distante
----------------------------

En fait, c'est une notion que nous avons déjà rencontrée dans le paragraphe ['Utilisez un dépôt distant'](../distant/). En effet, nous avions commencé par utiliser la commande `git remote` qui permet d'enregistrer dans le dépôt local l'adresse du dépôt distant sous forme de nom court :

    $ git remote add origin https://github.com/cristal-binome1/tpgit.git
    

'origin' peut ainsi être utilisé à la place de 'https://github.com/cristal-binome1/tpgit.git'. Ouf !

Remarque: 'origin' est usuellement choisi mais nous aurions pu utiliser n'importe quel nom.

Ensuite nous avons poussé la branche 'master' sur le dépôt distant 'origin' :

    $ git push -u origin master
    

Cela a eu pour effet de:

*   créer une nouvelle référence 'origin/master' sur le dernier commit de 'master'
*   créer une branche nommée 'master' sur le dépôt distant 'origin'.
*   copier (par ssh) tous les commits de la branche locale 'master' sur la branche 'master' du dépôt 'origin'.

Cela est vrai pour le 'git push' initial. L'utilisation ultérieure de 'git push' ne fera que copier les nouveaux commits sur le dépôt distant.

Clonage d'un dépôt distant
--------------------------

Nous avons par ailleurs utilisé la commande `git clone` qui permet de récupérer un dépôt distant :

    $ git clone https://github.com/cristal-binome1/tpgit.git
    

Cette commande procède aux actions suivantes :

*   l'adresse du dépôt distant est enregistrée sous forme du nom court 'origin'
*   une branche 'master' est créée en local, elle référence le commit le plus récent
*   la référence 'origin/master', correspondant à l'état actuel de la branche 'master' sur 'origin', est créée.

Qu'est qu'une branche distante ?
--------------------------------

'origin/master' est une référence vers ce qu'on appelle une _branche distante_.

Ce terme est en fait un abus de langage : avec Git **TOUT** est enregistré dans le dépôt local, et Git ne travaille **QUE** sur des commits locaux.  
Lors d'un `git clone` **TOUT** l'historique existant dans le dépôt distant est recopié tel quel : vous en avez une copie _parfaite_ en local.

Répétons-le, il n'y a pas à proprement parler de branches locales et de branches distantes. Tout est local. Les commandes `git clone/pull` ne font qu'échanger des commits avec un dépôt distant pour les placer dans une branche stockée localement, c'est à dire une séquence de commits avec une référence pointant sur leur tête. 'origin/master' est la référence vers la tête de cette séquence, que **nous nous représentons** comme étant une branche.

Les branches _dîtes distantes_ sont cependant gérées de manière spécifique, toutes les commandes Git ne leur sont pas applicables. Elles ne sont modifiables que par les commandes `git push/pull` que nous connaissons déjà et par une autre que nous allons étudier dans ce chapitre : `git fetch`.

Récupérer les modifications du dépôt distant
--------------------------------------------

Pour mettre à jour notre version locale d'une branche avec les modifications apportées par notre collaborateur, nous avions recours à la commande `git pull`. Cette commande est en fait un raccourci qui correspond à l'exécution de 2 commandes :

1.  `git fetch`: ajoute en tête de la branche (locale !) 'origin/master' les commits supplémentaires de la branche 'master' du dépôt 'origin' et déplace la référence 'origin/master' sur le dernier de ces commits (équivalent d'un `git reset`)
2.  `git merge origin/master` alors que vous êtes dans 'master'

On voit bien ici le mécanisme en 2 temps : synchronisation de l'historique d'une branche _spéciale_, dite _distante_, avec l'historique d'un dépôt distant, puis fusion de cette branche dans une branche _classique_ pour pouvoir y effectuer des modifications.

L'intérêt d'utiliser `git fetch` plutôt que `git pull` est de garder le contrôle sur la manière d'intégrer les modifications dans votre branche locale. Avec `git pull`, ce sera forcément une fusion avec la création éventuelle d'un commit de fusion.  
Il y a un autre avantage à procéder d'abord à un `git fetch` : cela vous permet _d'inspecter_ ce que vous venez de récupérez (`git log origin/master` ou `git diff origin/master`) avant de l'intégrer à votre branche 'master'.

Prenez donc plutôt l'habitude de faire un `git fetch` -> `git rebase origin/master` plutôt que d'utiliser un `git pull`.

Visualisons cela avec **explainGit**. Votre collaborateur vient de pousser un commit sur le dépôt distant. Votre dépot local est donc en retard d'un commit :

Effectuez un `git pull`.  
_Résultat_ : 'origin/master' est mis à jour avec le commit 'a278ab4' et fusionné sur 'master'.

Faisons la même chose en 2 temps avec `git fetch` et `git merge`:

Réinitialisez (`/reset`) et effectuez un `git fetch`.  
_Résultat_ : seule la mise à jour de 'origin/master' est effectuée.

Effectuer un `git merge origin/master`.  
Vous vous retrouvez dans me même état que précédemment.

Remarquez qu'il s'agit d'une fusion 'fast-forward'. En effet, comme vous n'aviez pas créé de commit de votre côté, la fusion revient juste à ajouter le nouveau commit du dépot distant (a278ab4) sur votre dernier commit (ee5df4b).

Maintenant regardons ce qu'il se passe si vous aviez créé un commit de votre côté:

Réinitialisez (`/reset`) et effectuez un `git commit`.  
_Résultat_ : votre branche master locale distante et la branche master sur le dépot distant divergent.

Effectuez un `git fetch`.  
_Résultat_ : seule la mise à jour de 'origin/master' est effectuée.

Vous pouvez alors choisir de fusionner comme avec `git pull` ou, si vous ne voulez pas encombrer l'historique avec un commit de fusion, vous avez la possibilité de _rebaser_ 'master' sur 'origin/master'.

Effectuez un `git rebase origin/master`.  
_Résultat_ : votre commit est recréé sur 'origin/master' comme s'il avait été créé après le `git fetch`.

Effectuez un `git push` pour mettre à jour le dépôt distant avec votre version.

Remarque: il possible de demander à `git pull` de faire un rebasage plutôt qu'une fusion en utilisant l'option `--rebase`. A vous de choisir ce que vous préférez utiliser.

Suivi de branche distante
-------------------------

Les commandes

    $ git push -u origin master
    

et

    $ git clone https://github.com/cristal-binome1/tpgit.git`
    

que nous avons utilisées jusqu'à présent ont permis de créer un lien fort entre les branches 'master' locale et distante.  
Ces deux commandes ont en effet enregistré dans le fichier .git/config le _lien_ que vous avez défini entre votre branche 'master' et la branche du dépôt distant (regardez le contenu de ce fichier, en particulier les enregistrements 'remote' et 'branch').

C'est le comportement par défaut de `git clone` ; par contre pour `git push`, il faut utiliser l'option `-u` la première fois, si un `git clone` n'a pas été fait précédemment.

L'enregistrement de ce _lien_ permet d'utiliser ultérieurement simplement `git push`, `git fetch` ou `git pull` sans préciser ni le dépôt distant ni la branche distante. On dit alors que la branche 'master' locale suit la branche 'master' du dépôt distant 'origin'.

(Pour rappel : en fait, tout passe par une branche locale intermédiaire, 'origin/master', qui est synchronisée avec le dépôt 'origin'.)

Ce _lien_ n'est cependant pas figé. Il n'est là que pour vous simplifier le travail. Il est tout à fait possible, à tout moment, de pousser la branche 'master' sur une autre branche ayant un nom différent. Par exemple, pour pousser la branche 'master' sur une branche 'masterdistante' du dépôt 'origin', vous pouvez utiliser la commande :

    $ git push origin master:masterdistante
    

Remarque : l'option `-u` n'est ici pas utilisée, nous ne voulons pas enregistrer le _lien_. Le comportement par défaut de `git push` (sans paramètres) sera encore de pousser 'master' sur 'master' de 'origin'.

Toutes vos branches peuvent être poussées sur le dépôt distant. Par exemple, si vous voulez pousser la branche locale 'wip' sur une branche distante du même nom, et conserver le _lien_, utilisez :

    $ git push -u origin wip
    

Les fois suivantes, si vous êtes sur votre branche 'wip' et que vous effectuez un `git push`, alors elle sera poussée sur la branche 'wip' du dépôt 'origin'.

Cela peut être pratique si vous avez besoin de travailler à plusieurs sur une nouvelle fonctionnalité, développée dans une branche spécifique (voir [Exemples de workflow](../workflows/)).

Plusieurs dépôts
----------------

De la même manière que vous pouvez utiliser plusieurs branches distantes, Git vous permet aussi d'utiliser plusieurs dépôts distants. Cela peut-être intéressant, par exemple, d'avoir un dépôt privé sur lequel vous et vos collègues allez souvent pousser et un autre dépôt sur lequel vous n'allez poussez que les livrables (voir le dernier paragraphe du chapitre [Exemples de workflow](../workflows/)).

On définira alors 2 dépôts distants 'interne' et 'livraison', à l'aide de :

    $ git remote add interne <url_depot_prive>
    $ git remote add livraison <url_depot_livrables>
    

On définira, par exemple, une branche 'dev' que l'on liera au dépôt 'interne' :

    $ git checkout -b dev
    $ git push -u interne dev
    

et une branche 'releases' que l'on liera au dépôt 'livraison' :

    $ git checkout -b releases
    $ git push -u livraison releases
    

Suppression de branches distantes
---------------------------------

Dans la branche 'wip', que vous avez partagée avec le reste de l'équipe via un dépôt distant, le travail est fini et il a été fusionné dans la branche principale. La branche 'wip' n'a plus de raison d'être, vous voulez la supprimer.

Pour supprimer la branche locale, c'est très simple :

    $ git branch -d wip
    

Par contre, comment faire pour supprimer la branche 'wip' sur 'origin' ?  
Pour cela, nous allons utiliser la commande `git push` de manière un peu spéciale.  
Rappelons sa forme générale :

    git push [depotdistant] [branchelocale]:[branchedistante]
    

Pour supprimer la branche 'wip' du dépôt distant 'origin', il faut pousser dans cette branche ... **rien** !  
Cela se fait simplement en utilisant :

    $ git push origin :wip
    

Remarquez qu'à la place de '\[branchelocale\]' juste avant les ':' on ne met **rien** .

var explaingit\_content = \[ { name: 'Fetch', height: 350, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: 'ee5df4b', parent: '7eb7654', tags: \['master', 'origin/master'\]} \], originData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: 'ee5df4b', parent: '7eb7654'}, {id: 'a278ab4', parent: 'ee5df4b', tags: \['master'\]} \] } \];