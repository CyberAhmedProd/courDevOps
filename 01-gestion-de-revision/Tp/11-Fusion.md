Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-branches` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-branches tpgit`

Lorsque vous avez terminé le travail pour lequel vous avez créé une branche, il est nécessaire de _reporter_ les modifications effectuées sur la branche principale (la branche **master**). Cela se fait par une opération de _fusion_ (_merge_ en anglais) et Git est très puissant de ce point de vue. La solution la plus légère sera toujours prise. Cela revient souvent à un simple déplacement d'étiquette et à la création d'un nouveau commit de fusion. Parfois, ce sera plus compliqué, il faudra résoudre des conflits, mais git vous rendra la tâche plus facile en vous guidant pas à pas.

Fusion 'fast-forward'
---------------------

Le premier cas de fusion que nous allons voir est le plus simple puisqu'aucun nouveau commit ne sera créé !

Visualisons avec **explainGit** ce cas particulier :

Sur la branche 'wip', qui a été créée, plusieurs commits ont été réalisés. La branche 'master', quant à elle, n'a pas évoluée.  
Les commits de la branche 'wip' sont donc tous situés dans une séquence qui suit la référence 'master'.

Pour fusionner une branche A sur une branche B, il faut d'abord se placer sur la branche B. Nous sommes donc revenus sur la branche 'master' (voir la position de la référence HEAD), sur laquelle nous voulons fusionner la branche 'wip'

Effectuez l'opération de fusion : `git merge wip`. Comme vous le remarquez, un déplacement de références est suffisant pour que les commits de la branche 'wip' soient ajoutés à 'master'.

#### Mise en pratique

Nous allons ajouter une fonctionnalié très utile à notre code : la possibilité d'écrire plusieurs fois un texte.

*   Vérifiez que vous êtes sur la branche 'wip', à l'aide de la commande `git branch`, par exemple :

    $ git branch
      master
    * wip
    

(Note: si vous avez installé le prompt Git, alors la branche courante est indiquée en fin de prompt)

*   Ajoutez la fonction suivante à **hello.py**

    def ecrireXFois(x, chaine):
        for i in range(x):
            ecrire(chaine)
    

*   Commitez la modification
    
*   Retournez sur master (`git checkout master`)
    

L'historique complet est le suivant :

    * 2d892e6 (wip) Ajout de la procédure ecrireXFois()
    * f1fa39c (HEAD -> master) Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

On voit bien que le commit de la branche 'wip' _est devant_ la tête de la branche 'master'.

*   Regardez le contenu du fichier **hello.py**

La fonction ecrireXFois() n'est plus là ! Git l'a supprimée ? Non, mais il fait ce que vous lui avez demandé, un 'checkout', c'est-à-dire qu'il a modifié le contenu de votre répertoire de travail pour qu'il reflète le dernier commit de la branche 'master'. Et _sur cette branche_, la nouvelle fonction n'existe pas dans **hello.py** !

Pour vous rassurer, retournez sur la branche 'wip' (`git checkout wip`) et vérifiez le fichier. Ouf, tout est là ! Retournez maintenant sur 'master'.

*   Fusionnez la branche `wip`:

    $ git merge wip
    Mise à jour ce07c5e..d6af756
    Fast-forward
     hello.py | 4 ++++
     1 file changed, 4 insertions(+)
    

Un nouveau `git lg` montre que la fusion n'a nécessité aucun nouveau commit (dù au fait que 'master' n'a pas évolué pendant le développement de la nouvelle fonctionnalité). Ce mode de fusion par simple déplacement de la référence de tête s'appelle un 'merge fast-forward' (c'est ce que Git a indiqué dans son message de réponse). Seul l'étiquette `master` s'est déplacée.

C'est le comportement par défaut de `git merge` mais, au besoin, vous avez la possibilité de forcer la création d'un commit de fusion en utilisant l'option `--no-ff`. L'intérêt est de conserver dans l'historique une trace du fait que certains commits ont été réalisés sur une branche. C'est d'ailleurs imposé dans certains workflows où la branche `master` ne comporte que des commits _livrables_.

