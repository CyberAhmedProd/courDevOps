

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
# 5 Ligne de commande
---------------------

On ne pourrait pas vraiment parler d'automatisation si la souris était le seul moyen d'aller vérifier la progression d'une tâche de CI/CD.

Voyons maintenant de quels outils en ligne de commande nous disposons.

### 5.1 GitHub CLI

Le programme gh **\[1****1****\]** est une sorte d'édition allégée de ce qu'on peut normalement faire avec la GUI du site GitHub.com.

La page du projet donne les instructions d'installation pour toutes les plateformes. Il s'agit d'un outil pour consulter et gérer pull request, issues et releases. Mais plus précisément pour ce qui nous intéresse ici, il va aussi nous permettre de configurer les secrets et de superviser l'exécution des workflows. On commence par s'authentifier avec un compte GitHub :
```bash
$ gh auth statusYou are not logged into any GitHub hosts. Run gh auth login to authenticate.$ gh auth login? What account do you want to log into? \[Use arrows to move, type to filter\]

\> GitHub.com

  GitHub Enterprise Server
```

Suite à quoi, après d'autres questions concernant le nom du compte et la clé SSH à utiliser, on nous invite à pointer un navigateur sur une URL éphémère pour finaliser l'appairage. Puis on peut vérifier que l'authentification est prête :
```bash
$ gh auth status

github.com

  ✓ Logged in to github.com as my-github-handle (/home/gabriel/.config/gh/hosts.yml)

  ✓ Git operations for github.com configured to use ssh protocol.

  ✓ Token: \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*
```
Ensuite, on se place dans le repository et on peut gérer les secrets :
```bash
~/projects/my-repo $ gh secret list

AWS\ACCESS\KEY\ID      Updated 2021-01-12

AWS\SECRET\ACCESS\KEY Updated 2021-01-12~/projects/my-repo $ gh secret set NEW\SECR -b valeur\confidentielle✓ Set secret NEW\SECR for my-org/my-repo
```
L'autre opération importante que l'outil nous permet de faire est la vérification de l'état des checks d'une PR, c'est-à-dire les résultats de workflows qu'elle a déclenchés. Noter que ces checks ne concernent pas que les GitHub Actions, mais également n'importe quel autre service de CI/CD sur lequel le repository a été branché :
```bash
~/projects/my-repo $ gh pr listShowing 2 of 2 open pull requests in my-org/my-repo

#699 <...>#692 <...>

~/projects/my-repo $ gh pr checks 699All checks were successful0 failing, 1 successful, and 0 pending checks✓ ci/circleci: build    https://circleci.com/gh/...
```
### 5.2 Exécution locale de workflows

La mise au point de workflows peut s'avérer laborieuse, surtout en raison de la nécessité de provoquer des merge et autres opérations historisées sur le repository.

Pour faciliter ce travail, le projet nektos/act **\[1****2****\]**, écrit en **Go** et utilisant Docker, permet d'exécuter les workflows en local, et de bénéficier d'une assez bonne émulation des runners (Linux) de GitHub Actions.

Une fois l'outil act installé et configuré (se référer à la page du projet), on peut lister les workflows qui sont rattachés à un event donné, par exemple ici ceux qui déclarent démarrage manuel :

~/projects/my-repo $ act workflow\dispatch --listID               Stage NamepromoteBackend   0      Release Backend to ProductionpromoteFrontend 0      Release Frontend app to Production

Nous allons ici démarrer le travail en mode dry run, en précisant quel fichier de workflow lancer. Puisqu'il s'agit de workflow\dispatch, sa payload doit contenir une map d'inputs. Le programme act attend sous la forme d'un fichier JSON la payload de l'événement à simuler ; mais, pour plus de souplesse, nous allons utiliser le Process Substition **\[1****3****\]** du **Bash** (ou **Zsh**) pour fournir au programme un descripteur de fichier non matérialisé, comme résultat d'une sortie de caractères :

~/projects/my-repo $ act workflow\dispatch --dryrun --workflows .github/workflows/promote-backend.yml \--eventpath <( echo '{"inputs":{"tagNo":"v3.21"}}' )\*DRYRUN\* \[Promote Backend/promoteBackend\] 🚀 Start image=node:12.20.1-buster-slim

\*DRYRUN\* \[Promote Backend/promoteBackend\]   🐳 docker run image=node:12.20.1-buster-slim platform=linux/amd64 entrypoint=\["/usr/bin/tail" "-f" "/dev/null"\] cmd=\[\]

