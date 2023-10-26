Récupération d'un dépot sain

Si, à ce stade, votre dépot se trouve dans un état instable, vous avez la possibilité de continuer avec un dépot sain sans être obligé de refaire le TP depuis le début.

Si vous ne l'avez pas déjà fait, récupérez les ressources utiles au TP en suivant les instructions décrites dans la page [Ressources pour le TP](../annexes/tpfiles/) des annexes.

Il vous suffit ensuite de supprimer votre dépot puis de copier le répertoire `~/tpfiles/brassens` en exécutant les commandes suivantes :

`$ cd`  
`$ rm -Rf tpgit`  
`$ cp -Rf ~/tpfiles/brassens tpgit`

Petit exercice en autonomie : avec les commandes que vous avez apprises (`git checkout`, `git reset`, ...) vous allez vous amuser à faire votre propre version des 'oiseaux de passages'.

Retournez donc dans le dépot correspondant à la chanson. Voici quelques modifications que vous pouvez faire :

*   supprimez la répétition du dernier paragraphe sans ajouter de nouveau commit,
*   retrouver le paragraphe avec le mot 'sommeil'
*   ce paragraphe a été supprimé, rétablissez-le avec la commande que vous venez d'apprendre: `git revert`.

N'hésitez pas à modifier d'avantage le texte pour parfaire votre expérience de Git !

[Revenez dans le cours du TP](../partager/)
-------------------------------------------