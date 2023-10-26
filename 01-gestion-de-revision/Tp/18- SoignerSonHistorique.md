*   [Docs](..) »
*   Soigner son historique

* * *

Dans les deux chapitres précédents, nous avons présenté une méthode permettant de répondre à la problématique d'un développement _non linéaire_ : en cours d'écriture d'une évolution du projet, nous avons besoin de modifier une partie ayant déjà été placée dans le dépôt. Cette opération est fréquente lorsque toutes les spécifications de l'évolution n'ont pas été précisées par avance.

Le principe est de mettre de côté les modifications non encore indexées et d'utiliser le rebasage interactif pour revenir sur le commit à mettre à jour. Il y a donc une _interruption d'activité_. Git nous permet de faire mieux en committant ce qui est en cours sans trop se soucier de l'historique dans un premier temps !

Si vous avez eu la curiosité de lire les instructions affichées par le `git rebase -i`, vous avez peut-être déjà une petite idée de la puissance de cette commande. Voici les mots-clés proposés par Git pour lui indiquer les opérations à effectuer lors du rebasage :

    # Commands:
    # p, pick = use commit
    # r, reword = use commit, but edit the commit message
    # e, edit = use commit, but stop for amending
    # s, squash = use commit, but meld into previous commit
    # f, fixup = like "squash", but discard this commit's log message
    # x, exec = run command (the rest of the line) using shell
    # d, drop = remove commit
    #
    # These lines can be re-ordered; they are executed from top to bottom.
    #
    # If you remove a line here THAT COMMIT WILL BE LOST.
    

Nous connaissons déjà :

*   **pick**
    
    appliquer le commit tel quel
    
*   **edit**
    
    interrompre le rebasage pour modifier le contenu du commit
    

Trois autres opérations sont intéressantes :

*   **drop**
    
    le commit ne sera pas pris en compte, il sera supprimé du nouvel historique
    
*   **squash/fixup**
    
    le commit sera fusionné avec celui placé avant lui
    
*   **changement d'ordre**
    
    l'ordre dans lequel les commits seront appliqués peut être changé
    

Nous pouvons donc, **_après coup_**, changer d'ordre, fusionner ou supprimer des commits. Cette possibilité nous permet de changer notre façon de travailler : nous _enregistrons_ des commits au fur et à mesure de notre développement, sans nous soucier de la _qualité_ de l'historique généré, et une fois l'évolution achevée, nous reprenons cet historique pour faire le ménage et améliorer ce _qu'il raconte_.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Copiez le répertoire `~/tpfiles/soignerhistorique` dans votre répertoire personnel (homedir) : `cp -Rf ~/tpfiles/soignerhistorique ~` . Placez vous dans le répertoire ainsi créé : `cd ~/soignerhistorique`.  
Ce dépôt reprend l'ensemble des évolutions effectuées sur notre petit programme C depuis le chapitre '[Des commits atomiques !](../atomiques)', sauf que cette fois-ci chaque modification a donné lieu à un commit, comme vous pouvez le voir en observant son historique (les messages de commits ont été préfixés par un numéro pour aider à la compréhension de la suite) :

    a6d1128 (8) modification de display(): Correction d'un bug : 1 point final en trop
    32bb071 (7) modification de main() : Affichage d'une deuxieme ligne de test
    db6dbab (6) modification de display(): Utilisation d'une constante
    ea76fd9 (5) Revert "modification de display(): Affichage sur 2 lignes"
    6f3df50 (4) modification de display(): Affichage sur 2 lignes
    a87c1cd (3) Utilisation de la fonction display() dans main()
    8fbcce0 (2) Ajout de la fonction display()
    61dea6a (1) Premier import : Fonction main()
    