Fusion sans conflit
-------------------

Voici dans **explainGit** un autre exemple plus fréquent : vous avez commencé à travailler sur une branche 'wip' et effectué quelques commits. Pour répondre à un besoin urgent, vous êtes retourné sur la branche 'master' (la branche _stable_) et vous avez réalisé un commit (pour corriger un bug, par exemple). Vous êtes ensuite retourné sur 'wip' pour continuer votre travail.

Il est temps maintenant d'intégrer le résultat sur la branche 'master', et vous y retournez :

Effectuez le `git merge wip` et observez le résultat.

La branche 'wip' ne faisant plus suite _directement_ à la branche 'master', un fast-forward (un déplacement de référence) n'est plus possible. Git doit combiner les modifications de deux branches en une seule, c'est l'opération de _fusion_ à proprement parler. Un commit contenant le résultat de cette fusion est créé et ajouté à la branche 'master'. On voit que ce commit à 2 parents. C'est la caractéristique des commits de fusion.

#### Mise en pratique

Commençons par revenir en arrière dans l'historique avec un `git reset --hard HEAD^`, pour supprimer le 'merge fast-forward' que nous avions créé. L'historique doit être semblable à :

    * 2d892e6 (wip) Ajout de la procédure ecrireXFois()
    * f1fa39c (HEAD -> master) Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

Interprétation : nous sommes sur la branche 'master' ('HEAD' pointe sur 'master') et il y a un commit supplémentaire sur la branche 'wip'. Notez au passage qu'il n'a pas été supprimé, malgrè l'exécution de la commande `git reset`. En effet, tant qu'un commit possède une référence (ici `wip`) il est visible dans l'historique.

Nous allons maintenant faire évoluer master pour obtenir un cas semblable à celui que nous venons de voir avec **explainGit**.

Dans master, vous allez modifier le programme pour qu'il affiche le message en majuscules :

    --- a/hello.py
    +++ b/hello.py
    @@ -1,4 +1,4 @@
     def ecrire(chaine):
    -    print chaine
    +    print chaine.upper()
    
     ecrire("Hello world!")
    

*   Ajoutez cette modification dans l'index, et commitez la.
    
*   Vous pouvez voir la différence qui existe entre le contenu de la branche 'wip' et la votre à l'aide de `git diff wip` :
    

    --- a/hello.py
    +++ b/hello.py
    @@ -1,8 +1,4 @@
    -def ecrireXFois(x, chaine):
    -    for i in range(x):
    -        ecrire(chaine)
    -
     def ecrire(chaine):
    -    print chaine
    +    print chaine.upper()
    
     ecrire("Hello world!")
    

Comment interprétez-vous ce résultat ?

Dans votre branche, par rapport à 'wip', la fonction ecrireXFois() n'existe pas (d'où les '-') et la ligne 'print' est différente. C'est cohérent.

*   Fusionnez la branche `wip` : `git merge wip`

Vous êtes invité à modifier le message du commit de fusion. Le 'fast-forward' n'est pas possible dans ce cas puisque les 2 branches ont évolué parallèlement. Par contre vous n'avez pas eu de conflit à régler. En effet, les 2 modifications ne concernent pas les mêmes lignes, comme nous l'avons vu en faisant le `git diff wip`.

L'historique résultat est conforme à ce que nous avons vu avec **explainGit** :

    *   b25ddb4 (HEAD -> master) Merge branch 'wip'
    |\  
    | * 2d892e6 (wip) Ajout de la procédure ecrireXFois()
    * | 0ed54e0 Affichage en majuscules
    |/  
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

Fusion avec conflit
-------------------

Effectuons un `git reset --hard HEAD^` pour annuler notre commit de fusion. Nous revenons à :

    * 452fe81 (HEAD -> master) Affichage en majuscules
    | * d6af756 (wip) Ajout de la fonction ecrireXFois()
    |/  
    * ce07c5e Utilisation de la fonction ecrire()
    * 7b72430 Ajout de la fonction ecrire()
    * ee6c325 Ajout de hello.py
    * d94e762 Ajout du REAMDE.md
    

