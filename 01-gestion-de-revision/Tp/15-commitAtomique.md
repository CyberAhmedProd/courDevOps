*   [Docs](..) »
*   Des commits atomiques !

* * *

Découper ses commits
====================

Une bonne pratique avec Git consiste à décomposer les modifications à enregistrer dans le dépôt en _entités atomiques_ (ou _unitaires_).  
Cette pratique permet de mieux suivre, et donc comprendre, les modifications apportées. Elle permet également de plus facilement retrouver une modification, et de la _renverser_ sans toucher au reste des modifications (quand c'est possible...).

Cependant si, par exemple, on ajoute une nouvelle fonction dans un code source, il faut pouvoir s'assurer du bon fonctionnement de cette fonction avant de déposer les modifications.  
On va donc utiliser cette fonction par ailleurs dans nos sources, compiler le projet et vérifier que le résultat est satisfaisant.

Nous voulons maintenant séparer nos modifications en 2 _commits_ :  
le premier contiendra la définition de la nouvelle fonction, le second contiendra l'utilisation de cette fonction.

Si ces modifications sont dans 2 fichiers séparés, alors vous savez déjà comment procéder :

*   Vous ajoutez et déposez le 1er fichier :

    git add <fichier_contenant_la_fonction>
    git commit -m "Ajout d'une nouvelle fonction"
    

*   Vous ajoutez ensuite et déposez le 2ème fichier :

    git add <fichier_contenant_l_utilisation_de_la_fonction>
    git commit -m "Utilisation de la nouvelle fonction"
    

Mais comment faire si tout est dans le même fichier ?

Préparons un cas d'usage de la façon suivante, dans un nouveau répertoire (pour changer un peu, nous passons au langage C...) :

*   Initialisez Git

    $ git init
    

*   Créez un fichier **main.c**, contenant :

    #include <stdio.h>
    #include <stdlib.h>
    
    int main(int argc, char *argv[])
    {
        printf("TP Git. Exemple de changement d'historique.\n");
        return EXIT_SUCCESS;
    }
    

*   Compilez le programme pour le tester :

    $ make main
    cc     main.c   -o main
    $ ./main
    TP Git. Exemple de changement d'historique.
    $
    

*   Le binaire compilé ne doit pas être mis dans le dépôt. Demandez à Git de l'ignorer :

    echo "main" >> .gitignore
    

*   Ajoutez et validez les 2 fichiers :

    $ git add main.c .gitignore
    $ git commit -m "Premier import"
    

Modifiez maintenant le fichier `main.c` pour y apporter (exactement !) les modifications suivantes :

    --- a/main.c
    +++ b/main.c
    @@ -1,8 +1,13 @@
     #include <stdio.h>
     #include <stdlib.h>
    
    +void display(char *msg)
    +{
    +       printf("TP Git. %s.\n", msg);
    +}
    +
     int main(int argc, char *argv[])
     {
    -       printf("TP Git. Exemple de changement d'historique.\n");
    +       display("Exemple de changement d'historique.");
            return EXIT_SUCCESS;
     }
    

Compilez-le et exécutez-le :

    $ make main
    $ ./main
    TP Git. Exemple de changement d'historique..
    $
    

Vous vous êtes peut-être rendu compte qu'il y a un souci dans le résultat. Ne le corrigez-pas, nous nous en occuperons plus tard !

Ajout interactif
================

Le fichier **main.c** contient maintenant 2 ensembles de modifications que nous voudrions ajouter séparément au dépôt.

L'option `-p` (`--patch`) de la commande `git add` permet de sélectionner _interactivement_ les morceaux de code que nous voulons ajouter à l'index.

Avec cette option, `git add` va _découper_ les modifications en morceaux, appelés _hunks_, qui seront affichés l'un après l'autre, et vous demander ce que vous voulez faire de chacun de ces _hunks_.

Utilisez `git add -p main.c`. Vous obtenez :

    --- a/main.c
    +++ b/main.c
    @@ -1,8 +1,13 @@
     #include <stdio.h>
     #include <stdlib.h>
    
    +void display(char *msg)
    +{
    +   printf("TP Git. %s.\n", msg);
    +}
    +
     int main(int argc, char *argv[])
     {
    -    printf("TP Git. Exemple de changement d'historique.\n");
    +    display("Exemple de changement d'historique.");
        return EXIT_SUCCESS;
     }
    Stage this hunk [y,n,q,a,d,/,s,e,?]?
    

Dans notre cas, les 2 modifications étant proches l'une de l'autre, `git add -p` les place dans le même _hunk_.

Répondez `?` à la question, pour obtenir de l'aide sur les différentes possibilités :

    y - stage this hunk
    n - do not stage this hunk
    q - quit; do not stage this hunk or any of the remaining ones
    ...
    s - split the current hunk into smaller hunks
    ...
    

Il est donc possible de découper le morceau actuel en 2 _hunks_ plus petits, à l'aide de la commande `s`. Vous obtenez alors :

    Split into 2 hunks.
    @@ -1,5 +1,10 @@
     #include <stdio.h>
     #include <stdlib.h>
    
    +void display(char *msg)
    +{
    +   printf("TP Git. %s.\n", msg);
    +}
    +
     int main(int argc, char *argv[])
     {
    Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]?
    

Cette modification doit être incluse dans le 1er commit, vous répondez donc par `y`. Le _hunk_ qui est affiché ensuite ne doit pas être inclus, vous répondez par`n`.

Vérifiez avec les commandes `git status`, `git diff --staged` et `git diff` que le code correspondant à l'ajout de la fonction est bien dans l'index, prêt à être validé, mais que le code d'utilisation de cette fonction est toujours dans la copie de travail, non encore indexé.

Procédez maintenant au nécessaire pour valider le 1er commit, puis ajoutez et validez le reste des modifications dans un 2eme commit.  
`git log` doit vous donner :

    $ git lg
    0422fbc (HEAD -> master) Utilisation de la nouvelle fonction
    1195259 Ajout d'une nouvelle fonction
    3660076 Premier import
    

**Note:** Il est possible d'éditer _à la main_ le contenu d'un _hunk_ à l'aide de la réponse `e`. Cela peut être nécessaire lorsque seule une partie d'une modification doit être ajoutée à l'index, par exemple. Le _hunk_ ainsi modifié doit respecter le format d'un `diff` pour être accepté. À utiliser avec précaution.

Désindexation interactive
=========================

Parfois, on se rend compte après coup qu'une modification a été ajoutée à l'index par erreur.

Après avoir effectué un `git add`, un `git status` nous indique qu'il est possible de désindexer des modifications à l'aide de la commande `git reset HEAD`. Il est également possible de choisir les morceaux de code devant être désindexés avec `git reset -p` (qui est donc la commande opposée à `git add -p`).

Pour tester cette opération, commencez par remettre les 2 derniers commits dans l'index à l'aide de (voir [Correction de commits](../correction/) si vous avez oublié le rôle de l'option '--soft') :

    $ git reset --soft HEAD^^
    $ git status
    Sur la branche master
    Modifications qui seront validées :
      (utilisez "git reset HEAD <fichier>..." pour désindexer)
    
        modifié :         main.c
    

Vérifiez avec `git diff --staged` que vos 2 modifications (ajout d'une fonction et utilisation de cette fonction) sont dans l'index.

Nous voulons donc maintenant _sortir_ de l'index le _hunk_ correspondant à l'utilisation de la fonction.

Pour cela, utilisez la commande `git reset -p`, qui s'utilise comme `git add -p`. Il faudra donc découper le _hunk_ en 2, ne pas _désindexer_ le premier morceau, et _désindexer_ le second morceau.

Vérifiez avec les commandes `git status`, `git diff --staged` et `git diff` que le code correspondant à l'ajout de la fonction est bien dans l'index, prêt à être validé, mais que le code d'utilisation de cette fonction est toujours dans la copie de travail, non encore indexé.

Annulation interactive
======================

Que ce soit lors de l'édition d'un texte ou du développement d'un code, il n'est pas rare que l'on désire abandonner les modifications que l'on vient de faire pour revenir à la version précédente.

Lorsqu'un fichier est modifié, sans que l'on ait ajouté les modifications à l'index, `git status` indique qu'il est possible d'annuler les modifications avec la commande `git checkout`. Avec l'option `-p`, on peut sélectionner les modifications que l'on désire annuler.

Si vous avez réalisé les manipulations du paragraphe précédent, commencez par tout remettre dans le dépôt :

    $ git commit -m "Ajout d'une nouvelle fonction"
    $ git add -u
    $ git commit -m "Utilisation de la nouvelle fonction"
    

Vérifiez avec `git status` qu'il ne reste plus de modifications en cours dans votre répertoire de travail :

    $ git status
    Sur la branche master
    nothing to commit, working tree clean
    

Vous allez maintenant modifier votre fichier avec le contenu suivant :

    --- a/main.c
    +++ b/main.c
    @@ -3,11 +3,13 @@
    
     void display(char *msg)
     {
    -       printf("TP Git. %s.\n", msg);
    +       printf("TP Git :\n");
    +       printf("    %s.\n", msg);
    +
     }
    
     int main(int argc, char *argv[])
     {
    -    display("Exemple de changement d'historique.");
    +    display("Exemple pour test de l'annulation interactive.");
         return EXIT_SUCCESS;
     }
    

Après mûres réflexions, vous voulez revenir en arrière sur les modifications apportées à la fonction **display()**, mais conserver celles de la fonction **main()**.

Utilisez pour cela la commande `git checkout -p`, qui s'utilise comme `git add -p` et `git reset -p`. Il faudra donc découper en 2 le _hunk_ proposé, accepter d'annuler les modifications correspondant au 1er morceau, et refuser pour le 2eme.

Vérifiez avec `git status` et `git diff` qu'il ne reste maintenant dans le répertoire de travail que la modification de la fonction **main()** :

    --- a/main.c
    +++ b/main.c
    @@ -8,6 +8,6 @@ void display(char *msg)
    
     int main(int argc, char *argv[])
     {
    -    display("Exemple de changement d'historique.");
    +    display("Exemple pour test de l'annulation interactive.");
            return EXIT_SUCCESS;
     }
    

À retenir

Avec Git, le transfert des modifications entre les 3 espaces (répertoire de travail, index, dépôt) ne se limite pas à des fichiers complets mais peut s'appliquer à des morceaux de fichiers.

Il ne faut donc pas hésiter à réaliser plusieurs modifications des fichiers sources, permettant d'introduire, tester et valider un ensemble cohérent de changements, pour ensuite découper _proprement_ en commits atomiques.

Attention cependant à ne pas aller trop loin avec cette démarche : plus les modifications sont nombreuses, plus il est fastidieux de découper et regrouper les différents morceaux à placer dans un commit.

On préférera donc plutôt réaliser des commits dès que possible (c'est-à-dire dès qu'une modification est considérée comme validée), quitte à procéder ensuite à une _réécriture de l'histoire_ comme nous allons le voir dans le chapitre suivant.


* * *
