

Les concepts de l'intégration continue et du déploiement continu se sont unifiés par l'usage, en tant que culture, ensemble de principes et recueil de pratiques. Pourtant, il n'y a pas encore de norme pour standardiser la terminologie, les modes opératoires et les conventions.

De nombreux éditeurs et fournisseurs d'infrastructures proposent leur implémentation d'un CI/CD. Toutes partagent l'essentiel de la vision, mais diffèrent par le point de vue central adopté (celui du développeur, du _R__elease_ _M__anager_ ou du responsable de l'infrastructure). Parfois encore, elles se spécialisent dans un langage.

# 1 Le CI/CD de GitHub
----------------------

L'« octo-chat » a livré au public en novembre 2019 sa solution d'intégration continue/de déploiement continu, dans laquelle on retrouve des inspirations de **GitLab** et **CircleCi**, entre autres. En plus des abonnements payants, elle est proposée en formule gratuite dans une certaine limite de minutes mensuelles (consulter github.com/features/actions), qui est cependant assez généreuse, et peut suffire à de petites entreprises.

### 1.1 Présentation

Sans surprise, ce CI/CD érige le développeur en acteur principal des séquences de travaux automatiques, puisque c'est au sein de chaque _repository_ que se place la déclaration des _workflows_ associés au projet. C'est aussi par un écosystème d'actions programmées par la communauté que le développeur/ DevOps pourra enrichir ses propres _workflows_.

Il s'agit donc de configurer des procédures à faire exécuter par des agents hébergés chez **GitHub**, en réaction à des événements qui surviennent sur le _repository_ : par exemple, un _push_ sur une branche, l'ouverture d'une _pull request_, ou encore la fermeture d'une _issue_. Classiquement, les procédures (_j__obs_) peuvent dépendre l'une de l'autre, et contenir des étapes (_s__teps_) conditionnelles, stocker des fichiers et archives, et autres travaux habituels d'un CI/CD.

### 1.2 GitHub Actions

Pour mettre en place une GitHub Action dans un projet, il suffit de créer un dossier .github/workflows à sa racine, et d'y déposer un fichier d'extension .yml (ou .yaml).

Il est possible de déclarer plusieurs fichiers de _workflow_ : chacun réagit à un ou plusieurs événements, et l'on peut donc définir des procédures différentes selon les événements qu'on souhaite traiter. Une fois cet ajout poussé vers l'_origin_, les _workflows_ apparaissent dans l'onglet _**Actions**_ du _repository_, et les prochains événements pourront être pris en charge par vos _jobs_.

Avant d'étudier les possibilités de configuration, il est important de noter que le manifeste de déroulement du CI/CD se trouve au sein du _repo_ lui-même : aussi, à moins de mettre en place des règles avancées de protection de fichiers et de branches (voir encadré), le contrôle en est laissé aux développeurs du projet.

#### Code Owners

Depuis 2017, il est possible de protéger la modification de certains fichiers du _repository_ GitHub au moyen d'un fichier .github/CODEOWNERS **\[1\]**. Si un tel fichier est présent, alors GitHub s'y réfère automatiquement lors de chaque _pull request_, afin de contacter les référents qui y sont mentionnés pour leur permettre d'éventuellement rejeter la demande.

Le Code Owners est un simple fichier texte, qui liste des règles de protection sur des chemins de fichiers. Typiquement, pour empêcher que des contributeurs ne modifient quoi que ce soit au dossier .github, et donc ni les actions de CI/CD ni le fichier CODEOWNERS lui-même, on pourrait y écrire ceci :

/.github/   @monGithubHandle

Cette ligne aurait pour effet d'envoyer une demande de _code review_ à la personne (ou l'équipe) indiquée, dès lors qu'une _pull request_ (PR) contient un changement dans un ou plusieurs fichiers du répertoire .github/.

