Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-merge` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-merge tpgit`

Fusionnez avec Git c'est plutôt facile mais dans certains cas, il n'est pas souhaitable d'encombrer l'historique en gardant la trace de toutes les branches fusionnées. L'idéal serait alors de faire, quelque soit le contexte, l'équivalent d'un 'merge fast-forward' afin d'obtenir, après fusion, un arbre linéaire. Avec Git, c'est possible malgrè la multiplication des branches et les conflits grâce à `git rebase`.

Attention ! L'utilisation de `git rebase` implique la modification de l'historique. Il faut donc s'assurer que la partie que l'on affecte est bien locale.

`git rebase`, comme son nom l'indique, change 'la base' de la branche courante, c'est à dire le commit à partir duquel la branche a démarré. La nouvelle base devient le dernier commit de la branche passée en paramêtre de `git rebase`. C'est pour cette raison que la branche que l'on rebase ne doit pas avoir été partagée (pas poussée).

Pour bien comprendre, regardons avec **explainGit** comment se passe un rebase :

Placez-vous sur la branche 'wip' (`git checkout wip`), tapez `git rebase master` et observez le résultat.  
Tout se passe comme si les commits de la branche 'wip' avaient été déplacés à la fin de la branche 'master'.  
En fait, si vous regardez bien le résultat, Git a _appliqué_ tour à tour chacun des commits de la branche 'wip' sur la tête de la branche 'master'.  
Il a donc créé de nouveaux commits lors du 'rebase', ce que l'on observe par le fait que leurs identifiants sont différents.

La conséquence de cette opération est que des conflits peuvent éventuellement arriver lors du rebasage, conflits qu'il vous faudra résoudre.

Remarquez aussi que la référence `wip` s'est déplacée. Les anciens commits ne sont plus attachés à une référence. Le ramasse-miettes finira par les effacer automatiquement.

On se retrouve donc maintenant dans le cas d'un 'merge fast-forward'. Il n'y a plus qu'à fusionner 'wip' dans master : `git checkout master` suivi de `git merge wip`

Mise en pratique
----------------

Nous allons maintenant utiliser `git rebase` dans plusieurs cas réels.  
Pour commencer, nous allons faire un peu de ménage dans notre dépôt 'tpgit'. Son historique actuel est :

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
    

*   En étant placé sur la branche 'master', annulez le commit de fusion : `git checkout master`, `git reset --hard HEAD^`
*   En étant placé sur la branche 'wip', on supprime le commit qui est source de conflit : `git checkout wip`, `git reset --hard HEAD^`
*   Retournez sur la branche 'master'.

L'historique est maintenant :

    * 0ed54e0 (master) Affichage en majuscules
    | * 2d892e6 (HEAD -> wip) Ajout de la procédure ecrireXFois()
    |/  
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

Nous nous retrouvons donc avec les deux branches ayant evoluées en parallèle (mais sans conflit entre elles).

Rebaser sans conflit
--------------------

Avant de faire la fusion de la branche 'wip' sur la branche 'master', nous allons donc déplacer la base de `wip` _au bout_ de la branche `master`.

Placez-vous dans `wip` et effectuez le rebasage :

    $ git checkout wip
    $ git rebase master
    

