Rapport entre répertoire de travail et commits
----------------------------------------------

Pour mieux comprendre la suite, il nous faut préciser un peu mieux le rapport entretenu par Git entre l'historique et le répertoire de travail.

De manière conceptuelle, sans trop entrer dans les détails donc, il faut considérer que :

*   en utilisant la commande `git add`, on demande à Git de _suivre_ certains fichiers, c'est-à-dire de prendre en charge la gestion de l'évolution de ces fichiers,
*   par la commande `git commit`, nous demandons à Git d'enregistrer un _snapshot_, c'est-à-dire une photographie instantanée de l'état des fichiers suivis (plus exactement, des modifications qui ont été placées dans l'index, mais il s'agit là en fait d'une facilité),
*   une suite des commits constitue un _historique_.

Git va alors voir votre répertoire de travail comme étant en fait composé de :

1.  l'ensemble des fichiers _suivis_, **dans leur version correspondant à UN SNAPSHOT COURANT** (un commit)
2.  **PLUS** les modifications existant sur ces fichiers par rapport à cette version spécifique
3.  **PLUS** des fichiers non suivis (pour lesquels on n'a pas effectué de `git add`)

_!!! Relisez bien cela, c'est un des secrets pour utiliser Git avec facilité !!!_

Pour désigner le 'SNAPSHOT COURANT', Git utilise une référence (c'est à dire, un pointeur vers un commit) qui s'appelle **HEAD**.  
Il existe une autre référence, que l'on va bientôt manipuler, et qui pointe vers la tête de l'historique. Sachez juste pour l'instant qu'elle s'appelle **master**, on verra plus tard pourquoi elle porte ce nom.

Manipulation de la référence HEAD
---------------------------------

Nous avons déjà vu une commande qui modifie ces deux références. En effet la commande `git commit` effectue deux choses :

1.  Elle enregistre un _snapshot_ ayant un lien de parenté avec le commit référencé par HEAD
2.  Elle déplace la référence HEAD et la référence master sur ce nouveau commit.

Vous pouvez visualiser cette opération avec le simulateur **explainGit** ci-dessous. Utilisez plusieurs fois `git commit`, et voyez comment l'historique et les deux références évoluent (entrez vos commandes dans la zone verte de saisie de texte, voir [Annexes/Utilisation d'explainGit](../annexes/explaingit/) pour plus de détails)

Une autre commande modifiant la référence HEAD, que vous manipulerez bientôt, est `git checkout`. Celle-ci va spécifiquement déplacer le HEAD vers un commit que vous lui précisez. En règle générale, elle ne peut être appliquée que si il n'y a pas de modifications en cours dans vos fichiers. Dans le simulateur explainGit ci-dessus, essayez les commandes suivantes et observez bien ce qui se passe :

1.  git checkout 56cd8a9 : la référence HEAD se déplace sur le commit 56cd8a9
2.  git checkout HEAD^ : la référence HEAD se déplace d'un cran vers le gauche (HEAD^ désigne le commit précédant HEAD)
3.  git checkout master : la référence HEAD se déplace vers la référence master, c'est-à-dire la tête de l'historique

Pour savoir sur quel commit pointent les références, vous pouvez, entre autre, utiliser l'option `--decorate` de `git log` (ajoutez cette option à votre alias 'lg').

Influence sur le répertoire de travail
--------------------------------------

Si vous avez bien compris ce qui a été dit plus haut, que se passe-t-il au niveau de votre répertoire de travail lorsque 'HEAD' est déplacé ?

Rappelons : votre répertoire de travail est constitué des fichiers suivis _dans leur version correspondant au commit référencé par 'HEAD'_

Par conséquent, `git checkout 56cd8a9`, par exemple, va déplacer 'HEAD' sur le commit 56cd8a9, puis va faire en sorte que votre répertoire de travail soit constitué des fichiers _dans leur version correspondant au nouveau 'HEAD'_.  
Les fichiers sont donc modifiés, ajoutés, ou supprimés, pour que votre répertoire de travail soit dans l'état qui était le sien au moment du commit 56cd8a9...

Vous comprenez pourquoi il n'est pas possible (sauf dans un cas bien précis) d'utiliser `git checkout` si il y a des modifications en cours : elles seraient perdues. C'est ce que Git vous répondra, en refusant d'effectuer le `git checkout`.

Avant de passer à la suite pour la mise en pratique, assurez vous de bien avoir assimilé ce chapitre.

var explaingit\_content = \[ { name: 'Commit', height: 275, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '56cd8a9', parent: '7eb7654'}, {id: 'ad86cfe', parent: '56cd8a9', tags: \['master'\]} \], } \];