Nous avons donc bien un commit particulier sur chacune des deux branches.

Nous allons maintenant provoquer volontairement un conflit, pour voir comment se comporte Git dans ce cas au moment de la fusion.  
Pour cela, nous allons modifier la même ligne du fichier **hello.py** dans les 2 branches.  
Dans `master`, nous avons modifié la fonction **ecrire()** pour qu'elle affiche en majuscules.  
Dans `wip`, effectuons un commit dans lequel la fonction **ecrire()** affiche en minuscules.

*   Placez-vous dans `wip` et modifiez la procédure `ecrire()` comme cela:

    --- a/hello.py
    +++ b/hello.py
    @@ -3,6 +3,6 @@ def ecrireXFois(x, chaine):
             ecrire(chaine)
    
     def ecrire(chaine):
    -    print chaine
    +    print chaine.lower()
    
     ecrire("Hello world!")
    

*   Commitez la modification
*   Retournez dans `master`
*   Tentez une fusion de la branche `wip` :

    $ git merge wip
    Fusion automatique de hello.py
    CONFLIT (contenu) : Conflit de fusion dans hello.py
    La fusion automatique a échoué ; réglez les conflits et validez le résultat.
    

Git a détecté un conflit, et a arrêté l'opération de fusion. `git status` vous le rappelle (le prompt Git fait de même avec l'indication '|MERGING') :

    $ git status
    Sur la branche master
    Vous avez des chemins non fusionnés.
      (réglez les conflits puis lancez "git commit")
      (use "git merge --abort" to abort the merge)
    
    Chemins non fusionnés :
      (utilisez "git add <fichier>..." pour marquer comme résolu)
    
        modifié des deux côtés :  hello.py
    
    aucune modification n'a été ajoutée à la validation (utilisez "git add" ou "git commit -a")
    

Il faut résoudre le conflit pour pouvoir poursuivre la fusion. Pour cela, éditez le fichier **hello.py** commme Git vous y invite et considérez les lignes suivantes :

    <<<<<<< HEAD
        print chaine.upper()
    =======
        print chaine.lower()
    >>>>>>> wip
    

Ce balisage symbolise le conflit à résoudre. La première section concerne la modification de la branche courante ('HEAD', qui pointe sur `master`) et la seconde, celle de `wip`.

Il suffit maintenant de supprimer le balisage de conflit et la modification que vous ne voulez pas garder. Par exemple, l'affichage en minuscules.  
Éditez **hello.py** pour qu'il contienne au final :

    def ecrireXFois(x, chaine):
        for i in range(x):
            ecrire(chaine)
    
    def ecrire(chaine):
        print chaine.upper()
    
    ecrire("Hello world!")
    

*   La version à conserver étant définie, on la commite :

    $ git add hello.py
    $ git commit
    

Git sait que l'on est en cours de fusion, il vous propose donc un message de commit par défaut.  
Une fois le commit réalisé, la fusion est terminée, et l'historique devient :

    *   b256e03 (HEAD -> master) Merge branch 'wip'
    |\  
    | * a886991 (wip) Affichage en minuscules
    | * 2d892e6 Ajout de la procédure ecrireXFois()
    * | 0ed54e0 Affichage en majuscules
    |/  
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

var explaingit\_content = \[ { name: 'Fastforward', height: 300, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b', tags: \['master'\]}, {id: 'ab86def', parent: '7eb7654'}, {id: '090e2b8', parent: 'ab86def'}, {id: 'ee5df4b', parent: '090e2b8', tags: \['wip'\]} \] }, { name: 'Merge', height: 300, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '56tg8a9', parent: '7eb7654', tags: \['master'\]}, {id: 'hd86gst', parent: '7eb7654'}, {id: '090e2b8', parent: 'hd86gst'}, {id: 'ee5df4b', parent: '090e2b8', tags: \['wip'\]} \], initialMessage: 'Have even more fun.' } \];