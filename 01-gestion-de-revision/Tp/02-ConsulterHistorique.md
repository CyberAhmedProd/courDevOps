Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-3arbres` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-3arbres tpgit`

Affichage de l'historique
-------------------------

Vous avons déjà vu la commande `git status`. Elle vous permet de voir où vous en êtes et vous donne des indications utiles. Vous aurez également besoin régulièrement de voir l'historique de vos commits. Pour cela vous allez utiliser `git log`.  
Vous devriez avoir quelque chose qui ressemble à cela :

    $ git log
    commit 081c474cb0535ddb5c0b3012e9cef5a3c88b2465
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 16:05:12 2017 +0100
    
        suppression fichier2
    
    commit 444047136aa437aea754beeff20305af05de8966
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 16:04:07 2017 +0100
    
        renommage fichier1 -> fichier2
    
    commit 3e348d465367d141be9e85159c748337cb34728b
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 16:02:50 2017 +0100
    
        Ajout fichier1
    
    commit f1fa39c7a0ce51c7c460009007f61dd3ee07e8a8
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 15:56:30 2017 +0100
    
        Utilisation de la procédure ecrire()
    
    commit 6766a32f8324827802f67d11f8ba57c7852b397d
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 15:56:17 2017 +0100
    
        ajout procédure ecrire()
    
    commit b9abeb89f579d4f2a86fe13f477f9fddd52eafb6
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 15:53:33 2017 +0100
    
        ajout programme hello
    
    commit 5e6ae0371633e32cc267dff55ba776c7bcd8a126
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 15:43:43 2017 +0100
    
        Ajout du README
    

Affichage concis
----------------

Pour un affichage plus concis vous pouvez utiliser l'option `--oneline` :

    $ git log --oneline
    081c474 suppression fichier2
    4440471 renommage fichier1 -> fichier2
    3e348d4 Ajout fichier1
    f1fa39c Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Les identifiants des commits sont réduits aux 7 premiers caractères. C'est suffisant la plupart du temps. Cette longueur est ajustée si nécessaire, pour les très gros projets notamment.

Comme c'est un affichage assez pratique, faites-en un alias :

    git config --global alias.lg "log --oneline"
    

Vous pouvez maintenant utiliser `git lg` pour visualiser un log concis.

Plus de détail
--------------

Pour avoir plus de détail sur un commit particulier vous pouvez utiliser `git show` suivi de l'identifiant du commit (6766a32 dans notre cas, à adapter en fonction de votre propre historique) :

    $ git show 6766a32
    commit 6766a32f8324827802f67d11f8ba57c7852b397d
    Author: Emmanuel Leguy <Emmanuel.Leguy@univ-lille1.fr>
    Date:   Wed Jan 11 15:56:17 2017 +0100
    
        ajout procédure ecrire()
    
    diff --git a/hello.py b/hello.py
    index ed708ec..6ae12ae 100644
    --- a/hello.py
    +++ b/hello.py
    @@ -1 +1,4 @@
    +def ecrire(chaine):
    +    print chaine
    +
     print "Hello world!"
    

Pour aller plus loin...
-----------------------

Si vous voulez vous exercer d'avantage sur la manipulation des 3 espaces de GIT (espace de travail, index et dépôt), allez à l'exercice [Brassens aurait adoré GIT !](../autonomie1/)