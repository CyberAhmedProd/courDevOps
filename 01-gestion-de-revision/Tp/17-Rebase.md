Un petit oubli (volontaire...)
==============================

Dans le chapitre précédent, nous avons passé sous silence un point important : un rebasage interactif ne peut se faire **que** si le répertoire de travail est _propre_ (aucune modification non indexée ou non validée).  
Dans le cas contraire, Git refusera d'exécuter le `git rebase -i` qui entraînerait sinon la disparition de ces modifications en cours.

Or c'est très souvent lorsque l'on travaille sur une fonctionnalité que l'on se rend compte, après coup, d'un oubli dans un ancien commit, ou de l'introduction d'un bug ou d'une régression que l'on n'avait pas encore détecté.  
Dans ce cas, notre répertoire de travail est loin d'être _propre_, et le rebasage interactif ne peut se faire.

Une première possibilité offerte par Git est le _remisage_ temporaire.

Remisage
========

Modifiez le fichier **main.c** de la manière suivante :

    --- a/main.c
    +++ b/main.c
    @@ -11,5 +11,6 @@ void display(char *msg)
     int main(int argc, char *argv[])
     {
         display("Exemple de changement d'historique.");
    +    display("Test du remisage.");
         return EXIT_SUCCESS;
     }
    

En testant ce code (`make main; ./main`) vous verrez peut-être mieux maintenant l'erreur qui a été introduite lorsque la fonction **display()** a été écrite : il y a 2 points en fin de chaque ligne.

Comme nous sommes toujours dans l'idée de créer une séquence _propre_ de commits avant de les _envoyer_ sur le dépôt partagé, nous voulons utiliser le rebasage interactif pour corriger le commit qui ajoute la fonction **display()**.  
En utilisant la commande `git log --oneline`, repérez l'identifant de ce commit ('e537d5c', dans notre cas), et tentez le rebasage :

    $ git rebase -i e537d5c^
    Cannot rebase: You have unstaged changes.
    Veuillez les valider ou les remiser.
    

Résultat: Git ne veut pas lancer la procédure de rebasage, et propose de _remiser_ les modifications en cours.

Le _remisage_ consiste à placer **temporairement** toutes les modifications présentes dans le répertoire de travail (et dans l'index) dans un espace spécifique, le **_stash_**.

Utilisez la commande :

    $ git stash
    Saved working directory and index state WIP on master: eb54d83 Utilisation de la nouvelle fonction
    HEAD est maintenant à eb54d83 Utilisation de la nouvelle fonction
    

Git nous indique que les modifications existantes dans le répertoire de travail **et** dans l'index ont été sauvegardées, et que nous sommes revenus dans l'étât du dernier commit (le 'eb54d83').  
C'est donc équivalent à un `git reset --hard HEAD`, mais avec sauvegarde.

L'espace **stash** fonctionne comme une pile de sauvegarde.  
Utilisez la commande `git stash list` pour savoir ce qui se trouve dans cet espace :

    $ git stash list
    stash@{0}: WIP on master: eb54d83 Utilisation de la nouvelle fonction
    

Pour afficher les modifications enregistrées en un point de sauvegarde, utilisez `git stash show -p stash@{0}`

Correction du bug
=================

Vous pouvez maintenant procéder à la correction du bug, en utilisant la méthode du rebasage interactif vue dans le chapitre précédent.

La correction à apporter est la suivante :

    --- a/main.c
    +++ b/main.c
    @@ -5,7 +5,7 @@ const char *TITLE = "TP Git";
    
     void display(char *msg)
     {
    -       printf("%s. %s.\n", TITLE, msg);
    +       printf("%s. %s\n", TITLE, msg);
     }
    
     int main(int argc, char *argv[])
    

Reprise du travail en cours
===========================

Pour _remettre_ dans votre répertoire de travail et votre index les modifications qui ont été sauvegardées, utilisez :

    $ git stash pop stash@{0}
    Fusion automatique de main.c
    Sur la branche master
    Modifications qui ne seront pas validées :
      (utilisez "git add <fichier>..." pour mettre à jour ce qui sera validé)
      (utilisez "git checkout -- <fichier>..." pour annuler les modifications dans la copie de travail)
    
        modifié :         main.c
    
    aucune modification n'a été ajoutée à la validation (utilisez "git add" ou "git commit -a")
    stash@{0} supprimé (552a542e86a1b6ad02c52627b3e7e312ca3cfbde)
    

Git indique qu'une fusion a été opérée entre le répertoire de travail et le _pseudo commit_ stash@{0}. S'agissant d'une fusion, il pourrait donc exister un conflit, qu'il vous faudra régler comme vu précédemment.  
Git indique également qu'il n'avait pas sauvegardé de modifications indexées (dans le cas contraire, Git les auraient _remises_ dans l'index).  
Enfin, Git précise que l'enregistrement sur le _stash_ a été supprimé (dû à l'utilisation de la commande 'pop' plutôt que 'apply').

À l'aide de `git diff`, vérifiez que notre modification en cours est bien présente dans le fichier **main.c**.

Utilisez `git stash list` qui fournit une liste vide, l'opération `pop` ayant supprimé stash@{0} après l'avoir fusionné avec succés.

La commande `git stash drop stash@{0}` permet de supprimer un enregistrement du **stash** sans l'appliquer, et `git stash clear` enlève tout ce qui se trouve.

#### Utilisations dérivées

Même si ce n'est pas son objectif initial, le remisage peut être utilisé pour _transférer_ des modifications en cours d'une branche sur une autre, de la manière suivante :

    $ git stash
    $ git checkout mon_autre_branche
    $ git stash pop stash@{0}
    $ git checkout ma_branche_de_depart
    

Avec l'option `git stash --keep-index`, seules les modifications qui ne sont pas encore indexées sont remisées.  
Cette option est utile si vous avez besoin en urgence de mettre une de vos modifications en cours dans un dépôt partagé. Vous procéderez alors de la manière suivante :

    $ git add -p  (pour ajouter la modification en question dans l'index)
    $ git stash --keep-index
    $ tester ce que vous avez conservé, par précaution !
    $ git commit -m "Modification en urgence"
    $ git push
    $ git stash pop  (vous reprenez votre travail)
    

Il existe également une version interactive : `git stash -p` (qui s'utilise comme `git add -p`). En reprenant l'exemple du cas précédent, vous pouvez utiliser cette option si vous devez envoyer en urgence toutes vos modifications **sauf** une _petite_ partie.