Toutefois, signaler ne suffit pas : par défaut, la validation d'un _code review_ n'est pas indispensable. Pour obliger la PR à obtenir le feu vert du mainteneur, il faut avoir coché, pour les branches protégées dans les _**Settings**_ du _repository_, l'option _**Require review from Code Owners**_.

Voir aussi les considérations de sécurité en 2.3.

# 2 Anatomie de la configuration
--------------------------------

Les procédures, qu'on appelle _workflows_, se configurent dans des fichiers individuels **\[2\]** au format YAML, dans le dossier .github/workflows relatif à la racine du projet. GitHub représente chaque fichier par un diagramme dans l'onglet _**Actions**_ du _repository_. Détaillons à présent les éléments constitutifs des travaux de CI.

### 2.1 Workflows

Un _w__orkflow_, autrement dit un fichier de configuration, réagit à un ou plusieurs événements : il débute donc par une clause on, où l'on précise le ou les événements. Certains sont particulièrement pertinents dans le cadre du CI/CD (_push_, _pull request_, fermeture de ticket, etc.) tandis que d'autres se rapportent peut-être moins à un contexte d'entreprise et plus au cycle communautaire des projets open source (_fork_ du _repository_, nouveau _watch_, nouvelle page wiki, etc.).

La procédure décrite dans un _workflow_ contient un ou plusieurs _j__obs_, dont on peut demander l'exécution en parallèle ou en séquence. Chaque _j__ob_ contient un ou plusieurs _s__teps_.

Un même _workflow_ peut être accroché à plusieurs événements. Mais bien sûr, si vous prévoyez deux traitements différents pour deux événements distincts, il faudra deux fichiers. En outre, plusieurs _workflows_ peuvent être accrochés au même événement, ce qui peut être utile par exemple s'il est besoin d'effectuer des procédures fondamentalement différentes et indépendantes pour une même action.

