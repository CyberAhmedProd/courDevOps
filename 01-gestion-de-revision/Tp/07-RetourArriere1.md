Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `tpfiles/tpgit-correction` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -rf tpgit`  
`$ cp -rf ~/tpfiles/tpgit-correction tpgit`

Revenir en arrière avec revert
------------------------------

Dans le chapitre [Revenir en arrière (1)](./05-RetourArriere1.md), nous avons vu une première méthode permettant de _renverser_ un commit : le fichier **hello.py** supprimé lors d'un commit a été _retrouvé_ avec `git checkout`, puis remis dans le dépôt avec un `git commit`.

Cette manipulation consiste donc à créer un commit qui inverse les modifications introduites par un ancien commit, afin qu'au final elles soient annulées dans la version courante du dépôt. C'est une opération classique, qui porte le nom de _revert_ en anglais.

Git dispose de la commande `git revert` pour effectuer en une seule opération ce que nous avions fait dans [Revenir en arrière (1)](./05-RetourArriere1.md).

Votre historique actuel doit être semblable à :

    b4c55d8 (HEAD -> master) Mise à jour de la doc suite à la refonte
    ba286f3 Refonte totale
    081c474 suppression fichier2
    4440471 renommage fichier1 -> fichier2
    3e348d4 Ajout fichier1
    f1fa39c Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Nous allons donc inverser le commit 'ba286f3' à l'aide de (à adapter à votre historique) :

    $ git revert ba286f3
    

Git va créer un nouveau commit et vous demande donc d'écrire le commentaire associé (un commentaire par défaut est proposé). Une fois celui-ci enregistré, vous pourrez constater que **hello.py** est revenu !

Votre historique est donc maintenant :

    4ed1962 (HEAD -> master) Revert "Refonte totale"
    b4c55d8 Mise à jour de la doc suite à la refonte
    ba286f3 Refonte totale
    081c474 suppression fichier2
    4440471 renommage fichier1 -> fichier2
    3e348d4 Ajout fichier1
    f1fa39c Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Amender le dernier commit
-------------------------

Le dernier commit restaure donc le fichier **hello.py**. Ce commit ne vous convient cependant pas totalement parce que vous aviez également mis le fichier **README.md** à jour pour indiquer que vous étiez en cours de refonte de votre projet.

Commencez par modifier votre **README.md** pour commenter différemment la refonte en cours. `git status` vous indique qu'il y a une modification non encore validée. Pour différentes raisons, vous voulez ajouter cette modification directement dans le même commit que celui qui _renverse_ la suppression de **hello.py**.

Avec ce que vous connaissez pour l'instant, il faudrait supprimer le dernier commit pour le recréer en utilisant la suite de commandes suivantes (ne le faîtes pas) :

1.  `git reset HEAD^`
2.  `git add hello.py README.md`
3.  `git commit` (avec un nouveau commentaire)

Ce type d'opération consistant à _amender_ le dernier commit est assez standard, et Git propose de la réaliser en une seule action avec l'option '--amend' de `git commit`.

Ajoutez d'abord **README.md** à l'index, puis exécutez le commit avec amendement :

    $ git add README.md
    $ git commit --amend
    

Vous êtes invités à modifier le message du dernier commit. Faites-le et constatez avec `git lg` que Git a remplacé le dernier commit (son identifiant est différent) et avec `git show HEAD` constatez que la modification de **README.md** a bien été prise en compte en plus de la réintégration de **hello.py**.

Votre historique devrait ressembler à cela:

    e8a2f06 (HEAD -> master) Annulation de la refonte (code + doc)
    b4c55d8 Mise à jour de la doc suite à la refonte
    ba286f3 Refonte totale
    081c474 suppression fichier2
    4440471 renommage fichier1 -> fichier2
    3e348d4 Ajout fichier1
    f1fa39c Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Cas fréquents d'amendement
--------------------------

Il y a 2 cas fréquents qui amènent à devoir amender le dernier commit :

1.  Vous avez oublié d'ajouter les modifications d'un fichier dans le commit :  
    Faîtes le `git add` nécessaire, puis un `git commit --amend`
2.  Vous voulez modifier, après coup, le commentaire du commit :  
    Faîtes un `git commit --amend`

Il existe un autre cas, plus rare : il est possible lorsque vous faîtes un commit de spécifier le nom de l'auteur des modifications (si ce n'est pas vous) à l'aide de la commande `git commit --author="son nom<son@adresse.mail>"`.

Assez souvent dans ce cas, on oublie (par habitude de faire un simple `git commit`) de spécifier le nom de l'auteur.  
On peut alors corriger cet oubli par amendement : `git commit --amend --author="son nom<son@adresse.mail>"`

**ATTENTION :** Cette commande modifie l'historique. Nous insistons une fois de plus : à ne pas utiliser sur un historique partagé !

Pour aller plus loin...
-----------------------

Si vous voulez vous exercer d'avantage sur la manipulation des commandes abordées dans les chapitres précédents, allez à l'exercice [Dans la peau de Brassens](../brassens2/).