Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-3arbres` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-3arbres tpgit`

Nous reprenons les manipulations dans l'état où elles étaient à la fin du chapitre [Consulter l'historique](../historique/)

Pour des raisons de gain de performance, vous désirez compiler votre programme :

    $ python -c "import hello"
    

Regardez l'état de votre projet :

    $ git status
    Sur la branche master
    Fichiers non suivis:
      (utilisez "git add <fichier>..." pour inclure dans ce qui sera validé)
    
        hello.pyc
    

Le fichier `hello.pyc` (résultat de la compilation) est un fichier binaire généré par la compilation. Vous n'avez aucun besoin de suivre l'état de ce fichier qui peut être généré à nouveau à chaque compilation.  
D'autre part, comme tout VCS, Git n'est réellement adapté qu'au suivi de fichiers textes. Il ne faut donc intégrer **hello.pyc** ni à l'index ni au dépôt.

Pour l'oublier une fois pour toute, vous pouvez l'ajouter à la liste des fichiers à ignorer par Git.

Pour cela, il suffit de créer un fichier **.gitignore** à la racine de votre répertoire de travail, qui contiendra une liste de fichiers à ignorer (une ligne par fichier). Dans notre cas, mettez simplement la ligne suivante :

    hello.pyc
    

ou plus généralement, pour ignorer tout fichier compilé :

    *.pyc
    

Vous pouvez également ignorer toute une partie de l'arborescence. Par exemple, si une application génère des logs dans un répertoire `logs/`, vous pouvez l'ignorer en ajoutant simplement la ligne suivante :

    logs/*
    

Testez de nouveau `git status`. **hello.pyc** n'est plus listé comme non suivi, par contre Git détecte notre nouveau fichier **.gitignore**.  
Vous pouvez au choix commiter ce fichier ou... l'ignorer !

Sous Unix, vous pouvez aussi placer un fichier .gitignore à la racine de votre homedir.