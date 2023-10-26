ExplainGit
==========

[ExplainGit](http://www.wei-wang.com/ExplainGitWithD3/) est un simulateur permettant d'expérimenter visuellement le résultat de certaines commandes Git.

Dans la version utilisée dans ce document, il affiche sous forme de graphe l'historique du dépôt local dans la zone jaune et l'historique d'un (éventuel) dépôt distant dans la zone bleue. La zone verte en bas du simulateur permet de saisir les commandes à tester.

**explainGit** ne simule ni le répertoire de travail ni l'espace d'index, mais uniquement le dépôt. Seules les commandes qui agissent directement sur le dépôt sont donc simulables. En particulier, on fait directement un `git commit` sans devoir faire un `git add` au préalable. Les options des commandes ne sont également pas simulées.

Si **explainGit** ne comprend pas une commande, il ne fait rien. Dans sa version _officielle_ ([ExplainGit](http://www.wei-wang.com/ExplainGitWithD3/)), **explainGit** affiche un message d'erreur. Avec le mode d'affichage utilisé dans ce document, les messages d'erreurs ne sont pas visibles.

var explaingit\_content = \[ { name: 'Exemple', height: 500, commitData: \[ {id: 'e137e9b', tags: \['origin/master', 'master'\]} \], originData: \[ {id: 'e137e9b'}, {id: '7eb7654', parent: 'e137e9b'}, {id: '090e2b8', parent: '7eb7654'}, {id: 'ee5df4b', parent: '090e2b8', tags: \['master'\]} \] } \];