`git lg` montre que nous avons maintenant un arbre linéaire de commits (et que l'identifiant du commit de la branche 'wip' est différent) :

    * 879069d (HEAD -> wip) Ajout de la procédure ecrireXFois()
    * 0ed54e0 (master) Affichage en majuscules
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

Nous pouvons retourner dans 'master' et faire un 'merge fast-forward' en tapant simplement :

    $ git checkout master
    $ git merge wip
    

_Remarque :_ lorsque nous disons que `git rebase` déplace la branche, nous faisons un abus de langage. Dans les faits, une nouvelle séquence de commits est créée et la référence de tête de branche est modifiée pour pointer sur le dernier commit. Si l'identifiant des commits est modifé suite à un rebasage ce n'est pas seulement parce que leur contenu peut être différent, mais aussi parce que leur parent n'est plus le même.

Rebaser avec conflit
--------------------

**Remarque utile pour la suite :** Vous pouvez à tout moment annuler un rebasage en cours en utilisant `git rebase --abort`. Vous reviendrez alors dans l'état où vous étiez avant d'exécuter la commande `git rebase`.

Pour provoquer un conflit, nous allons maintenant modifier la même ligne du README.md dans les 2 branches.

*   Modifiez la première ligne du README.md dans `master` puis commitez.
*   Modifiez (avec un autre contenu) la première ligne du README.md dans `wip` puis commitez. Votre historique devrait ressembler à cela :

    * 811254e (HEAD -> wip) Doc: description de la nouvelle fonctionalité
    | * 17141c4 (master) Doc: la mise en majuscules
    |/  
    * 879069d Ajout de la procédure ecrireXFois()
    * 0ed54e0 Affichage en majuscules
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

*   Tentez un rebasage de 'wip' sur 'master' :

    $ git checkout wip
    $ git rebase master
    Premièrement, rembobinons head pour rejouer votre travail par-dessus...
    Application de  Doc: description de la nouvelle fonctionalité
    Utilisation de l'information de l'index pour reconstruire un arbre de base...
    M   README.md
    Retour à un patch de la base et fusion à 3 points...
    Fusion automatique de README.md
    CONFLIT (contenu) : Conflit de fusion dans README.md
    error: Échec d'intégration des modifications.
    le patch a échoué à 0001 Doc: description de la nouvelle fonctionalité
    La copie du patch qui a échoué se trouve dans : .git/rebase-apply/patch
    
    Lorsque vous aurez résolu ce problème, lancez "git rebase --continue".
    Si vous préférez sauter ce patch, lancez "git rebase --skip" à la place.
    Pour extraire la branche d'origine et stopper le rebasage, lancez "git rebase --abort".
    

*   Un conflit a donc été détecté dans README.md. On est dans un cas similaire à une fusion avec conflit, que vous devez régler de la même façon : éditez le fichier README.md qui contient un marquage de conflit:

    <<<<<<< 17141c4e1c5b42c429e90d7167b5c7fdeaccd349
    Programme qui affiche plusieurs fois 'Hello world!' en majuscule.
    =======
    Nous allons faire quelque chose de nouveau avec le programme hello!
    >>>>>>> Doc: description de la nouvelle fonctionalité
    

**Remarque** : la première section montre la modification qu'apporte le commit `17141c4` qui se trouve dans `master`. La deuxième concerne le commit de wip que l'on veut appliquer.

*   Conservez ce que vous voulez.

**Remarque** : si vous choisissez de garder la version de 'master', en fait vous décidez d'oublier purement et simplement la modification qu'apporte le commit de 'wip' que vous essayez d'appliquer. À vous de choisir, mais pour simplifier la suite, conservez plutôt la version de la branche 'wip'.

*   `git status` vous rappelle ce que vous avez à faire : utilisez `git add` pour indexer la résolution du conflit
    
*   un nouveau `git status` vous indique comment poursuivre le rebasage : `git rebase --continue`
    

**Remarque :** Si, lors de la résolution du conflit, vous choisissez de garder la version de 'master', il n'y a de fait rien à ajouter à l'historique courant et Git vous propose de sauter le patch (c'est à dire de ne pas appliquer un commit qui n'apporte rien) avec un `git rebase --skip`. Si vous changez d'avis, annulez le rebase avec `git rebase --abort`.

*   Constatez que votre arbre de commit est linéaire
    
*   Vous pouvez retourner dans master pour exécuter un 'merge fast-forward' : `git merge wip`
    

Votre historique devrait ressembler à cela:

    * ad21e59 (HEAD -> master, wip) Doc: description de la nouvelle fonctionalité
    * 17141c4 Doc: la mise en majuscules
    * 879069d Ajout de la procédure ecrireXFois()
    * 0ed54e0 Affichage en majuscules
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    

À retenir

Avec Git, la création de branches, le passage d'une branche à une autre, la fusion de branches sont des opérations très faciles à utiliser et très rapides à exécuter. Usez-en et abusez-en. Il n'est pas rare d'avoir une dizaine de branches en cours. Elles peuvent être utilisées pour tout (et donc n'importe quoi...), ce qui fait que des "bonnes pratiques" ont été proposées. Nous en rediscuterons plus loin.

var explaingit\_content = \[ { name: 'Rebase', height: 300, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '56cd8a9', parent: '7eb7654', tags: \['master'\]}, {id: '1286ada', parent: '7eb7654'}, {id: '090e2b8', parent: '1286ada'}, {id: 'ee5df4b', parent: '090e2b8', tags: \['wip'\]} \] } \];