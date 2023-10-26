Il arrive parfois que l'on se rende compte après coup d'un problème. Il faut dans ce cas retrouver la modification (le commit) qui l'a provoqué, et donc pouvoir analyser les versions prédédentes de nos fichiers.  
`git checkout` est dans ce cas également bien utile.

Identifiez le problème
----------------------

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Recopiez d'abord le répertoire `~/tpfiles/checkout` dans votre répertoire personnel :

    $ cp -Rf ~/tpfiles/checkout ~
    

Allez dans ce répertoire et exécutez hello.py :

    $ cd ~/checkout
    $ python hello.py
    

Lancer les tests :

    $ python test.py
    

La sortie attendue est :

    -> Test ecrire: devrait ecrire une fois 'test'
    test
    -> Test ecrireXFois: devrait ecrire 3 fois 'test'
    test
    test
    test
    

Retrouver le commit défectueux
------------------------------

Pour savoir à partir de quel commit les tests ne passent plus, vous allez utiliser la commande `git checkout` pour revenir en arrière un commit à la fois (voir [Parlons de HEAD](../head/)).

_Aidez-vous des messages de commit pour vous mettre la voie._

Une fois que vous avez retrouvé le premier commit qui pose problème, utilisez `git show` pour mettre en évidence la modification défectueuse.

Vous pouvez résoudre le problème avec un commit qui correction le problème (après être revenu à la fin de votre historique à l'aide d'un `git checkout master`). Nous allons voir par la suite d'autres moyens plus élaborés de correction.

[Revenez dans le cours du TP](../correction/)
---------------------------------------------