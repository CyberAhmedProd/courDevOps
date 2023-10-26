Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-ignore` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-ignore tpgit`

Refonte totale !
----------------

Vous décidez de procéder à une refonte totale de votre programme en commençant par le supprimer :

    $ rm hello.py
    $ git rm hello.py
    

ou plus simplement (`git rm` supprimant également le fichier si il existe) :

    $ git rm hello.py
    

Commitez cette suppression.

Modifiez le fichier README.md pour expliquer la suppression et commitez le.

Votre historique devrait être le suivant :

    b4c55d8 (HEAD -> master) Mise à jour de la doc suite à la refonte
    ba286f3 Refonte totale
    081c474 suppression fichier2
    4440471 renommage fichier1 -> fichier2
    3e348d4 Ajout fichier1
    f1fa39c Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Cette suppression de **hello.py** est un peu brutale, non ? Vous auriez peut-être voulu garder une trace de votre programme... et vous n'avez pas fait de fichier .old ! Ni un répertoire d'archive !

Grâce à Git, vous pouvez consultez très facilement les anciennes versions de votre dépôt à l'aide de la commande `git checkout`, puisqu'elle permet de remettre le contenu du répertoire de travail dans un état précédent.

Repérez l'id du commit précédant la suppression du fichier hello.py grâce à `git log` (081c474, en ce qui nous concerne), et procédez au déplacement de 'HEAD' (ignorez le message cryptique que vous donne Git, pour l'instant) :

    $ git checkout 081c474
    

Regardez le contenu de votre répertoire : le fichier hello.py est à nouveau présent. Vous pouvez, à nouveau, visualiser son contenu.

Si vous affichez l'historique, vous pouvez constater que tous les commits suivants ont _disparu_ (tout du moins en apparence). Pour les _retrouver_, nous savons comment procéder : il faut replacer 'HEAD' sur la tếte de l'historique enregistré dans le dépôt :

    $ git checkout master
    

Restaurer un fichier
--------------------

Avec `git checkout`, vous pouvez aussi récupérer un état particulier d'un fichier donné. Cela va nous permettre de _récupérer_ notre fichier **hello.py**, en utilisant (à adapter à votre historique) :

    $ git checkout 081c474 hello.py
    

Littéralement, cela signifie : _met dans le répertoire de travail le fichier hello.py dans l'état où il était au commit `081c474`_.

`git status` montre que la commande précédente a mis la modification dans l'index. Vous pouvez maintenant faire un nouveau commit pour restaurer complètement le fichier.

**Note :** Nous poursuivrons les manipulations sur ce dépôt dans le chapitre [Correction de commits](../correction/). Ne le supprimez donc pas, et ne le modifiez pas d'ici là.

Votre historique devrait maintenant ressembler à cela:

    d563361 (HEAD -> master) Restauration du programme hello
    b4c55d8 Mise à jour de la doc suite à la refonte
    ba286f3 Refonte totale
    081c474 suppression fichier2
    4440471 renommage fichier1 -> fichier2
    3e348d4 Ajout fichier1
    f1fa39c Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Aller plus loin : recherche de bugs
-----------------------------------

`git checkout` permet également de retrouver un commit qui a introduit un problème. Pour tester cette fonctionnalité vous pouvez effectuer l'exercice [Chercher le bug](../chercherbug/).