\*DRYRUN\* \[Promote Backend/promoteBackend\] ⭐ Run actions/checkout@v2

\*DRYRUN\* \[Promote Backend/promoteBackend\]   ✅ Success - actions/checkout@v2

\*DRYRUN\* \[Promote Backend/promoteBackend\] ⭐ Run Promote

\*DRYRUN\* \[Promote Backend/promoteBackend\]   ✅ Success - Promote

Cette méthode permet un cycle raccourci pour détecter les problèmes de variables manquantes et autres commandes mal paramétrées dans les workflows, en s'évitant les allers-retours vers GitHub.

Mais attention, à part en dry run, les commandes sont réellement exécutées ! En particulier, les interactions avec les plateformes d'hébergement et de Cloud, etc. Simplement, elles sont exécutées en local dans votre Docker plutôt que sur les ordinateurs de GitHub.

### 5.3 API pour le lancement manuel

On peut lancer un workflow manuel chez GitHub Actions par l'API REST habituelle de GitHub **\[1****4****\]**. On doit tout d'abord créer un Personal Access Token dans son profil sous **Settings > Developer Settings**, puis le stocker en lieu sûr, après quoi le workflow manuel se déclenche comme ceci :

$ curl -u <mon-user>:<mon-token> \\   -X POST \\   -H "Accept: application/vnd.github.v3+json" \\   -d '{"ref":"master","inputs":{...}}' \\   https://api.github.com/repos/<owner>/<repo>/actions/workflows/<workflow>.yml/dispatches

On retrouve ici dans le corps de la requête JSON l'élément ref pour indiquer la branche master, ainsi que l'entrée inputs comme vu plus haut, puisqu'il s'agit du workflow\dispatch.

# 6 Exemples
------------

Il ne nous reste plus qu'à mentionner quelques cas d'utilisation classiques.

### 6.1 AWS

Le géant du Cloud fournit avec la famille github.com/aws-actions tout le nécessaire pour manipuler ses infrastructures très simplement depuis un job GitHub Action.

Muni des secrets pour les clés **AWS**, on commence par un step qui configure les identifiants pour l'ensemble du job :

your-job-with-aws:

    steps:

     - name: "Configuration des credentials pour AWS"

        uses: aws\-actions/configure-aws-credentials@v1

        with:

          aws-access-key-id: ${{ secrets.AWS\ACCESS\KEY\ID }}

          aws-secret-access-key: ${{ secrets.AWS\SECRET\ACCESS\KEY }}

          aws-region: us-east-1

Suite à quoi le CLI aws, déjà préinstallé sur tous les runners GitHub Actions, se trouve authentifié et s'emploie sans difficulté.

D'autres actions utiles sont disponibles, par exemple, on peut identifier le Docker Engine du runner sur le Registry ECR d'**Amazon** :

     - name: "Executer Docker Login avec les creds ECR"

        uses: aws\-actions/amazon-ecr-login@v1

Ceci pour pouvoir y effectuer les commandes habituelles docker push etc. dans les steps ultérieurs.

### 6.2 PHP

Pour jongler sans difficulté entre les versions de PHP, on peut utiliser l'action communautaire nanasess/setup-php comme ceci :

your-job-with-php:

    steps:

     - name: "Choisir la version de PHP"

        uses: nanasess/setup-php@master

        with:

          php-version: '7.4'

Ce qui plante le décor pour tout ce qui concerne le PHP dans la suite :

     - name: "Installer et tester"

        run: |          composer install --no-progress          vendor/bin/phpunit

Pour rappel, composer est déjà présent sur les runners GitHub Actions, ainsi qu'un grand nombre d'outils spécifiques aux autres langages principaux.

### 6.3 Notification Slack

On peut enfin mentionner une action bien pratique, qui laisse deviner toutes les autres possibilités d'intégration du même genre. Le projet rtCamp/action-slack-notify n'est qu'une implémentation parmi d'autres qui ne répond sans doute pas à tous les besoins, mais sa simplicité de mise en œuvre illustre le propos.

      - name: Envoyer une notif Slack

        uses: rtCamp/action-slack-notify@v2

        env:

          SLACK\WEBHOOK: ${{ secrets.SLACK\WEBHOOK }}

          SLACK\USERNAME: github

          SLACK\TITLE: Votre nouvelle version est en ligne

          SLACK\FOOTER: ""

          SLACK\CHANNEL: build

La page du projet détaille les options disponibles et la façon d'obtenir auprès de **Slack** un token de webhook.

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