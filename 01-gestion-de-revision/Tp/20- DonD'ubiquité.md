Non content de vous permettre de voyager dans le temps, Git vous permet également d'être à _deux endroits_ en même temps (plus ou moins...).

Voyons d'abord deux cas où cela peut être utile.

#### Déplacement temporaire sur une branche

Vous êtes en plein développement d'une évolution sur une branche spécifique, et on vous demande de faire une démonstration ou de corriger un bug en urgence. Nous avons déjà vu que la commande `git stash` permet de remiser temporairement votre travail.

Mais vous êtes peut-être en train d'exécuter votre programme en mode pas-en-pas dans un débugger, très près de cerner le problème que vous _chassez_ depuis de nombreuses minutes, et vous n'avez pas envie de devoir quitter le débugger et de devoir recommencer toute l'analyse...

#### Comparer l'exécution de deux versions

Il est parfois nécessaire de comparer le résultat de l'exécution d'une modification en cours avec le résultat d'une ancienne version d'un code.  
Admettons que la version en cours de développement soit sur une branche spécifique. Il va alors vous falloir faire des _aller-retours_ entre les deux branches pour exécuter chacune des versions et les comparer.

Une solution possible
---------------------

Avec ce que vous connaissez déjà de Git, vous pourriez envisager de cloner une deuxième fois votre dépôt, dans un répertoire différent. Vous avez ainsi possibilité de vous placer sur une branche différente dans ce deuxième clone.

Cependant, vous avez maintenant créé deux dépôts locaux, et les modifications que vous apportez à l'un ne seront pas _transmises_ automatiquement à l'autre. C'est dommage !

Duplication de workdir
----------------------

Git vient à votre rescousse avec le commande `git worktree`. Celle-ci vous permet de créer un autre répertoire de travail qui référence le même dépôt. Le dépôt est donc, d'une certaine manière, _partagé_ entre les deux répertoires de travail.

Placez-vous dans un de vos projets qui contient des branches (ou créez rapidement une branche dans un projet existant). On suppose pour la suite qu'il s'agit de 'tpgit'.

Utilisez la commande suivante :

    $ git worktree add ../tpgit-tmp
    

Un répertoire de travail est crée dans ../tpgit-tmp. Entrez-y. Vous retrouvez vos fichiers, et `git lg` montrera que vous avez bien accès à tout l'historique. Par défaut, `git worktree` crée une branche du même nom que le répertoire choisi ('tpgit-tmp', en l'occurence) et vous place sur cette branche.

Regardez '.git'. Ce n'est plus un répertoire, mais un simple fichier, qui contient une référence permettant à Git de savoir que ce répertoire de travail est associé à un dépôt situé à un autre endroit.

Effectuez une modification sur un des fichiers, et validez-la (`git add ; git commit`).

Retournez dans votre répertoire de travail de base, et affichez les branches existantes :

    $ cd ../tpgit
    $ git branch
    * master
      tpgit-tmp
      wip
    

La branche 'tpgit-tmp' existe bien également. Normal, puisque le dépôt est _réellement_ partagé.

Tentez de vous placer sur cette branche :

    $ git checkout tpgit-tmp
    fatal: 'tpgit-tmp' is already checked out at '/home/degrande/Documents/CRIStAL/SADR/2017_tp_git/srcs/tpgit-tmp'
    

Git refuse, en vous indiquant que vous êtes déjà placé sur cette branche dans un autre répertoire de travail. C'est un peu normal. Git ne saurait pas quoi faire si vous modifiiez la même branche dans les deux répertoires de travail...

Vous pouvez néanmoins regarder ce qui a été fait sur cette branche : `git show tpgit-tmp`. Vous devriez y retrouver le commit exécuté précédemment.

Gérer les duplications
----------------------

La commande `git worktree list` affiche la liste des répertoires de travail associés au dépôt.

Une fois le travail terminé dans 'tpgit-tmp', il vous suffit de supprimer le répertoire par la commande de base de votre OS : `rm -rf tpgit-tmp`.  
Placez-vous dans 'tpgit' et exécutez `git worktree list`. La copie est toujours référencée... Il faut faire le ménage à l'aide de `git worktree prune`. Cette commande supprimera de la liste tous les répertoires de travail qui n'existent plus.

**Note :** Faîtes bien attention à ne pas supprimer le répertoire de travail de base ('tpgit'), ce qui supprimerait également le dépôt ! Prenez donc l'habitude de marquer de manière visible le répertoire qui contient la duplication (par exemple en utilisant le suffixe '-tmp').

**Remarque :** N'utilisez cette fonctionnalité que de manière temporaire, n'en faîtes pas une utilisation normale. Être à deux endroits en même temps n'est pas si facile que ça à gérer...