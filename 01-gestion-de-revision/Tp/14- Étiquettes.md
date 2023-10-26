Faire des branches pour _protéger_ sa branche principale ('master') est une bonne pratique. Cela permet d'avoir en permanence une version de référence (dont les tests passent) de votre projet.  
Cette version, considérée comme suffisamment stable, peut avoir fait l'objet d'une livraison. Il est souvent utile de **marquer** toutes les versions stables d'un projet au cours du temps en leur donnant un numéro : '1.3.25', 'v0.4', 'v0.8RC2'...

Git permet de mettre une _étiquette_ sur un commit grâce à la commande `git tag`.

Il existe 2 types d'étiquettes : les étiquettes légères et les étiquettes annotées.

#### Étiquettes légères

Pour étiqueter le commit courant, vous pouvez simplement utiliser :

    $ git tag v1.0
    

Pour étiqueter un commit 'après-coup' :

    $ git tag v0.4 <id_commit>
    

Cela crée une référence _constante_ sur le commit '<id\_commit>'. Elle est _constante_ car elle ne peut pas être déplacée par `git reset`.  
Vous pouvez néanmoins, en cas d'erreur, forcer la re-création de l'étiquette à l'aide de `git tag -f <tag_name> <new_id_commit>` (à ne pas utiliser si l'étiquette a été poussée sur un dépôt partagé).

#### Étiquettes annotées

Une étiquette annotée est un _objet de commit_, c'est-à-dire une donnée contenant une date, un auteur et un commentaire.  
Elle est crée à l'aide de :

    $ git tag -a v3.1 <id_commit>
    

L'éditeur est ouvert afin de vous permettre d'écrire un message de commentaire.

Un `git show <tag_name>` affichera le contenu de l'étiquette avant le contenu du commit associé.  
Exemple :

    tag v2
    Tagger: sdegrande <samuel.degrande@univ-lille1.fr>
    Date:   Wed Jan 18 07:34:58 2017 +0100
    
    ** Version 2
    Notes de version : l'affichage est converti en majuscules.
    
    commit 452fe81a260967edd4ef988fd3df0aa6b7292761
    Author: sdegrande <samuel.degrande@univ-lille1.fr>
    Date:   Wed Jan 11 08:39:35 2017 +0100
    
        Affichage en majuscules
    
    diff --git a/hello.py b/hello.py
    index 320c425..a0b8c32 100644
    --- a/hello.py
    +++ b/hello.py
    @@ -1,4 +1,4 @@
     def ecrire(chaine):
    -    print chaine
    +    print chaine.upper()
    

Utilisation
-----------

Une étiquette étant une référence, vous l'utiliserez principalement comme un _alias_ vers un commit particulier.  
Vous pouvez ainsi faire un `git checkout <tag_name>` pour vous déplacer sur le commit en question, ou un `git branch <tag_name>` pour créer une branche à partir de ce commit.

Il est conseillé de n'utiliser les étiquettes légères que comme _marques pages_ personnelles, et de ne pas les pousser sur un dépôt partagé.  
Une fois que vous n'avez plus besoin de ce _marque page_, vous pouvez le supprimer à l'aide de `git tag -d <tag_name>`.

Au contraire, les étiquettes annotées sont elles destinées à être partagées, par le fait qu'elles contiennent des informations utiles (date, auteur, commentaire).

Pour lister les étiquettes de votre projet, il vous suffit d'exécuter simplement `git tag` sans paramètre.  
La commande `git log --oneline --decorate` vous permet d'afficher les étiquettes associées à un commit.

Une étiquette n'est pas automatiquement transmise sur le dépôt distant lorsque le commit associé est poussé. Il faut le faire _manuellement_ à l'aide de `git push` :

    $ git push origin v1.0
    

Il y a cependant une option permettant de transmettre les étiquettes en même temps que les commits : `git push --follow-tags`.  
Cette option a l'avantage de ne pousser que les étiquettes annotées qui référencent des commits existants sur le dépôt.  
Elle peut être activée par défaut par `git config push.followTags true`. Une fois ceci fait, plus besoin de spécifier l'option `--follow-tags`.

Les étiquettes sont automatiquement récupérées lors des `git fetch`.

À vous
------

Vous pouvez ainsi reprendre votre projet et _tagguer_ certaines de ses étapes: _l'utilisation de la procédure ecrire()_, _l'affichage multiple_, _l'affichage en majuscules_...

Testez les différentes commandes vues dans ce chapitre.