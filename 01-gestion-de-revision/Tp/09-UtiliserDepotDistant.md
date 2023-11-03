Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-local` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-local tpgit`

Pour collaborer sur un projet, nous pouvons utiliser et partager par 'ssh' le dépôt d'un des collaborateurs, comme nous l'avons vu dans le chapitre précédent. Cependant cela oblige, d'une part, que la machine de ce collaborateur soit en permanence en fonctionnement et, d'autre part, que cette machine soit accessible depuis l'Internet (et donc sur un réseau public) et, enfin, que tous les collaborateurs aient un compte sur cette machine.

Ces contraintes sont dans la majorité des cas bien trop fortes. De ce fait, les projets collaboratifs utilisent un dépôt dît de _référence_, hébergé sur un serveur Git. Plusieurs solutions sont disponibles. Elles sont listées dans le chapitre [Services Disponibles](../services/). Pour ce chapitre, nous allons utiliser un serveur [github](https://github.com/) et en particulier celui que le FIL met à disposition. Si vous utilisez un autre serveur, il vous suffira de modifier les urls en conséquence.

Création d'un dépôt sur le serveur gitlab du FIL
------------------------------------------------

Vous allez d'abord créer un dépôt distant en utilisant le serveur gitlab du FIL.

*   Connectez vous sur [https://github.com/](https://github.com/).
*   Authentifiez-vous avec votre login FIL.
*   Créez un groupe privé dont le nom est préfixé par 'cristal-' ('cristal-binome1', par exemple)
*   Ajoutez votre binôme dans le groupe. Dans le cas du serveur du FIL, il faut attendre que votre binôme s'y soit authentifié une première fois. Donnez-lui les permissions 'Master' (pour éviter de devoir régler plus finement les droits d'accés).
*   Dans ce groupe, créez un projet avec comme nom 'tpgit'. gitlab vous donne un certain nombre d'instructions pour procéder à la suite. Gardez une copie de ces instructions, vous en aurez besoin.

Poussez votre dépôt local
-------------------------

Nous allons réutiliser le dépôt que vous avez créé en local (répertoire 'tpgit') et _enregistrer une copie_ sur le projet créé sur le serveur gitlab.

Il faut tout d'abord indiquer à Git de créer un _lien_ vers le dépôt géré par le serveur. Suivez pour cela l'instruction que vous donne gitlab. Elle ressemble à :

    $ git remote add origin https://github.com/cristal-binome1/tpgit.git
    

Vous comprendrez plus tard ce que fait cette commande ; pour l'instant, considérez simplement que vous avez créé en local un lien ('_remote add_') nommé '_origin_' vers l'URL du dépôt stocké sur le serveur gitlab.  
Le nom 'origin' est celui donné usuellement, vous pouvez en choisir un autre si vraiment vous le désirez. Vous pouvez vérifier les liens distants enregistrés, avec la commande :

    $ git remote -v
    

Pour envoyer la version actuelle de votre historique sur le dépôt distant, utilisez :

    $ git push -u origin master
    

Pour l'instant, interprétez cette commande comme voulant dire : _synchroniser le dépôt distant dont le nom est **origin** avec mon historique local dont la tête est **master**_. Le dépôt distant étant pour l'instant vide, tout votre historique va être recopié sur le serveur.

Une fois cette commande terminée, vous pouvez vérifier avec l'interface Web du serveur gitlab que votre projet contient bien votre historique (onglets 'Files' et 'Commits').

Maintenant que votre dépôt est enregistré sur le serveur, il est possible à tous les membres de votre projet d'en créer une copie locale, un _clone_.

Nous allons _mettre de côté_ le répertoire 'tpgit' pour la suite, en le renommant :

    $ cd ~
    $ mv tpgit tpgit-local
    

Cloner un dépôt distant
-----------------------

Imaginons que vous êtes un nouveau collaborateur au projet. Vous devez récupérer le dépôt hébergé sur le serveur.

Retournez dans votre répertoire racine, et utilisez, comme dans le chapitre précédent, une commande `git clone` semblable à :

    $ cd ~
    $ git clone https://github.com/cristal-binome1/tpgit.git
    

Une copie du dépôt 'tpgit' du serveur est alors créée dans un répertoire local 'tpgit' (par défaut, Git créé un répertoire en réutilisant le nom du dépôt mais sans le suffixe .git).

Entrez dans le répertoire 'tpgit' et vérifiez avec `git log` que tout l'historique est bien présent sur votre machine.

Vérifiez qu'un lien vers le dépôt distant a été créé :

    $ git remote -v
    

`git clone` crée par défaut un lien nommé 'origin'.

Bien entendu, dans notre cas cette opération n'était pas réellement nécessaire puisque nous avions déjà une version locale du dépôt...

Choix d'un dépôt de référence
-----------------------------

Pour la suite, vous allez travailler en binôme pour _simuler_ un développement collaboratif. Choisissez si votre projet gitlab ou celui de votre binôme servira de dépôt référence (c'est à dire de dépôt partagé entre vous).  
Si il s'agit de votre projet, alors vous en avez déjà créé un clone.  
Si il s'agit de celui de votre binôme, alors il vous faut en créer une copie en local sur votre machine.

Supprimez votre répertoire 'tpgit' et procédez comme dans le paragraphe précédent pour cloner le dépôt de votre binôme, à l'aide d'une commande semblable à (remplacez 'cristal-binomex' par le nom de groupe qu'a créé votre binôme) :

    $ cd ~
    $ rm -rf tpgit
    $ git clone https://github.com/cristal-binomex/tpgit.git
    

Modifications collaboratives (1)
--------------------------------

Vous allez maintenant à tour de rôle faire une modification :

*   Le premier membre du binôme, modifie le fichier **README.md** et crée un commit.

_Remarque :_ à ce stade, vous pouvez toujours annuler le dernier commit en utilisant `git reset HEAD^` sans problème, puisque pour l'instant rien n'a été transmis sur le serveur. Vous pouvez vérifiez avec l'interface Web du serveur gitlab que le fichier qu'il héberge est toujours dans sa version précédente.

*   Le commit est poussé sur le dépôt distant :

    $ git push
    

_Note :_ Contrairement au cas du deuxième paragraphe, il n'est pas nécessaire ici de préciser où pousser le commit. `git clone` a enregistré l'information pour vous.

**Attention !** Le commit est maintenant récupérable par votre coéquipier. Si vous l'annulez avec `git reset` (git vous le déconseillera) vous allez au devant de problèmes certains !

Parenthèse visuelle (1)
-----------------------

Que s'est-il passé jusqu'à présent ? Utilisons une simulation **explainGit** pour bien le comprendre.

Local RepositoryCurrent Branch: masteree5df4b..7eb7654..e137e9b..masterHEAD

originee5df4b..7eb7654..e137e9b..masterHEAD

Enter git commands below.

Lorsque dans le deuxième paragraphe vous avez poussé votre dépôt local vers votre dépôt distant (représenté en haut à droite), Git a crée une _copie exacte_ de votre historique : les mêmes commits avec les mêmes identifiants sont dans le dépôt hébergé sur le serveur.

Créez un commit : `git commit`.  
_Résultat:_ Il y a maintenant un commit supplémentaire dans votre historique local.

Effectuez un `git push`.  
_Résultat:_ Git compare votre historique local avec l'historique distant (en se basant sur les identifiants des commits). Par rapport à la tête ('master') de l'historique distant, il y a un commit supplémentaire dans votre historique local. Il est recopié tel quel, et la tête de l'historique distant est modifiée.

Modifications collaboratives (2)
--------------------------------

*   De son côté, le deuxième membre du binôme ajoute un commentaire au fichier **hello.py**, crée un commit et le pousse.

Pourquoi Git refuse-t-il de le faire ?

Le dépôt distant compte un commit que vous n'avez pas en local. Git ne sait donc pas quoi faire avec le commit que vous voulez pousser : écraser le commit existant sur le dépôt distant ? Fusionner les 2 commits et régler les conflits ?

Non, Git ne veut/peut pas prendre ce genre de décision. Il faut donc que vous récupériez d'abord les changements du dépôt distant et que vous régliez vous-même les éventuels problèmes. Pour cela tapez :

    $ git pull
    

Un nouveau commit qui fusionne les 2 modifications (celles du commit distant et celles de votre commit local) va être créé. Vous êtes invité à modifier (ou non) son message. Nous reviendrons plus tard sur cette notion de fusion.

Vous pouvez maintenant effectuer le `git push`.

Parenthèse visuelle (2)
-----------------------

Reprenons **explainGit**.

Tout comme Git gère une référence vers la tête de l'historique local (**master**), il gère également une référence vers ce qu'il pense être la tête de l'historique distant (**origin/master**). Avoir cette référence permet à Git de pouvoir exécuter beaucoup d'opérations sans avoir besoin de se connecter au serveur distant. C'est toute sa force ! Cette référence n'est mise à jour que lorsque vous utilisez les commandes _distantes_ (pull, push et fetch).

En local, Git n'a aucune connaissance du fait que le premier collaborateur a effectué un `git push`. La référence **origin/master** ne pointe donc plus sur le réel dernier commit présent dans l'historique du dépôt distant.

Local RepositoryCurrent Branch: masteree5df4b..7eb7654..e137e9b..origin/mastermasterHEAD

origina278ab4..ee5df4b..7eb7654..e137e9b..masterHEAD

Enter git commands below.

Effectuez un `git commit`.  
_Résultat :_ Ce commit est ajouté à la fin de votre historique local (**master**), qui n'est donc maintenant plus cohérent avec le dépôt distant.

Effectuez un `git push` (il ne se passe rien...).  
_Résultat :_ Git se rend compte qu'il y a une différence entre votre **origin/master** et le vrai dernier commit distant. Il faut d'abord procéder à une synchronisation...

Effectuez un `git pull` et regardez bien l'animation (utilisez `/reset` et recommencez le `git push`, `git pull` si nécessaire)  
_Résultat :_ Git récupère d'abord le commit distant supplémentaire, qu'il attache à votre **origin/master** et il déplace la référence. Vous avez alors en local en copie exacte de l'historique distant. Une divergence a été introduite dans votre historique local, que Git résout en procédant à une fusion avec votre commit (celui référencé par **master**). Un nouveau commit est crée dans votre historique, résultat de cette fusion.

Effectuez un `git push`.  
_Résultat :_ Le dépôt distant est mis à jour (note: il semble y avoir un bug dans **explainGit**, après le `git push` **origin/master** devrait être déplacé...)

Modifications collaboratives (3)
--------------------------------

Pour que tous les dépôts soit synchronisés, il reste à faire un `git pull` sur l'autre machine.

var explaingit\_content = \[ { name: 'Distant1', height: 350, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: 'ee5df4b', parent: '7eb7654', tags: \['master'\]} \], originData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: 'ee5df4b', parent: '7eb7654', tags: \['master'\]} \] }, { name: 'Distant2', height: 350, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: 'ee5df4b', parent: '7eb7654', tags: \['origin/master', 'master'\]} \], originData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: 'ee5df4b', parent: '7eb7654'}, {id: 'a278ab4', parent: 'ee5df4b', tags: \['master'\]} \] } \];