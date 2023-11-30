Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `tpfiles/tpgit-local` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/tpgit-local tpgit`

Une **branche** est une succession de commits, avec une référence _nommée_ désignant la tête de cette succession.  
Lorsque que l'on initialise un dépôt Git, la branche par défaut se nomme **master**.

Lorsqu'on veut faire évoluer notre projet, cela peut avoir des effets de bord non désirés et créer des bugs. Nous voudrions pouvoir garder intacte la version _stable_ courante tant que nous n'avons pas testé correctement notre nouvelle version. Il nous faut donc avoir la possibilité de pouvoir _enregistrer_ notre travail en cours dans une succession de commits, et donc une **branche**, qui soit gérée _séparément_ de la version _stable_.

Une bonne pratique est ainsi de créer une nouvelle branche pour chaque évolution, que ce soit une nouvelle fonctionnalité, une amélioration ou une correction de bug. Cela donne une grande souplesse et tranquillité d'esprit. Si l'évolution s'avère inutile ou même catastrophique, ce n'est pas grave, il suffira d'abandonner la branche et de la supprimer sans que cela ait d'impact sur la branche contenant la version _stable_.

Git offre des outils puissants permettant de gérer les évolutions sous forme de branches. Une branche est rattachée à un commit donné, point de départ du travail sur l'évolution. Ce commit peut faire partie de la branche **master** ou de toute autre branche. Un historique complet est donc un arbre, ou plus exactement un graphe acyclique direct (_Direct Acyclic Graph_, ou DAG, en anglais).

Création d'une branche
----------------------

L'utilisation des branches est utile même dans un contexte local. Nous allons donc reprendre notre dépôt initial `tpgit-local`.

Tout d'abord, un peu de révision sur l'usage de la commande `git reset`. En effet, nous allons commencer par un peu de ménage dans notre dépôt en supprimant (option `--hard` pour vraiment tout oublier) tous les commits suivant celui qui intègre l'utilisation de la procédure ecrire(). Vous devriez revenir à un historique qui ressemble à :

    $ git log --oneline --decorate
    f1fa39c (HEAD -> master) Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Si ce n'est déjà fait, créer un alias pour cette commande : git config alias.lg "log --oneline --decorate"

Ensuite nous créons une nouvelle branche nommée 'wip' :

    $ git branch wip
    

Nous obtenons l'historique suivant (remarquez la position des références, HEAD, master et wip) :

    $ git lg
    f1fa39c (HEAD -> master, wip) Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

Nous avons donc simplement créé une nouvelle référence, nommée 'wip', placée pour l'instant sur le dernier commit (celui pointé par HEAD).

Conceptuellement, se placer sur une branche, c'est faire en sorte que notre répertoire de travail soit _en lien avec le dernier commit de cette branche_. Nous savons que ce lien est défini par la position de la référence **HEAD** et nous connaissons la commande qui permet de manipuler cette référence : c'est la commande `git checkout`.

Pour se placer dans la branche **wip**, il suffit donc de déplacer **HEAD** sur le commit référence par **wip** :

    $ git checkout wip
    

Vous pouvez faire les deux actions précédentes (création et déplacement) grâce au raccourci suivant :

    $ git checkout -b wip
    

`git lg` nous donne :

    f1fa39c (HEAD -> wip, master) Utilisation de la procédure ecrire()
    6766a32 ajout procédure ecrire()
    b9abeb8 ajout programme hello
    5e6ae03 Ajout du README
    

La référence **HEAD** pointe maintenant effectivement sur **wip**. Résultat : tout nouveau commit sera ajouté après **wip** et non plus après **master**. C'est cela qui va créé une _branche_, une succession _séparée_ de commits.

Utilisons **explainGit** pour bien comprendre :

*   `git branch wip`, crée une nouvelle référence qui pointe sur le commit référence par HEAD (qui lui pointe sur 'master')
*   `git checkout wip`, fait pointer HEAD sur 'wip'. Cela est repésenté graphiquement par le fait que `HEAD` est directement en dessous de `wip`.
*   `git commit`, ajoute un commit après le HEAD, et donc fait progresser la référence 'wip' (toujours pointé par `HEAD`).
*   `git commit`, nouveau commit sur 'wip'
*   `git checkout master`, fait revenir HEAD sur le commit référencé par 'master'
*   `git commit`, ajoute un commit après le HEAD, et donc après 'master', on voit les 'branches' apparaîtrent...

N'oubliez pas qu'en plus de déplacer la référence HEAD, l'objectif de la commande `git checkout` est surtout de modifier le contenu du répertoire de travail pour qu'il reflète le contenu du commit pointé par HEAD.

Ainsi, en changeant de branche vous accédez à des versions de vos fichiers pouvant être totalement différentes, et ce extrêmement rapidement comme vous aurez bientôt l'occasion de le tester.

Ne passez pas à la suite sans être sûr d'avoir bien compris ce principe.

Visualisation avancée de l'historique
-------------------------------------

Pour visualiser l'arbre des commits, vous pouvez utiliser `git log` avec l'option `--graph`. Et pour voir toutes les branches, vous pouvez utiliser l'option `--all`. Cela donnera ce type d'affichage :

    $ git log --oneline --decorate --graph --all
    * a886991 (wip) Affichage en minuscules
    * 2d892e6 Ajout de la procédure ecrireXFois()
    | * 0ed54e0 (HEAD -> master) Affichage en majuscules
    |/  
    * f1fa39c Utilisation de la procédure ecrire()
    * 6766a32 ajout procédure ecrire()
    * b9abeb8 ajout programme hello
    * 5e6ae03 Ajout du README
    
    

Vous pouvez ajouter l'option `--graph` à votre alias `lg`. N'ajoutez pas l'option `--all` à votre alias car il souvent pratique de ne visualiser qu'une seule branche à la fois. Si vous souhaitez voir toutes les branches il vous suffira de tapez `git lg --all`.

var explaingit\_content = \[ { name: 'Branches', height: 350, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '090e2b8', parent: '7eb7654', tags: \['master'\]} \] } \];