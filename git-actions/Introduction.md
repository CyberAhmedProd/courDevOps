

Les concepts de l'intégration continue et du déploiement continu se sont unifiés par l'usage, en tant que culture, ensemble de principes et recueil de pratiques. Pourtant, il n'y a pas encore de norme pour standardiser la terminologie, les modes opératoires et les conventions.

De nombreux éditeurs et fournisseurs d'infrastructures proposent leur implémentation d'un CI/CD. Toutes partagent l'essentiel de la vision, mais diffèrent par le point de vue central adopté (celui du développeur, du Release Manager ou du responsable de l'infrastructure). Parfois encore, elles se spécialisent dans un langage.

# 1 Le CI/CD de GitHub
----------------------

L'« octo-chat » a livré au public en novembre 2019 sa solution d'intégration continue/de déploiement continu, dans laquelle on retrouve des inspirations de **GitLab** et **CircleCi**, entre autres. En plus des abonnements payants, elle est proposée en formule gratuite dans une certaine limite de minutes mensuelles (consulter github.com/features/actions), qui est cependant assez généreuse, et peut suffire à de petites entreprises.

### 1.1 Présentation

Sans surprise, ce CI/CD érige le développeur en acteur principal des séquences de travaux automatiques, puisque c'est au sein de chaque repository que se place la déclaration des workflows associés au projet. C'est aussi par un écosystème d'actions programmées par la communauté que le développeur/ DevOps pourra enrichir ses propres workflows.

Il s'agit donc de configurer des procédures à faire exécuter par des agents hébergés chez **GitHub**, en réaction à des événements qui surviennent sur le repository : par exemple, un push sur une branche, l'ouverture d'une pull request, ou encore la fermeture d'une issue. Classiquement, les procédures (jobs) peuvent dépendre l'une de l'autre, et contenir des étapes (steps) conditionnelles, stocker des fichiers et archives, et autres travaux habituels d'un CI/CD.

### 1.2 GitHub Actions

Pour mettre en place une GitHub Action dans un projet, il suffit de créer un dossier .github/workflows à sa racine, et d'y déposer un fichier d'extension .yml (ou .yaml).

Il est possible de déclarer plusieurs fichiers de workflow : chacun réagit à un ou plusieurs événements, et l'on peut donc définir des procédures différentes selon les événements qu'on souhaite traiter. Une fois cet ajout poussé vers l'origin, les workflows apparaissent dans l'onglet **Actions** du repository, et les prochains événements pourront être pris en charge par vos jobs.

Avant d'étudier les possibilités de configuration, il est important de noter que le manifeste de déroulement du CI/CD se trouve au sein du repo lui-même : aussi, à moins de mettre en place des règles avancées de protection de fichiers et de branches (voir encadré), le contrôle en est laissé aux développeurs du projet.


# 2 Anatomie de la configuration
--------------------------------

Les procédures, qu'on appelle workflows, se configurent dans des fichiers individuels **\[2\]** au format YAML, dans le dossier .github/workflows relatif à la racine du projet. GitHub représente chaque fichier par un diagramme dans l'onglet **Actions** du repository. Détaillons à présent les éléments constitutifs des travaux de CI.

### 2.1 Workflows

Un workflow, autrement dit un fichier de configuration, réagit à un ou plusieurs événements : il débute donc par une clause on, où l'on précise le ou les événements. Certains sont particulièrement pertinents dans le cadre du CI/CD (push, pull request, fermeture de ticket, etc.) tandis que d'autres se rapportent peut-être moins à un contexte d'entreprise et plus au cycle communautaire des projets open source (fork du repository, nouveau watch, nouvelle page wiki, etc.).

La procédure décrite dans un workflow contient un ou plusieurs jobs, dont on peut demander l'exécution en parallèle ou en séquence. Chaque job contient un ou plusieurs steps.

