*   [Docs](..) »
*   Initialisation

* * *

Initialiser un nouveau répertoire
---------------------------------

Pour créer un nouveau répertoire suivi par GIT tapez:

    $ git init tpgit
    

Cette commande crée le répertoire `tpgit` et le sous répertoire `tpgit/.git` dans lequel est stocké l'historique de votre projet.

Entrez dans le répertoire de travail:

    $ cd tpgit
    

S'identifier pour les futurs commits
------------------------------------

Il est important (surtout en équipe) de savoir qui a fait quoi. Pour identifier vos futurs commits, tapez les commandes suivantes:

    $ git config --global user.name "votre nom"
    $ git config --global user.email nom.prenom@univ-lille1.fr
    

Remarque: l'option -g permet de définir ces propriétés globalement pour tous vos répertoires suivis par GIT. Si vous l'omettez, les propriétés seront spécifiques au projet courant. Tapez `git config --list` pour visualiser toutes les propriétés de votre environement.

Personaliser son environnement
------------------------------

Pour certaines commandes vous aurez besoin d'un éditeur de texte. Ce sera l'éditeur par défaut de votre système qui sera choisi. Si vous souhaitez en utiliser un autre (par exemple vi):

    $ git config --global core.editor vi
    

Ajoutez également, pour éviter un message de warning un peu cryptique que GIT affichera lors d'une commande que nous utiliserons bientôt :

    $ git config --global push.default simple
    

Ajouter un prompt
-----------------

Utiliser un prompt spécial GIT est très pratique pour visualiser rapidement ou vous en êtes. Pour en configurer un, placez-vous dans votre répertoire personnel:

    $ cd ~
    

Récupérez ce [script](https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh) et mettez-le dans le répetoire courant (répertoire personnel).

Ensuite, ajoutez les lignes suivantes à la fin du fichier .bashrc (créez-le si il n'existe pas):

    . ~/git-prompt.sh
    export GIT_PS1_SHOWDIRTYSTATE=1
    export PS1='\u@\h:\w$(__git_ps1 " (%s)")\$ '
    

Retournez dans votre dépot git:

    $ cd tpgit
    

Mettez à jour votre environement bash (ou reconnectez-vous) :

    $ . ~/.bashrc
    

Votre promt devrait ressembler à cela:

    mon_login@ma_machine:~/tpgit (master)$
    

* * *