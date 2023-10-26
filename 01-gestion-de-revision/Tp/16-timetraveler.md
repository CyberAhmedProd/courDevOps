J'ai changé d'avis sur une ancienne modification
================================================

Lors du développement d'un code, il arrive que l'on ait besoin d'adapter une ancienne modification (un ancien commit) suite à un besoin apparu plus tardivement en cours de travail.

Bien sûr, cela ne doit pas arriver si la phase de spécifications a été correctement réalisée, mais nous sommes à l'ère du _Développement Agile_, les spécifications peuvent donc s'affiner au cours du temps (no comment !!!).

Continuons avec l'exemple utilisé dans le chapitre précédent, on nettoie le répertoire de travail par un `git reset --hard HEAD`.

Votre historique doit être semblable à :

    033a573 (HEAD -> master) Utilisation de la nouvelle fonction
    6d369a6 Ajout d'une nouvelle fonction
    3660076 Premier import
    

**Note:** Les identifiants des commits (les _hashcodes SHA1_) que vous obtiendrez seront différents. La suite doit donc être adaptée à votre cas.  
Notez dans un coin la valeur de vos 3 identifiants, ils vous serviront à la toute fin de ce chapitre.

Pour une raison quelconque, on considère maintenant que la chaîne **"TP Git"** présente dans la fonction **display()** aurait dû être mise dans une constante, de la façon suivante :

    const char *TITLE = "TP Git";
    
    void display(char *msg)
    {
        printf("%s. %s.", TITLE, msg);
    }
    

L'ajout de la fonction ayant déjà été validé (commit '6d369a6'), il semble être trop tard. Avec ce que vous connaissez pour l'instant, il vous faudrait créer un commit spécifique pour cette modification tardive, pour aboutir à un historique tel que :

    53f9b3d (HEAD -> master) Supplément au commit 6d369a6: Utilisation d'une constante
    033a573 Utilisation de la nouvelle fonction
    6d369a6 Ajout d'une nouvelle fonction
    3660076 Premier import```
    

Il n'y a pas d'autres solutions si les commits précédents ont déjà été transférés dans un dépôt partagé, mais **tant qu'ils ne sont présents que dans notre dépôt local**, alors il est possible de faire plus _propre_.

Modifier le passé
=================

Il nous faut donc revenir en arrière dans _l'histoire_, juste avant le moment où le commit '6d369a6' a été réalisé, modifier le fichier source et le valider, puis retourner dans le présent en réappliquant les commits qui avaient suivis (dans notre cas, uniquement le commit '033a573').

Nous avons déjà vu une commande permettant de modifier l'enchaînement d'une séquence de commits : `git rebase`. Il existe une version interactive de cette commande, véritable _couteau suisse_ de la manipulation de l'histoire, à faire pâlir Doc Brown de jalousie.

Le principe _technique_ est de réappliquer une séquence de commits à partir d'un point donné, mais en interrompant le processus pour pouvoir éditer le commit qui nous intéresse.

L'historique actuel est donc :

    033a573 (HEAD -> master) Utilisation de la nouvelle fonction
    6d369a6 Ajout d'une nouvelle fonction
    3660076 Premier import
    

Pour revenir _avant_ '6d369a6', utilisez la commande :

    $ git rebase -i 6d369a6^
    

Git ouvre alors l'éditeur de texte (on est donc dans **vim**), avec une liste de _choses à faire_ :

    pick 6d369a6 Ajout d'une nouvelle fonction
    pick 033a573 Utilisation de la nouvelle fonction
    ...
    # Commands:
    # p, pick = use commit
    ...
    # e, edit = use commit, but stop for amending
    
    

Les mots clés utilisables pour spécifier les actions à réaliser sont rappelés par Git. On verra un peu plus dans le détail plus tard.

On voit que par défaut Git va effectuer une opération **pick** à chacun des deux commits ; autrement dît, il va _appliquer_ les commits, en commençant par le commit 6d369a6.

Vous allez lui demander d'interrompre le processus, en remplaçant la commande **pick** par une commande **edit**, puis vous sauvegardez la liste et vous quittez **vim** (`<esc>:wq`) :

    edit 6d369a6 Ajout d'une nouvelle fonction
    pick 033a573 Utilisation de la nouvelle fonction
    

Git vous indique alors la marche à suivre pour la suite :

    Stopped at 6d369a6... Ajout d'une nouvelle fonction
    You can amend the commit now, with
    
        git commit --amend
    
    Once you are satisfied with your changes, run
    
        git rebase --continue
    

Le commit 6d369a6 a été appliqué, et nous pouvons l'_amender_ à l'aide de `git commit --amend` comme nous l'avons déjà vu.

Modifiez donc main.c de la façon suivante :

    --- a/main.c
    +++ b/main.c
    @@ -1,9 +1,11 @@
     #include <stdio.h>
     #include <stdlib.h>
    
    +const char *TITLE = "TP Git";
    +
     void display(char *msg)
     {
    -       printf("TP Git. %s.\n", msg);
    +       printf("%s. %s.\n", TITLE, msg);
     }
    
     int main(int argc, char *argv[])
    

Puis amendez le commit actuel : `git add main.c`, `git commit --amend`

Utilisez `git status` qui vous indique ce que vous pouvez faire maintenant :

    rebasage interactif en cours ; sur 3660076
    Dernière commande effectuée (1 commande effectuée) :
       edit 6d369a6 Ajout d'une nouvelle fonction
    Prochaine commande à effectuer (1 commande restante) :
       pick 033a573 Utilisation de la nouvelle fonction
      (utilisez "git rebase --edit-todo" pour voir et éditer)
    Vous êtes actuellement en train d'éditer un commit pendant un rebasage de la branche 'master' sur '3660076'.
      (utilisez "git commit --amend" pour corriger le commit actuel)
      (utilisez "git rebase --continue" quand vous êtes satisfait de vos modifications)
    
    nothing to commit, working tree clean
    

Vous pouvez donc continuer à corriger le commit, ou continuer le processus de rebasage.  
C'est ce que nous faisons maintenant :

    $ git rebase --continue
    Successfully rebased and updated refs/heads/master.
    

À l'aide de `git show HEAD^`, vérifiez que vos modifications sont bien dans l'avant dernier commit, comme attendu.

Et voilà ! On peut maintenant continuer notre travail.

#### Attention

Les modifications que vous introduisez sur le commit passé peuvent éventuellement être incompatibles avec les commits suivants. Dans ce cas, des conflits peuvent apparaître lors du processus de rebasage, qu'il vous faudra régler comme lors d'un `git rebase` classique.

#### Pour les plus curieux

Un coup d'oeil sur l'historique (`git log --oneline`) montrera que les identifiants des 2 derniers commits ont changés.

Le contenu du fichier **main.c** ayant été modifié, et les identifiants étant des clés de hachage, c'est tout à fait normal.

Cela veut dire 2 choses : 1) Git a recréé _de toute pièce_ de nouveaux commits, et 2) les anciens commits sont toujours accessibles !  
Pour vous en convaincre, essayez `git show 6d369a6` et comparez-le à `git show HEAD^` ('6d369a6' étant l'identifiant du commit "Ajout d'une nouvelle fonction" avant que vous le changiez).

En cas d'erreur _grave_ de manipulation, il existe le plus souvent une manière de revenir en arrière, mais cela implique d'utiliser des commandes de _plomberie_ (pour reprendre le terme en usage) permettant d'accéder aux objets _bas niveau_ qui sont manipulés par Git. Pas de panique donc, votre moteur de recherche Web vous permettra sûrement de trouver comment réparer les dégats.