Un même workflow peut être accroché à plusieurs événements. Mais bien sûr, si vous prévoyez deux traitements différents pour deux événements distincts, il faudra deux fichiers. En outre, plusieurs workflows peuvent être accrochés au même événement, ce qui peut être utile par exemple s'il est besoin d'effectuer des procédures fondamentalement différentes et indépendantes pour une même action.

Il est possible de restreindre l'activation d'un Workflow à une ou plusieurs branches (selon les types d'événements, bien sûr) ou au contraire, d'en exclure certaines. Il est également possible de conditionner le déclenchement à la présence d'au moins un fichier modifié sous un répertoire donné.

Par exemple, si le fichier commence par la séquence suivante :

```bash
on:

  push:

    branches: \[ master \]

    path-ignore:

    - 'docs/\*\*'
```
Alors le workflow sera déclenché dès qu'un push aura lieu sur la branche master, sauf si les modifications ne portent que sur des fichiers se trouvant sous le répertoire docs.

### 2.2 Jobs, Steps

Après avoir indiqué quel(s) événement(s) sont à traiter, et sous quelles conditions de branches et de chemins, le workflow liste dans une section jobs l'ensemble des travaux à effectuer.

GitHub Actions va exécuter chaque job indépendamment, mais on peut en contrôler la séquence et l'arbre de dépendances : quel job passe après quel autre, lequel a droit d'échouer sans empêcher la suite, etc.

Il faut aussi indiquer par une clause runs-on le système sur lequel un job doit tourner, parmi un choix de machines **GNU/L****inux**, **m****acOS** et même **Windows**.

Pour un confort optimal de CI/CD, les runners proposés par GitHub Actions sont très généreusement fournis en logiciels et langages de toutes sortes, Shell, SDK **Android**, bases de données, **Ansible**, images **D****ocker** en cache, etc. **\[****3****\]** !

Chaque job peut tourner sur une plateforme différente, mais tous les steps d'un même job s'exécutent les uns à la suite des autres sur la même instance. Ils partagent donc le système de fichiers : les fichiers qu'un step dépose ou modifie sont disponibles pour les steps suivants du même job.

Voici un exemple de section jobs : la déclaration de ces simples steps parle d'elle-même.
```bash
jobs:

  compiler\mon\module:

    runs-on: ubuntu-20.04

    steps:

    - run: |

        npm install

        npm test

    - run: |

        echo "Faire quelque chose"

        ./my-script.sh

      working-directory: ./scripts

  publier\mon\module:

    runs-on: ubuntu-20.04

    needs: compiler\mon\module

    steps:

    - name: 'Mon Step de Publication'

      run: |

        echo "Will now publish"

        npm publish
```

Pour les moins familiers de la syntaxe YAML, on peut préciser ici que la clé steps introduit une liste dont les éléments arrivent par un tiret. Par exemple, ici les deux steps du job compiler\mon\module portent une propriété run, mais seul le deuxième a la propriété working-directory.

Les steps peuvent avoir aussi une propriété name optionnelle, pour de simples raisons de lisibilité du fichier de workflow comme du log d'exécution.

De plus, le deuxième job publier\mon\module stipule une clause needs : il dépend du premier, et attendra donc la fin de son exécution avec succès pour démarrer. Si cette clause n'était pas présente, GitHub Actions pourrait décider de démarrer les deux jobs en parallèle.

### 2.3 Secrets et environnement

Certains travaux de CI/CD nécessitent évidemment des secrets (clés d'API, mots de passe, etc.). On les enregistre sous la forme de couples clé-valeur dans les **Settings** du projet (les valeurs ne sont plus consultables par la suite). Les steps accèdent aux secrets en utilisant : ${{ secrets.NomDeSecret }}.

Par exemple, dans un step, l'appel d'une commande avec un argument provenant des secrets sous le nom MaCleApi se ferait comme ceci :
```bash
    steps:

    - run: ma-commande.sh ${{ secrets.MaCleApi }}
```

Les variables d'environnement, quant à elles, peuvent être fournies soit à un step en particulier, soit à l'ensemble d'un job. Ces deux éléments prennent en charge la propriété env, qui est naturellement une map YAML de couples clé-valeur.

Une variable d'environnement peut bien sûr contenir la valeur d'un secret.
```bash
jobs:

  my\job\with\db:

    runs-on: ubuntu-20.04

    env:

      DB\NAME: glmf

      DB\PASS: ${{ secrets.MotDePasseDb }}

    steps:

    - run: ./upgrade-db.sh
```

Cependant, certains secrets ne doivent pas être confiés à n'importe quel workflow sur n'importe quelle branche. Nous avons évoqué comment protéger les abus sur les fichiers de workflow par des contributeurs non autorisés, mais même en rejetant une revue de code pour empêcher le merge des changements, les workflows sont néanmoins exécutés.

Pour éviter la fuite de secrets, GitHub propose depuis février 2021 **\[4\]** l'utilisation de plusieurs Environments : il s'agit de désigner de l'extérieur (du repo, c'est-à-dire dans les **Settings** et non dans les fichiers) quels workflows sur quelles branches ont le droit de consulter quels secrets. Au sein d'un workflow non autorisé, un job qui tente d'accéder aux secrets protégés échouera automatiquement, et ne pourra donc pas effectuer une action risquant d'exfiltrer des valeurs confidentielles.

### 2.4 Checkout

Lorsqu'une action se déclenche, seul le contenu du fichier YAML de workflow est initialement accessible au service GitHub Actions, à l'exclusion de tout autre élément du repository. Aussi, pour pouvoir effectuer des travaux utiles, une des premières choses à faire est souvent un checkout du code.

GitHub Actions propose un mécanisme standard utilisant une action communautaire (voir section 4) : c'est un bloc déjà écrit par quelqu'un d'autre, que l'on peut incorporer à un de nos jobs, comme step à part entière. Au lieu de la propriété run, le step indiquera plutôt une clause uses, et GitHub Actions saura aller chercher le bloc correspondant pour l'exécuter.

Ici, le mainteneur de l'action n'est autre que l'équipe GitHub elle-même, et l'action s'appelle actions/checkout@v2.

Voici typiquement comment débute le premier job d'un workflow devant travailler avec le code du projet :
```bash
jobs:

  compiler\mon\module:

    runs-on: ubuntu-20.04

    steps:

    - name: 'Checkout du code'

      uses: actions/checkout@v2

    - name: 'Dépendances et tests'

      run: |

        npm install

        npm run test
```
On aurait pu se passer de ce bloc réutilisable actions/checkout@v2 et effectuer la commande git clone explicitement, à condition d'avoir paramétré ce qu'il faut de secrets et de clés SSH ; mais c'est évidemment plus lisible ainsi, et de plus le code (en NodeJS) de l'action elle-même est ouvert **\[5\]**.

### 2.5 Conditions

Chaque job ou step peut être assujetti à une condition d'exécution. Pour ce faire, on instruira la clause if sur le job ou le step qui ne doit travailler que dans certains cas.
```bash
steps:

  - name: Màj DB Peut-être

    id: my-upgrade-db

    run: composer run migrate-db

    if: ${{ env.ONE + env.TWO }}
```

L'expression qui constitue la condition peut utiliser des informations de différentes provenances, dont entre autres :

*   les variables d'environnement : env.<MY\VAR> ;
*   l'événement déclencheur (activité, branche, user…) : github.base\ref ;
*   les valeurs de sortie de steps précédents : steps.<id-de-step-amont\>.outputs.<ma\valeur\>.

Chacune de ces provenances s'appelle un contexte **\[****6****\]** et s'utilise par un préfixe spécifique et une notation à point. Les expressions peuvent aussi utiliser des opérateurs &&, ||, \==, \>=, etc. ainsi que de quoi manipuler tableaux, chaînes de caractères et morceaux **JSON**.

Voyons maintenant comment mettre des données à disposition des steps et jobs qui suivent dans l'exécution du workflow.

### 2.6 Partage de données

Au sein d'un job, les steps s'exécutent en séquence. Chaque step peut consommer les résultats de steps antérieurs du même job, du moment qu'ils ont positionné leurs valeurs de sortie selon une syntaxe particulière :
```bash
run: |

  echo "::set-output name=ma\valeur::glmf"
```

Toute commande ou tout exécutable d'un step, qui effectue cette sortie standard, a pour effet de rendre disponible aux steps suivants la donnée glmf pour l'expression ${{ steps.<id\du\step\>.outputs.<ma\valeur\> }}.

Sans entrer dans le détail, précisons que sur un modèle similaire, les jobs peuvent passer des valeurs à leurs successeurs, en déclarant quelles sorties de quels steps un job souhaite exporter. Naturellement, contrairement aux steps qui ne sont jamais parallélisés, les jobs peuvent s'exécuter simultanément. Par conséquent, un job qui a besoin de la sortie d'un autre devra en indiquer son id dans une clause needs et sera forcément joué après lui.

### 2.7 Artifacts

Lorsque l'échange de simples valeurs ne suffit pas entre deux jobs, il est possible de stocker des artifacts : ce sont des fichiers ou dossiers qu'on fait persister d'un job à l'autre. Le filesystem est remis à neuf pour chaque job, mais les artifacts sont stockés dans un emplacement différent, fourni par GitHub Actions, et on les téléverse/télécharge grâce à deux actions préconstruites **\[7\]**.

Un job stocke un artifact comme ceci :
```bash
jobs:

  job-fabrication:

    steps:

      - name: Construction du binaire

        run: make.sh

      - name: Conservation du binaire

        uses: actions/upload-artifact@v2

        with:

          name: glmf.so

          path: outputs/lib-mon-truc.so
```
Et un job ultérieur peut télécharger l'artifact par :
```bash
  job-diffusion:

    steps:

      - name: Récupération du binaire

        uses: actions/download-artifact@v2

        with:

          name: glmf.so

      - name: Envoyer aux FTP amis

        run: ./envoyer.sh glmf.so
```
Les artifacts restent conservés chez GitHub Actions pour une certaine durée (par défaut 90 jours) et sont accessibles via l'interface web.

# 3 Minuteurs et manivelle
--------------------------

Une grande variété d'événements déclencheurs de workflows permet de couvrir tous les besoins du cycle de vie des projets : nouvelle pull-request, approuvée, rejetée, nouveau ticket, ticket fermé, nouveau tag, etc. **\[8\]**.

Mais on peut souligner l'existence de deux actions en particulier qui s'avèrent pratiques dans certains cas : le déclenchement manuel, par lequel on peut passer des paramètres personnalisés à chaque lancement, et le déclenchement planifié.

Le démarrage manuel s'obtient en utilisant l'événement workflow\dispatch. Une section inputs permet de préciser les arguments attendus :
```bash
on:

  workflow\dispatch:

    inputs:

      numero\de\rc:

        description: 'Entrez le numéro de Release Candidate à utiliser'

        required: true

      nom\de\code:

        description: 'Quel petit nom donner à cette version ?'

        required: false

        default: 'SNAPSHOT'
```
Ceci aura pour effet de présenter à l'utilisateur, au moment où il déclenchera le workflow, une popup de saisie pour les deux paramètres précisés dans les inputs.

Quant au démarrage planifié, il s'agit d'un classique cron pour l'événement schedule (plusieurs programmations possibles) :
```bash
on:

  schedule:

   - cron: '0 10 \* \* MON'

   - cron: '30 12 \* \* SUN'
```

# 4 Écosystème d'actions
------------------------

La grammaire des workflows est assez concise ; tout repose sur le caractère extensible du système. Comme nous l'avons vu avec le checkout, certaines des opérations les plus essentielles d'un workflow s'effectuent au moyen de modules que tout un chacun est libre de fournir ou d'enrichir. À la mi-2021, un vaste catalogue **\[9\]** de près de 8 000 actions permet d'ajouter facilement aux jobs des tâches les plus indispensables, comme la configuration de la version du **Java SDK**, aux plus exotiques telles que l'envoi d'un **Tweet**, en passant par les intégrations DevOps des principaux Clouds ou l'analyse statique de projets **PHP**.

GitHub Actions fournit un toolkit pour rédiger des actions réutilisables **\[10\]**. Elles se développent soit en **NodeJS**, soit sous la forme d'images Docker, et se diffusent naturellement sur GitHub comme tout autre projet.

Par exemple, une action personnalisée en Node se présente comme un repository mon-nom/mon-action, comportant à la racine un manifeste nommé action.yml. Il précise les arguments attendus et les valeurs de sortie de ce qui sera donc un step dans le workflow de quelqu'un, et il indique le fichier .js d'entrée. Le reste est un projet Node comme un autre.

Le tout s'utilise dans un job comme ceci :
```bash
your-job:

    steps:

      - name: "Avec l'action de mon ami"

        uses: mon-nom/mon-action@tag1

        with:

          un-arg: "glmf"
```

Conclusion
----------

Le marché des solutions de CI/CD est relativement fourni, en auto hébergé ou non, aussi bien en offre gratuite que payante. **TravisCI**, CircleCI, GitLab CI, **Jenkins**, **TeamCity** et bien sûr GitHub Actions ne sont qu'une petite liste parmi toutes les options, auxquelles s'ajoutent les services d'AWS et de **Google**.

GitHub Actions est un produit encore jeune, mais bien pensé, qui rend des services très appréciables pour les projets open source, sans être de reste dans un contexte privé. Il apporte une intégration toute naturelle avec le reste de l'écosystème GitHub et bien sûr, il est tout à fait possible de l'utiliser en complément d'autres CI/CD, de façon à faire prendre en charge des parties différentes des opérations par chaque fournisseur, pour réduire les délais et les coûts.

Références
----------

**\[1\]** Fichier de Code Owners : [https://github.blog/2017-07-06-introducing-code-owners/](https://github.blog/2017-07-06-introducing-code-owners/)

**\[2\]** Syntaxe des workflows :  
[https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)

**\[****3****\]** Logiciels préinstallés sur les runners : [https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners#supported-software](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners#supported-software)

**\[****4****\]** Environnements protégés et branches de déploiement : [https://github.blog/changelog/2021-02-17-github-actions-limit-which-branches-can-deploy-to-an-environment/](https://github.blog/changelog/2021-02-17-github-actions-limit-which-branches-can-deploy-to-an-environment/)

**\[****5****\]** Action de checkout : [https://github.com/actions/checkout](https://github.com/actions/checkout)

**\[****6****\]** Syntaxe des expressions :  
[https://docs.github.com/en/actions/reference/context-and-expression-syntax-for-github-actions](https://docs.github.com/en/actions/reference/context-and-expression-syntax-for-github-actions)

**\[****7****\]** Artifacts : [https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts)

**\[****8****\]** Événements déclencheurs : [https://docs.github.com/en/actions/reference/events-that-trigger-workflows](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

**\[****9****\]** Écosystème d'actions : [https://github.com/marketplace?type=actions](https://github.com/marketplace?type=actions)

**\[****10****\]** Créer ses propres actions : [https://docs.github.com/en/actions/creating-actions](https://docs.github.com/en/actions/creating-actions)

**\[****11****\]** GitHub en ligne de commande : [https://github.com/cli/cli](https://github.com/cli/cli)

**\[****12****\]** Exécution de GitHub Actions en local : [https://github.com/nektos/act](https://github.com/nektos/act)

**\[****13****\]** Process Substitution : [https://tldp.org/LDP/abs/html/process-sub.html](https://tldp.org/LDP/abs/html/process-sub.html)

**\[****14****\]** API REST de GitHub pour déclencher une action manuelle : [https://docs.github.com/en/rest/reference/actions#create-a-workflow-dispatch-event](https://docs.github.com/en/rest/reference/actions#create-a-workflow-dispatch-event)