Vous pouvez utiliser \`git show ' pour voir les modifications associées à chaque commit.

Pour aboutir au même résultat que précédemment, nous devons réunir en un seul commit tout ce qui a trait à la fonction **display()**, c'est à dire : supprimer les commits 4 et 5, et fusionner les commits 2, 6 et 8. Le rebasage interactif va nous permettre cela.  
Si nous voulons reprendre **tout** l'historique, alors il faudrait utiliser une commande telle que `git rebase -i 61dea6a^`, sauf qu'il n'existe pas de commit **avant** '61dea6a'. Git ne sait donc pas interpréter cette commande. Une option spécifique est nécessaire : `--root`.

Exécutez donc un `git rebase -i -root`, et modifiez la _liste des opérations à effectuer_ de la manière suivante, pour aboutir au résultat escompté (les flèches indiquent les lignes à modifier, elles ne sont pas à écrire !) :

        pick 61dea6a (1) Premier import : Fonction main()
        pick 8fbcce0 (2) Ajout de la fonction display()
    ->  fixup db6dbab (6) modification de display(): Utilisation d'une constante
    ->  fixup a6d1128 (8) modification de display(): Correction d'un bug : 1 point final en trop
        pick a87c1cd (3) Utilisation de la fonction display() dans main()
    ->  drop 6f3df50 (4) modification de display(): Affichage sur 2 lignes
    ->  drop ea76fd9 (5) Revert "modification de display(): Affichage sur 2 lignes"
        pick 32bb071 (7) modification de main() : Affichage d'une deuxieme ligne de test
    

Une fois le rebasage terminé (a priori sans problèmes !), vous obtenez le nouvel historique suivant :

    $ git log --oneline
    0c289ea (7) modification de main() : Affichage d'une deuxieme ligne de test
    6c42676 (3) Utilisation de la fonction display() dans main()
    d6c833f (2) Ajout de la fonction display()
    f0a8986 (1) Premier import : Fonction main()
    

Vous pouvez vérifier que le contenu de **main.c** est correct.

Effectuez la même opération pour fusionner les commits 3 et 7, et obtenir :

    $ git log --oneline
    8667928 (3) Utilisation de la fonction display() dans main()
    0759de8 (2) Ajout de la fonction display()
    9085de6 (1) Premier import : Fonction main()
    

Et vérifiez de nouveau que **main.c** est toujours correct.

Le rebasage interactif est **très puissant**, mais il n'est pas pour autant **_magique_**. On ne peut pas modifier l'ordre des commits sans s'attendre à des problèmes de conflit, sauf dans le cas où les commits _inversés_ ne touchent pas aux mêmes lignes de texte (ce qui est le cas pour les deux rebasages que vous venez de faire).

Mais que se passerait-il si on avait inversé les commits 6 et 8 ?

Pour rappel, le commit 6 effectue la modification suivante :

    -       printf("TP Git. %s.\n", msg);
    +       printf("%s. %s.\n", TITLE, msg);
    

Et le commit 8 :

    -       printf("%s. %s.\n", TITLE, msg);
    +       printf("%s. %s\n", TITLE, msg);
    

Le commit 8 ne peut donc s'appliquer avant le 6... Il y a un conflit, qu'il faudra résoudre, si on veut les inverser.

Pour tester ce cas, il faut repartir de l'historique initial où tous les commits étaient présents. Mais comment faire pour revenir en arrière sur le rebasage ? Cette fois-ci il semble qu'un `git reset` ne peut nous servir, puisque la séquence des anciens commits a disparu... ou pas...

Parenthèse : Reparlons de HEAD
------------------------------

Nous avons déjà indiqué que Git ne supprime pas réellement les commits. Simplement, ils ne sont plus accessibles à l'aide d'une référence ('HEAD', 'master' ou autre), mais ils restent présents dans le dépôt. Si vous connaissiez l'identifiant de l'ancien commit de tête de la branche 'master', alors vous pourriez restaurer cette séquence avec un `git reset --hard <ancien_id>`. En effet cette commande va déplacer la référence 'HEAD' et la tête de la branche qui lui est associée ('master' en l’occurrence) sur cet ancien commit.

Voyons cela avec **explaiGit** :

var explaingit\_content = \[ { name: 'Rebase', height: 300, commitData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '56cd8a9', parent: '7eb7654', tags: \['master'\]}, {id: '1286ada', parent: '7eb7654'}, {id: '090e2b8', parent: '1286ada'}, {id: 'ee5df4b', parent: '090e2b8', tags: \['wip'\]} \] } \];

*   Passez sur la branche 'wip' (`git checkout wip`)
    
*   Effectuez un rebase sur la branche 'master' (`git rebase master`)
    
    De nouveaux commits sont créés à la suite de la référence 'master', mais les anciens sont toujours présents. Plus aucune référence ne permet cependant de les atteindre, comme le montre **explainGit** en les affichant en grisé.
    
*   "Restaurez" l'ancienne séquence à l'aide de `git reset ee5df4b`
    
    La référence 'wip' et la référence 'HEAD' sont replacées sur ce commit. Les liens de parenté n'étant pas supprimés, l'ancienne séquence de commits est _retrouvée_.  
    Par contre, les commits qui avaient été créés sont maintenant sans référence...
    

Revenons à notre manipulation de l'historique précédente. Vous voudriez revenir en arrière et retrouver les commits d'origine. Hélas, vous n'avez sûrement pas pris la précaution de noter l'identifiant du commit de tête avant de procéder au rebasage interactif. Mais Git l'a fait pour vous ! En effet, Git gère un historique des mouvements effectués sur les références; c'est ce que Git appelle les **_reflogs_**.

Utilisez `git reflog HEAD`, pour afficher l'historique de déplacement de la référence 'HEAD' :

    8667928 HEAD@{0}: rebase -i (finish): returning to refs/heads/master
    8667928 HEAD@{1}: rebase -i (fixup): (3) Utilisation de la fonction display() dans main()
    38c8663 HEAD@{2}: rebase -i (pick): (3) Utilisation de la fonction display() dans main()
    0759de8 HEAD@{3}: rebase -i (pick): (2) Ajout de la fonction display()
    9085de6 HEAD@{4}: rebase -i (pick): (1) Premier import : Fonction main()
    4d48445 HEAD@{5}: rebase -i (pick): (1) Premier import : Fonction main()
    dcebf5b HEAD@{6}: rebase -i (start): checkout dcebf5b34a5d40d003a9a2eaa9d1b833b6a8b35e
    0c289ea HEAD@{7}: rebase -i (finish): returning to refs/heads/master
    0c289ea HEAD@{8}: rebase -i (pick): (7) modification de main() : Affichage d'une deuxieme ligne de test
    6c42676 HEAD@{9}: rebase -i (pick): (3) Utilisation de la fonction display() dans main()
    d6c833f HEAD@{10}: rebase -i (fixup): (2) Ajout de la fonction display()
    d0477eb HEAD@{11}: rebase -i (fixup): # This is a combination of 2 commits.
    8d32e0a HEAD@{12}: rebase -i (pick): (2) Ajout de la fonction display()
    f0a8986 HEAD@{13}: rebase -i (pick): (1) Premier import : Fonction main()
    a92df79 HEAD@{14}: rebase -i (pick): (1) Premier import : Fonction main()
    da19992 HEAD@{15}: rebase -i (start): checkout da19992dddc4cfbb2e2a98af06cacfeb8c76fe68
    a6d1128 HEAD@{16}: commit: (8) modification de display(): Correction d'un bug : 1 point final en trop
    32bb071 HEAD@{17}: commit: (7) modification de main() : Affichage d'une deuxieme ligne de test
    db6dbab HEAD@{18}: commit: (6) modification de display(): Utilisation d'une constante
    ...
    

On retrouve la suite complète des opérations ayant déplacées le 'HEAD', avec en début de ligne le numéro du commit sur lequel se trouve le 'HEAD' suite à l'opération. La notation REFERENCE@{XX} peut être utilisée comme _alias_ dans les commandes prenant en paramètre un identifiant de commit.

Il est assez rare, dans un usage courant de Git, d'avoir besoin d'utiliser un reflog. Il y a cependant un cas où on les manipule forcément, _sans peut-être le savoir_ : le [remisage](../remisage). La référence d'une modification remisée est en effet de la forme 'stash@{xx}', c'est un reflog.

**Note :** `git reflog -p HEAD` affiche en plus les modifications associées à chaque commit listé. Ce peut être utile si le résumé de commentaire affiché par `git reflog` n'est pas suffisant pour se souvenir du contenu du commit (d'où l'importance de bien choisir la 1ère ligne des commentaires de commit !).

**Pour les plus curieux :** Vous vous demandez peut-être ce qu'il se passe si vous revenez sur un des commits _intermédiaires_ d'un rebasage, 'HEAD@{11}' par exemple. Se retrouve-t-on _au milieu_ de l'opération de rebasage avec possibilité de la reprendre en cours de route ? Hélas non, Git a quand même ses limites...

Re-ordonnancement provoquant un conflit
---------------------------------------

Le dernier commit avant les deux rebasages que l'on vient d'effectuer est donc accessible par le reflog 'HEAD@{16}'.

Revenez sur l'historique initial, avant rebasage, à l'aide d'un `git reset --hard HEAD@{16}`. Vérifiez que l'historique que vous obtenez est bien celui que vous désirez.

Appliquez maintenant un `git rebase -i --root` avec la _recette_ suivante (on inverse l'ordre d'application des commits 6 et 8):

        pick 61dea6a (1) Premier import : Fonction main()
        pick 8fbcce0 (2) Ajout de la fonction display()
    ->  fixup a6d1128 (8) modification de display(): Correction d'un bug : 1 point final en trop
    ->  fixup db6dbab (6) modification de display(): Utilisation d'une constante
        pick a87c1cd (3) Utilisation de la fonction display() dans main()
    ->  drop 6f3df50 (4) modification de display(): Affichage sur 2 lignes
    ->  drop ea76fd9 (5) Revert "modification de display(): Affichage sur 2 lignes"
        pick 32bb071 (7) modification de main() : Affichage d'une deuxieme ligne de test
    

Résultat :

    error: could not apply a6d1128... (8) modification de display(): Correction d'un bug : 1 point final en trop
    
    When you have resolved this problem, run "git rebase --continue".
    If you prefer to skip this patch, run "git rebase --skip" instead.
    To check out the original branch and stop rebasing, run "git rebase --abort".
    
    Could not apply a6d1128025ee01f4ff437a1cc71ed079775abcf7... (8) modification de display(): Correction d'un bug : 1 point final en trop
    

C'est bien ce que l'on craignait : le commit 8 ne peut être appliqué sur le commit 2 sans conflit.  
Le conflit marqué dans **main.c** est :

    void display(char *msg)
    {
    <<<<<<< HEAD
            printf("TP Git. %s.\n", msg);
    =======
            printf("%s. %s\n", TITLE, msg);
    >>>>>>> a6d1128... (8) modification de display(): Correction d'un bug : 1 point final en trop
    }
    

Conservez la version du commit 8 (2ème partie du marquage), indexez **main.c** (`git add main.c`) et continuez le rebasage (`git rebase --continue`).

L'application du commit 6 après le commit 8 génère également un conflit. Résolvez-le en gardant cette fois-ci la version courante (1ère partie du marquage, celle qui vient du commit 8 donc), indexez et terminez le rebasage.

À retenir

L'indexage interactif (`git add -p`) et le rebasage interactif (`git rebase -i`) permettent une très grande souplesse dans le processus d'évolution d'un code (ou d'un texte...).

Pour paraphraser le manuel de 'git rebase', ils permettent un processus de type (avec souvent une boucle sur les étapes 2 et 3) :

1.  Je commence à travailler sur une nouvelle idée
2.  Je modifie le code dans tous les sens, par répétition de 2 types d'opérations :
    *   J'ai effectué une modification qui vaut le coût d'être stockée -> je la commite (éventuellement en plusieurs morceaux)
    *   Je réalise que quelque chose ne va pas -> je le corrige -> je commite la correction
3.  Je retravaille les commits pour obtenir un historique _propre_
4.  Je soumets le résultat

Ce type de développement est assez classique lorsqu'il n'est pas mené par des spécifications très précises. Avec un VCS plus standard (tel que SVN), il aboutît en général à un _gros commit fourre-tout_...  
Puisque Git fournit tous les outils pour éviter ce phénomène, n'hésitez pas à fonctionner de cette manière là !

**Remarque :** Le rebasage interactif modifie l'historique, il faut donc attendre d'avoir terminer votre travail avant de le _pousser_ sur un dépôt partagé. Cette méthode est, en général, utilisée pour une évolution demandant un _certain temps_ avant d'être finalisée. Le travail ne s'effectuera donc pas sur la branche de développement partagée, mais sur une branche spécifique; ceci afin de conserver la possibilité de récupérer les dernières évolutions de la branche de développement (nous évoquerons bientôt la notion de _workflows_).

**Conseil :** Usez de la méthode de développement qui vient d'être présentée, mais n'en abusez pas dans un projet collaboratif ! Il faut éviter de _garder pour soi_ une évolution pendant trop longtemps (rappellez-vous que vous ne pouvez pas partager votre travail tant que l'historique n'a pas été récrit). Un dicton qui a cours chez les utilisateurs Git est : "_Commit early, commit often_", avec en sous-entendu : "**_Commit and push early, commit and push often_**".


* * *