Il est possible de restreindre l'activation d'un _Workflow_ à une ou plusieurs branches (selon les types d'événements, bien sûr) ou au contraire, d'en exclure certaines. Il est également possible de conditionner le déclenchement à la présence d'au moins un fichier modifié sous un répertoire donné.

Par exemple, si le fichier commence par la séquence suivante :

on:

  push:

    branches: \[ master \]

    path-ignore:

    - 'docs/\*\*'

Alors le _workflow_ sera déclenché dès qu'un _p__ush_ aura lieu sur la branche master, sauf si les modifications ne portent que sur des fichiers se trouvant sous le répertoire docs.

### 2.2 Jobs, Steps

Après avoir indiqué quel(s) événement(s) sont à traiter, et sous quelles conditions de branches et de chemins, le _workflow_ liste dans une section jobs l'ensemble des travaux à effectuer.

GitHub Actions va exécuter chaque _job_ indépendamment, mais on peut en contrôler la séquence et l'arbre de dépendances : quel _job_ passe après quel autre, lequel a droit d'échouer sans empêcher la suite, etc.

Il faut aussi indiquer par une clause runs-on le système sur lequel un _job_ doit tourner, parmi un choix de machines **GNU/L****inux**, **m****acOS** et même **Windows**.

Pour un confort optimal de CI/CD, les _runners_ proposés par GitHub Actions sont très généreusement fournis en logiciels et langages de toutes sortes, Shell, SDK **Android**, bases de données, **Ansible**, images **D****ocker** en cache, etc. **\[****3****\]** !

Chaque _job_ peut tourner sur une plateforme différente, mais tous les _steps_ d'un même _job_ s'exécutent les uns à la suite des autres sur la même instance. Ils partagent donc le système de fichiers : les fichiers qu'un _step_ dépose ou modifie sont disponibles pour les _steps_ suivants du même _job_.

Voici un exemple de section jobs : la déclaration de ces simples _steps_ parle d'elle-même.

jobs:

  compiler\_mon\_module:

    runs-on: ubuntu-20.04

    steps:

    - run: |

        npm install

        npm test

    - run: |

        echo "Faire quelque chose"

        ./my-script.sh

      working-directory: ./scripts

  publier\_mon\_module:

    runs-on: ubuntu-20.04

    needs: compiler\_mon\_module

    steps:

    - name: 'Mon Step de Publication'

      run: |

        echo "Will now publish"

        npm publish

Pour les moins familiers de la syntaxe YAML, on peut préciser ici que la clé steps introduit une liste dont les éléments arrivent par un tiret. Par exemple, ici les deux _steps_ du _job_ compiler\_mon\_module portent une propriété run, mais seul le deuxième a la propriété working-directory.

Les _step__s_ peuvent avoir aussi une propriété name optionnelle, pour de simples raisons de lisibilité du fichier de _workflow_ comme du log d'exécution.

De plus, le deuxième _job_ publier\_mon\_module stipule une clause needs : il dépend du premier, et attendra donc la fin de son exécution avec succès pour démarrer. Si cette clause n'était pas présente, GitHub Actions pourrait décider de démarrer les deux _jobs_ en parallèle.

### 2.3 Secrets et environnement

Certains travaux de CI/CD nécessitent évidemment des secrets (clés d'API, mots de passe, etc.). On les enregistre sous la forme de couples clé-valeur dans les _**Settings**_ du projet (les valeurs ne sont plus consultables par la suite). Les _steps_ accèdent aux secrets en utilisant : ${{ secrets.NomDeSecret }}.

Par exemple, dans un _step_, l'appel d'une commande avec un argument provenant des secrets sous le nom MaCleApi se ferait comme ceci :

    steps:

    - run: ma-commande.sh ${{ secrets.MaCleApi }}

Les variables d'environnement, quant à elles, peuvent être fournies soit à un _step_ en particulier, soit à l'ensemble d'un _job_. Ces deux éléments prennent en charge la propriété env, qui est naturellement une _map_ YAML de couples clé-valeur.

Une variable d'environnement peut bien sûr contenir la valeur d'un secret.

jobs:

  my\_job\_with\_db:

    runs-on: ubuntu-20.04

    env:

      DB\_NAME: glmf

      DB\_PASS: ${{ secrets.MotDePasseDb }}

    steps:

    - run: ./upgrade-db.sh

Cependant, certains secrets ne doivent pas être confiés à n'importe quel _workflow_ sur n'importe quelle branche. Nous avons évoqué comment protéger les abus sur les fichiers de _workflow_ par des contributeurs non autorisés, mais même en rejetant une revue de code pour empêcher le _merge_ des changements, les _workflow__s_ sont néanmoins exécutés.

Pour éviter la fuite de secrets, GitHub propose depuis février 2021 **\[4\]** l'utilisation de plusieurs _Environments_ : il s'agit de désigner de l'extérieur (du _repo_, c'est-à-dire dans les _**Settings**_ et non dans les fichiers) quels _workflows_ sur quelles branches ont le droit de consulter quels secrets. Au sein d'un _workflow_ non autorisé, un _job_ qui tente d'accéder aux secrets protégés échouera automatiquement, et ne pourra donc pas effectuer une action risquant d'exfiltrer des valeurs confidentielles.

### 2.4 Checkout

Lorsqu'une action se déclenche, seul le contenu du fichier YAML de _workflow_ est initialement accessible au service GitHub Actions, à l'exclusion de tout autre élément du _repository_. Aussi, pour pouvoir effectuer des travaux utiles, une des premières choses à faire est souvent un _checkout_ du code.

GitHub Actions propose un mécanisme standard utilisant une _action_ communautaire (voir section 4) : c'est un bloc déjà écrit par quelqu'un d'autre, que l'on peut incorporer à un de nos _jobs_, comme _step_ à part entière. Au lieu de la propriété run, le _step_ indiquera plutôt une clause uses, et GitHub Actions saura aller chercher le bloc correspondant pour l'exécuter.

Ici, le mainteneur de l'action n'est autre que l'équipe GitHub elle-même, et l'action s'appelle actions/checkout@v2.

Voici typiquement comment débute le premier _job_ d'un _workflow_ devant travailler avec le code du projet :

jobs:

  compiler\_mon\_module:

    runs-on: ubuntu-20.04

    steps:

    - name: 'Checkout du code'

      uses: actions/checkout@v2

    - name: 'Dépendances et tests'

      run: |

        npm install

        npm run test

On aurait pu se passer de ce bloc réutilisable actions/checkout@v2 et effectuer la commande git clone explicitement, à condition d'avoir paramétré ce qu'il faut de secrets et de clés SSH ; mais c'est évidemment plus lisible ainsi, et de plus le code (en NodeJS) de l'action elle-même est ouvert **\[5\]**.

### 2.5 Conditions

Chaque _job_ ou _step_ peut être assujetti à une condition d'exécution. Pour ce faire, on instruira la clause if sur le _job_ ou le _step_ qui ne doit travailler que dans certains cas.

steps:

  - name: Màj DB Peut-être

    id: my-upgrade-db

    run: composer run migrate-db

    if: ${{ env.ONE + env.TWO }}

L'expression qui constitue la condition peut utiliser des informations de différentes provenances, dont entre autres :

*   les variables d'environnement : env.<MY\_VAR> ;
*   l'événement déclencheur (activité, branche, user…) : github.base\_ref ;
*   les valeurs de sortie de _steps_ précédents : steps.<id-de-step-amont\>.outputs.<ma\_valeur\>.

Chacune de ces provenances s'appelle un contexte **\[****6****\]** et s'utilise par un préfixe spécifique et une notation à point. Les expressions peuvent aussi utiliser des opérateurs &&, ||, \==, \>=, etc. ainsi que de quoi manipuler tableaux, chaînes de caractères et morceaux **JSON**.

Voyons maintenant comment mettre des données à disposition des _steps_ et _jobs_ qui suivent dans l'exécution du _workflow_.

### 2.6 Partage de données

Au sein d'un _job_, les _steps_ s'exécutent en séquence. Chaque _step_ peut consommer les résultats de _steps_ antérieurs du même _job_, du moment qu'ils ont positionné leurs valeurs de sortie selon une syntaxe particulière :

run: |

  echo "::set-output name=ma\_valeur::glmf"

Toute commande ou tout exécutable d'un _step_, qui effectue cette sortie standard, a pour effet de rendre disponible aux _steps_ suivants la donnée glmf pour l'expression ${{ steps.<id\_du\_step\>.outputs.<ma\_valeur\> }}.

Sans entrer dans le détail, précisons que sur un modèle similaire, les _jobs_ peuvent passer des valeurs à leurs successeurs, en déclarant quelles sorties de quels _steps_ un _job_ souhaite exporter. Naturellement, contrairement aux _steps_ qui ne sont jamais parallélisés, les _jobs_ peuvent s'exécuter simultanément. Par conséquent, un _job_ qui a besoin de la sortie d'un autre devra en indiquer son id dans une clause needs et sera forcément joué après lui.

### 2.7 Artifacts

Lorsque l'échange de simples valeurs ne suffit pas entre deux _jobs_, il est possible de stocker des _artifacts_ : ce sont des fichiers ou dossiers qu'on fait persister d'un _job_ à l'autre. Le _filesystem_ est remis à neuf pour chaque _job_, mais les _artifacts_ sont stockés dans un emplacement différent, fourni par GitHub Actions, et on les téléverse/télécharge grâce à deux actions préconstruites **\[7\]**.

Un _job_ stocke un _artifact_ comme ceci :

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

Et un _job_ ultérieur peut télécharger l'_artifact_ par :

  job-diffusion:

    steps:

      - name: Récupération du binaire

        uses: actions/download-artifact@v2

        with:

          name: glmf.so

      - name: Envoyer aux FTP amis

        run: ./envoyer.sh glmf.so

Les _artifacts_ restent conservés chez GitHub Actions pour une certaine durée (par défaut 90 jours) et sont accessibles via l'interface web.

# 3 Minuteurs et manivelle
--------------------------

Une grande variété d'événements déclencheurs de _workflows_ permet de couvrir tous les besoins du cycle de vie des projets : nouvelle _pull-request_, approuvée, rejetée, nouveau ticket, ticket fermé, nouveau tag, etc. **\[8\]**.

Mais on peut souligner l'existence de deux actions en particulier qui s'avèrent pratiques dans certains cas : le déclenchement manuel, par lequel on peut passer des paramètres personnalisés à chaque lancement, et le déclenchement planifié.

Le démarrage manuel s'obtient en utilisant l'événement workflow\_dispatch. Une section inputs permet de préciser les arguments attendus :

on:

  workflow\_dispatch:

    inputs:

      numero\_de\_rc:

        description: 'Entrez le numéro de Release Candidate à utiliser'

        required: true

      nom\_de\_code:

        description: 'Quel petit nom donner à cette version ?'

        required: false

        default: 'SNAPSHOT'

Ceci aura pour effet de présenter à l'utilisateur, au moment où il déclenchera le _workflow_, une _popup_ de saisie pour les deux paramètres précisés dans les _inputs_.

Quant au démarrage planifié, il s'agit d'un classique cron pour l'événement schedule (plusieurs programmations possibles) :

on:

  schedule:

   - cron: '0 10 \* \* MON'

   - cron: '30 12 \* \* SUN'

# 4 Écosystème d'actions
------------------------

La grammaire des _workflows_ est assez concise ; tout repose sur le caractère extensible du système. Comme nous l'avons vu avec le _checkout_, certaines des opérations les plus essentielles d'un _workflow_ s'effectuent au moyen de modules que tout un chacun est libre de fournir ou d'enrichir. À la mi-2021, un vaste catalogue **\[9\]** de près de 8 000 actions permet d'ajouter facilement aux _jobs_ des tâches les plus indispensables, comme la configuration de la version du **Java SDK**, aux plus exotiques telles que l'envoi d'un **Tweet**, en passant par les intégrations DevOps des principaux Clouds ou l'analyse statique de projets **PHP**.

GitHub Actions fournit un _toolkit_ pour rédiger des actions réutilisables **\[10\]**. Elles se développent soit en **NodeJS**, soit sous la forme d'images Docker, et se diffusent naturellement sur GitHub comme tout autre projet.

Par exemple, une action personnalisée en Node se présente comme un _repository_ mon-nom/mon-action, comportant à la racine un manifeste nommé action.yml. Il précise les arguments attendus et les valeurs de sortie de ce qui sera donc un _s__tep_ dans le _workflow_ de quelqu'un, et il indique le fichier .js d'entrée. Le reste est un projet Node comme un autre.

Le tout s'utilise dans un _job_ comme ceci :

your-job:

    steps:

      - name: "Avec l'action de mon ami"

        uses: mon-nom/mon-action@tag1

        with:

          un-arg: "glmf"

# 5 Ligne de commande
---------------------

On ne pourrait pas vraiment parler d'automatisation si la souris était le seul moyen d'aller vérifier la progression d'une tâche de CI/CD.

Voyons maintenant de quels outils en ligne de commande nous disposons.

### 5.1 GitHub CLI

Le programme gh **\[1****1****\]** est une sorte d'édition allégée de ce qu'on peut normalement faire avec la GUI du site GitHub.com.

La page du projet donne les instructions d'installation pour toutes les plateformes. Il s'agit d'un outil pour consulter et gérer _pull request_, _issues_ et _releases_. Mais plus précisément pour ce qui nous intéresse ici, il va aussi nous permettre de configurer les secrets et de superviser l'exécution des _workflows_. On commence par s'authentifier avec un compte GitHub :

$ gh auth statusYou are not logged into any GitHub hosts. Run gh auth login to authenticate.$ gh auth login? What account do you want to log into? \[Use arrows to move, type to filter\]

\> GitHub.com

  GitHub Enterprise Server

Suite à quoi, après d'autres questions concernant le nom du compte et la clé SSH à utiliser, on nous invite à pointer un navigateur sur une URL éphémère pour finaliser l'appairage. Puis on peut vérifier que l'authentification est prête :

$ gh auth status

github.com

  ✓ Logged in to github.com as my-github-handle (/home/gabriel/.config/gh/hosts.yml)

  ✓ Git operations for github.com configured to use ssh protocol.

  ✓ Token: \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Ensuite, on se place dans le _repository_ et on peut gérer les secrets :

~/projects/my-repo $ gh secret list

AWS\_ACCESS\_KEY\_ID      Updated 2021-01-12

AWS\_SECRET\_ACCESS\_KEY Updated 2021-01-12~/projects/my-repo $ gh secret set NEW\_SECR -b valeur\_confidentielle✓ Set secret NEW\_SECR for my-org/my-repo

L'autre opération importante que l'outil nous permet de faire est la vérification de l'état des _c__hecks_ d'une PR, c'est-à-dire les résultats de _workflows_ qu'elle a déclenchés. Noter que ces _c__hecks_ ne concernent pas que les GitHub Actions, mais également n'importe quel autre service de CI/CD sur lequel le _repository_ a été branché :

~/projects/my-repo $ gh pr listShowing 2 of 2 open pull requests in my-org/my-repo

#699 <...>#692 <...>

~/projects/my-repo $ gh pr checks 699All checks were successful0 failing, 1 successful, and 0 pending checks✓ ci/circleci: build    https://circleci.com/gh/...

### 5.2 Exécution locale de workflows

La mise au point de _workflows_ peut s'avérer laborieuse, surtout en raison de la nécessité de provoquer des _merge_ et autres opérations historisées sur le _repository_.

Pour faciliter ce travail, le projet nektos/act **\[1****2****\]**, écrit en **Go** et utilisant Docker, permet d'exécuter les _workflows_ en local, et de bénéficier d'une assez bonne émulation des _runners_ (Linux) de GitHub Actions.

Une fois l'outil act installé et configuré (se référer à la page du projet), on peut lister les _workflows_ qui sont rattachés à un _event_ donné, par exemple ici ceux qui déclarent démarrage manuel :

~/projects/my-repo $ act workflow\_dispatch --listID               Stage NamepromoteBackend   0      Release Backend to ProductionpromoteFrontend 0      Release Frontend app to Production

Nous allons ici démarrer le travail en mode _dry run_, en précisant quel fichier de _workflow_ lancer. Puisqu'il s'agit de workflow\_dispatch, sa _payload_ doit contenir une _map_ d'inputs. Le programme act attend sous la forme d'un fichier JSON la _payload_ de l'événement à simuler ; mais, pour plus de souplesse, nous allons utiliser le _Process Substition_ **\[1****3****\]** du **Bash** (ou **Zsh**) pour fournir au programme un descripteur de fichier non matérialisé, comme résultat d'une sortie de caractères :

~/projects/my-repo $ act workflow\_dispatch --dryrun --workflows .github/workflows/promote-backend.yml \--eventpath <( echo '{"inputs":{"tagNo":"v3.21"}}' )\*DRYRUN\* \[Promote Backend/promoteBackend\] 🚀 Start image=node:12.20.1-buster-slim

\*DRYRUN\* \[Promote Backend/promoteBackend\]   🐳 docker run image=node:12.20.1-buster-slim platform=linux/amd64 entrypoint=\["/usr/bin/tail" "-f" "/dev/null"\] cmd=\[\]

\*DRYRUN\* \[Promote Backend/promoteBackend\] ⭐ Run actions/checkout@v2

\*DRYRUN\* \[Promote Backend/promoteBackend\]   ✅ Success - actions/checkout@v2

\*DRYRUN\* \[Promote Backend/promoteBackend\] ⭐ Run Promote

\*DRYRUN\* \[Promote Backend/promoteBackend\]   ✅ Success - Promote

Cette méthode permet un cycle raccourci pour détecter les problèmes de variables manquantes et autres commandes mal paramétrées dans les _workflows_, en s'évitant les allers-retours vers GitHub.

Mais attention, à part en _dry run_, les commandes sont réellement exécutées ! En particulier, les interactions avec les plateformes d'hébergement et de Cloud, etc. Simplement, elles sont exécutées en local dans votre Docker plutôt que sur les ordinateurs de GitHub.

### 5.3 API pour le lancement manuel

On peut lancer un _workflow_ manuel chez GitHub Actions par l'API REST habituelle de GitHub **\[1****4****\]**. On doit tout d'abord créer un _Personal_ _Access_ _Token_ dans son profil sous _**Settings > Developer Settings**_, puis le stocker en lieu sûr, après quoi le _workflow_ manuel se déclenche comme ceci :

$ curl -u <mon-user>:<mon-token> \\   -X POST \\   -H "Accept: application/vnd.github.v3+json" \\   -d '{"ref":"master","inputs":{...}}' \\   https://api.github.com/repos/<owner>/<repo>/actions/workflows/<workflow>.yml/dispatches

On retrouve ici dans le corps de la requête JSON l'élément ref pour indiquer la branche master, ainsi que l'entrée inputs comme vu plus haut, puisqu'il s'agit du workflow\_dispatch.

# 6 Exemples
------------

Il ne nous reste plus qu'à mentionner quelques cas d'utilisation classiques.

### 6.1 AWS

Le géant du Cloud fournit avec la famille github.com/aws-actions tout le nécessaire pour manipuler ses infrastructures très simplement depuis un _job_ GitHub Action.

Muni des secrets pour les clés **AWS**, on commence par un _step_ qui configure les identifiants pour l'ensemble du _job_ :

your-job-with-aws:

    steps:

     - name: "Configuration des credentials pour AWS"

        uses: aws\-actions/configure-aws-credentials@v1

        with:

          aws-access-key-id: ${{ secrets.AWS\_ACCESS\_KEY\_ID }}

          aws-secret-access-key: ${{ secrets.AWS\_SECRET\_ACCESS\_KEY }}

          aws-region: us-east-1

Suite à quoi le CLI aws, déjà préinstallé sur tous les _runners_ GitHub Actions, se trouve authentifié et s'emploie sans difficulté.

D'autres actions utiles sont disponibles, par exemple, on peut identifier le Docker Engine du _runner_ sur le Registry ECR d'**Amazon** :

     - name: "Executer Docker Login avec les creds ECR"

        uses: aws\-actions/amazon-ecr-login@v1

Ceci pour pouvoir y effectuer les commandes habituelles docker push etc. dans les _steps_ ultérieurs.

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

Pour rappel, composer est déjà présent sur les _runners_ GitHub Actions, ainsi qu'un grand nombre d'outils spécifiques aux autres langages principaux.

### 6.3 Notification Slack

On peut enfin mentionner une action bien pratique, qui laisse deviner toutes les autres possibilités d'intégration du même genre. Le projet rtCamp/action-slack-notify n'est qu'une implémentation parmi d'autres qui ne répond sans doute pas à tous les besoins, mais sa simplicité de mise en œuvre illustre le propos.

      - name: Envoyer une notif Slack

        uses: rtCamp/action-slack-notify@v2

        env:

          SLACK\_WEBHOOK: ${{ secrets.SLACK\_WEBHOOK }}

          SLACK\_USERNAME: github

          SLACK\_TITLE: Votre nouvelle version est en ligne

          SLACK\_FOOTER: ""

          SLACK\_CHANNEL: build

La page du projet détaille les options disponibles et la façon d'obtenir auprès de **Slack** un _token_ de _webhook_.

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