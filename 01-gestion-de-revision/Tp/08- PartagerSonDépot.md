Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/tpgit-local` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-local tpgit`

Git est un VCS distribué ; cela signifie que tous les collaborateurs au projet possèdent une _copie_ du dépot sur leur machine.  
Centraliser sur un serveur (par exemple) un dépot qui fera foi n'est pas une obligation. Tout dépend de votre organisation de travail.

Il est à noter que en conséquence le dépot central n'est pas un point critique du système. En particulier, une sauvegarde n'est pas indispensable (mais reste une bonne pratique !) puisque chaque collaborateur possède une copie.

Attention ! Il ne faut pas modifier l'historique des modifications qui ont été partagées. Par exemple ne faites pas de `git reset`. Vos collaborateurs auront peut-être commencé à faire des modifications à partir d'un commit que vous avez supprimé. Si vous voulez annuler un commit, préférez `git revert`.

Partage par simple copie
------------------------

Une manière simple de partager son dépot et de le copier sur un support (clé USB, par exemple) et de le transmettre.

En vous plaçant dans le répertoire racine de votre répertoire de travail, recopiez votre dépot :

    $ cp -Rf tpgit tpgit-cp
    $ cd tpgit-cp
    

Constatez avec `git log` que vous venez de créer un clone, que vous pouvez maintenant compresser sous forme de tar.gz et envoyer par mail, par exemple.

Vous pouvez aussi faire la même chose avec la commande `git clone` :

    $ git clone tpgit tpgit-clone
    

Entrez dans le répertoire tpgit-clone pour constater que vous obtenez le même résultat.

Partager via ssh
----------------

`git clone` peut s'utiliser avec divers protocoles réseau (ftp, http, ssh...).

Pour tester 'ssh', commençons par un test en local (remplacez '<uid>' par votre nom de login) :

    $ git clone ssh://<uid>@localhost/home/<uid>/tpgit tpgit-ssh
    

Vérifiez le répertoire tpgit-ssh.

Remarque : vous auriez pu faire un simple `scp` mais nous verrons plus tard que ce n'est pas complêtement équivalent...

Partager via ssh via le réseau
------------------------------

Réalisable sous certaines conditions

Cette manip n'est réalisable que si vous pouvez acceder à la machine de votre binôme (votre voisin) via ssh. Cela n'est possible que sous certaines conditions:  
\- un serveur ssh doit être installé sur la machine  
\- vous devez avoir un compte sur cette machine  
\- vous devez connaitre l'adresse IP de la machine  
\- le protocole ssh doit être autorisé sur le réseau

Passez cette étape si toutes les conditions ne sont pas remplies.

L'intérêt du protocole 'ssh' est de l'utiliser entre deux machines via le réseau. C'est donc ce que vous allez faire en clonant depuis votre machine le dépot de votre binôme. Avant cela il faut que votre binôme s'assure que vous avez la _permission_ d'entrez via ssh dans le répertoire de son dépot.

Demandez à votre binôme le nom de sa machine et le chemin complet de son dépot.

Faites un clone de son dépot via ssh :

    $ git clone ssh://<votre_uid>@<nom_poste_binome>/home/<uid_binome>/tpgit tpgit-binome
    

Vérifiez le contenu du répertoire tpgit-binome.