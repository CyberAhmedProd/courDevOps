Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `tpfiles/tpgit-enarriere1` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf tpfiles/tpgit-enarriere1 tpgit`

Pour les manipulations de ce chapitre, retournez dans le répertoire utilisé dans le chapitre [Revenir en arrière (1)](./05-RetourArriere1.md)

Votre historique devrait être le suivant :

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
    

Annuler des commits
-------------------

Vous décidez, après réflexion, que le commit restaurant **hello.py** n'était pas une bonne idée. Il faudrait donc supprimer ce commit de l'historique. Cette opération peut être réalisée avec la commande `git reset` :

    $ git reset HEAD^
    

_Rappel_ : **HEAD^** référence le commit précédant HEAD.

`git status` montre que **hello.py** est toujours présent dans votre espace de travail mais qu'il est maintenant non suivi.

    Sur la branche master
    Fichiers non suivis:
      (utilisez "git add <fichier>..." pour inclure dans ce qui sera validé)
    
        hello.py
    
    aucune modification ajoutée à la validation mais des fichiers non suivis sont présents (utilisez "git add" pour les suivre)
    

C'est le comportement par défaut de `git reset`. Pour plus de précisions, veuillez consulter le paragraphe _'À retenir'_ à la fin de cette page.

À l'aide du simulateur **explainGit**, vous pouvez observer la différence entre la commande `git checkout` que nous avons déjà vue,  
et la commande `git reset` :

1.  `git checkout HEAD^` : déplacement de la référence **HEAD** mais sans _toucher_ à l'historique enregistré (et **synchronisation** du répertoire de travail)
2.  `/reset` (cette commande propre au simulateur le remet dans sa configuration initiale)
3.  `git reset HEAD^` : déplacement de la référence de la tête de l'historique (**master**), ce qui force un déplacement de **HEAD** (mais **sans modification** du répertoire de travail)

`git reset` va donc déplacer en arrière la référence qui _pointe_ sur le dernier commit de l'historique (le **master**). C'est comme si ce commit n'avait jamais été effectué. Avant ce commit, le fichier **hello.py** n'existait plus, il n'était donc plus _suivi_ par Git. C'est bien dans cet état que nous sommes revenu.

Attention au reset --hard!
--------------------------

Le mode `--hard` de `git reset` permet d'aller plus loin dans le retour en arrière, en combinant le `git reset` avec une synchronisation du répertoire de travail (comme le fait `git checkout`). Il y a cependant une différence essentielle avec `git checkout` : la _synchronisation_ est forcée, **toutes** les modifications sur les fichiers suivis sont supprimées (`git checkout` ne peut pas être exécuté si il existe des modifications non validées).  
Ce mode est à utiliser en connaissance de cause : c'est l'un des rares cas où vous pouvez perdre des données de manière irréversible !

Pour tester ce mode :

*   Re-validez hello.py en créant un nouveau commit.
*   Utilisez le mode `--hard`: `git reset --hard HEAD^`
*   Constatez que les modifications sont perdues (le fichier **hello.py** a disparu !)

Supprimer plusieurs commits
---------------------------

`git reset` permet aussi de supprimer plusieurs commits. Pour cela il y a plusieurs possibilités (ne les essayez pas, nous avons besoin de conserver le dépôt dans son état actuel pour la suite...).

Pour supprimer les 'n' derniers commits, il suffit de suffixer le caractère '^' n fois à la référence 'HEAD'. Par exemple pour supprimer les 5 derniers commits :

    $ git reset HEAD^^^^^
    

Notation compressée :

    $ git reset HEAD~5
    

Pour supprimer tous les commits après le commit ayant l'id 'f1fa39c' :

    $ git reset f1fa39c
    

Pour supprimer le commit ayant l'id 'f1fa39c' et ceux qui le suivent (et donc revenir _avant_ le commit 'f1fa39c') :

    $ git reset f1fa39c^
    

_Note :_ Cette notation est également utilisable avec d'autres commandes, telle que `git show`, `git checkout`, etc

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
    

À retenir

Les commandes `git checkout` et `git reset` pourraient sembler équivalentes de prime abord (du moins dans l'état actuel de ce que vous en connaissez) puisqu'elles peuvent produire le même résultat apparent sur votre répertoire de travail.

Elles sont cependant fondamentalement différentes dans leur objectif :

*   `git checkout` agit **sur le répertoire de travail** : le répertoire de travail est restauré dans l'état enregistré dans le commit sélectionné.
    
    Pour cela, Git peut modifier vos fichiers, et en ajouter ou supprimer d'autres (Git ne touche cependant pas aux fichiers non suivis, fort heureusement).  
    Cette commande ne modifie pas l'historique enregistré dans le dépôt (la référence **master** n'est pas modifiée). On peut donc revenir à la dernière version avec `git checkout master`.
    
*   `git reset` agit **sur l'historique** : la référence de tête (**master**) est déplacée, ce qui supprime des commits de l'historique.
    
    *   Avec l'option `--soft`
        
        le contenu du répertoire de travail n'est pas modifié : les différences entre le répertoire de travail et le nouveau commit de tête sont considérées comme des modifications placées dans l'index, mais non encore validées.  
        _Résultat_ : on revient juste au moment où l'on avait fait le `git add` mais sans avoir encore fait le commit.
        
    *   Dans sa version de base (option `--mixed` par défaut)
        
        le contenu du répertoire de travail n'est toujours pas modifié : les différences entre le répertoire de travail et le nouveau commit de tête sont considérées comme non indexées.  
        _Résultat_ : on revient un cran plus tôt, juste au moment où l'on avait modifié les fichiers mais sans avoir encore fait le `git add`.
        
    *   Dans sa version `--hard`
        
        le répertoire de travail est également synchronisé avec le nouveau commit de tête.  
        _Résultat_ : on revient un cran de plus en arrière, avant le moment où l'on avait modifié les fichiers.
        

Cette différence fait que leur fréquence d'utilisation est très dissemblable :

*   `git checkout <commit_id>` est une commande utilisée très régulièrement lorsque l'on travaille avec des _branches_, comme vous le verrez bientôt. C'est l'usage principal de cette commande.
    
*   `git checkout <un_fichier>` est occasionnellement utilisée pour supprimer des modifications que l'on vient d'ajouter à un fichier.
    
*   `git reset` est rarement utilisée. Elle ne sert, a priori, que lorsque l'on décide d'abandonner un travail en cours et de revenir en arrière.
    

**ATTENTION :** Comme pour toute commande modifiant l'historique, `git reset` ne doit pas être utilisé sur des historiques partagés avec d'autres contributeurs.

var explaingit\_content = \[ { name: 'Reset', height: 300, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '56af8a9', parent: '7eb7654'}, {id: 'ce86fab', parent: '56af8a9'}, {id: '090e2b8', parent: 'ce86fab'}, {id: 'ee5df4b', parent: '090e2b8', tags: \['master'\]} \] } \];