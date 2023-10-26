Il y aurait beaucoup de choses à dire sur Git.

Nous nous contenterons ici de lister quelques commandes qui peuvent vous être utiles :

*   git bisect
    
    Permet de rechercher par dichotomie le commit qui apporte un problème. Cette commande aurait été bien utile pour faire l'exercice [Chercher la BUG](../chercherbug/).
    
*   git commit --gpg-sign
    
    Permet d'apposer une signature GPG sur un commit. Utilisé sur les projets 'sensibles', tel le noyau Linux
    
*   git cherry-pick
    
    Pour appliquer directement un commit (et lui seul) d'une autre branche
    
*   git blame
    
    Liste le contenu d'un fichier en préfixant chaque ligne par les informations du dernier commit ayant modifié cette ligne (identifiant, date, auteur). Permet de trouver le coupable qui a introduit un bug !
    
*   git rerere
    
    Pour enregistrer la façon dont vous avez résolu un conflit, et réappliquer automatiquement cette résolution si le même conflit est de nouveau détécté.
    
*   git submodule
    
    Pour inclure des dépôts étrangers au sein de l'arborescence
    
*   git filter-branch
    
    Pour ré-appliquer une suite de commits en les passant par un filtre (peut permettre de corriger _en masse_ un historique)
    
*   tout un ensemble de commandes 'bas niveau' (commandes de _plomberie_) utiles pour créer vos propres commandes !
    

Nous vous conseillons les excellents [tutoriaux](https://fr.atlassian.com/git/tutorials/) écrits par la société Atlassian.

**Et lisez et relisez le livre [Pro Git](https://git-scm.com/book/en/v2). C'est une mine d'informations abordant tous les domaines couverts par Git. À chaque lecture vous découvrirez quelquechose de nouveau !**