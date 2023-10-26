_**Remarque importante** : ce chapître est **optionel** . Son objectif est de montrer comment héberger un dépot de référence sur une machine. En l'occurence, la station de travail d'un des collaborateurs, ce qui reste une pratique rarissime à cause des raisons évoquées au début du [chapître précédent](../distant/). Ne faites ce châpitre que si vous avez fini le TP pour avoir le temps d'aborder les concepts (plus essentiels) présentés dans les châpitres suivants_

L'objectif ici est d'héberger un dépôt de référence sur une des 2 machines du binôme, et de vous exercer avec les commandes que nous avons vues dans les chapitres précédents.

Créer en local un dépôt de référence
------------------------------------

Chaque binôme va maintenant désigner la machine qui va stocker le dépôt de référence.

Sur cette machine vous allez créer un dépôt vide dont le nom sera 'ref' :

    $ git init --bare ref.git
    

L'option `--bare` crée un dépôt sans espace de travail. Le répertoire ainsi créé ne contient que l'historique (équivalent du répertoire .git). Cette option est utilisée sur les serveurs (gitlab, github...) où un espace de travail est inutile. Néanmoins, vous pouvez rentrer dans ce répertoire et exécuter les commandes `git log`, `git show` ou encore `git diff`.

Mettez les droits suivants pour éviter tout problème de droit d'accès (on donne les droits à tout le monde, ce n'est pas bien, dans un cas d'usage réel il faut régler ça plus proprement !) :

    $ chmod -Rf a+w ref.git
    

Poussez votre dépôt local
-------------------------

Sur l'autre machine du binôme, copiez le répertoire `tpgit-local` (que vous avez précieusement conservé), dans le répertoire `tpgit1`, dans lequel vous rentrez. Vous allez configurer la machine qui stocke le dépôt ref.git comme dépôt distant (à adapter à votre cas, <uid> est le nom de login de l'utilisateur de la machine qui héberge le dépôt, <nom\_machine> est le nom de cette machine) :

    $ git remote add origin ssh://<uid>@<nom_machine>/home/<uid>/ref.git
    

Cela créé un lien nommé 'origin' vers le dépôt distant. Vous pouvez le vérifier :

    $ git remote -v
    

Poussez votre dépôt sur `origin`:

    $ git push -u origin master
    

Ici l'historique pointé par **master** est envoyé au dépôt distant référencé par le nom `origin`.

Retournez sur l'autre machine et clonez le dépôt :

    $ git clone ssh://<uid>@<nom_machine>/home/<uid>/ref.git tpgit1
    

Chacun a donc une version du dépôt dans un répertoire 'tpgit1'.

Modifications de chaque côté
----------------------------

Vous allez maintenant à tour de rôle faire une modification comme lors de l'utilisation précédente de gitlab.

*   Sur la première machine, modifiez le README.md, commitez et poussez
*   Sur la deuxième machine, ajoutez un commentaire à hello.py, commitez.
*   Récupérez la modification du README.md (`git pull`).
*   Poussez votre commit
*   `git pull` sur l'autre machine
*   Testez d'autres modifications dans un autre ordre...

[Revenez dans le cours du TP](../branches/)
-------------------------------------------