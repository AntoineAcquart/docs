# Mise en place de GitLab Runner

## Présentation

Le but de cette opération est d'installer et de configurer [gitlab-runner](https://docs.gitlab.com/runner/) pour mettre en place une chaîne d'intégration continue.

Cela permet de pouvoir lancer des tests (unitaire, intégration, validation) automatiquement après chaque commit. Les résultats de ces tests peuvent être consultés dans le projet GitLab partie CI/CD.

## Environnement

Ici on veut faire de la CI pour un backend Python utilisant [Django REST framework](https://www.django-rest-framework.org/).
Le serveur qui héberge notre Runner est sous Debian.

Néanmoins, GitLab Runner est très souple et peut être mis en place sur un grand nombre d'environnements différents.

Pour plus de détails ou une installation sur un environnement différent, consulter la documentation officielle.

> [GitLab Runner](https://docs.gitlab.com/runner/) / *documentation officielle*

## Installation

#### Installer GitLab Runner

Il faut commencer par installer [gitlab-runner](https://docs.gitlab.com/runner/) sur le serveur.

  1. Ajouter le repository GitLab officiel

  `curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash`

  2. Installer la dernière version de GitLab Runner

  `sudo apt-get install gitlab-runner`

> [Install GitLab Runner](https://docs.gitlab.com/runner/install/) / *documentation officielle*

#### Créer un Runner

Ensuite, on enregistre un Runner. On peut récupérer le token et consulter ses Runners dans le projet GitLab partie Settings > CI/CD > Runners.

  `sudo gitlab-runner register`

1. Entrer l'url d'instance GitLab :
```console
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
https://gitlab.com
```

2. Entrer le token :
```console
Please enter the gitlab-ci token for this runner
xxx
```

3. Entrer une description :
```console
Please enter the gitlab-ci description for this runner
gitlab ci runner
```

4. Entrer les tags associés au Runner :
```console
Please enter the gitlab-ci tags for this runner (comma separated):
my-tag,another-tag
```

5. Entrer l’exécuteur du Runner :
```console
Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
shell
```

  Le Runner est créé, il est visible sur GitLab.

  La configuration du Runner se trouve dans `/etc/gitlab-runner/config.toml`.

> [Registering Runners](https://docs.gitlab.com/runner/register/index.html) / *documentation officielle*

#### Configurer la CI

Maintenant il faut configurer la chaine d'intégration.


Pour commencer, il faut créer un fichier **.gitlab-ci.yml** à la racine du projet GitLab. C'est ici qu'on va décrire à GitLab comment il va devoir exécuter nos tests après un commit.

Ici on lance simplement les tests à partir d'une image et d'un volume docker.

```yml
#une image docker peut-être spécifiée
image: visitadom_coordination

#les stages sont les étapes réalisées après un commit
stages:
  - test
#  - build
#  - deploy

test:
  stage: test
  script: docker run -v ~/<path>/:/code/ <image> python manage.py test
  #on spécifie sur quelle branche les tests doivent être exécutés
  only:
  - gitlab-CI
  ```

> [Getting started with GitLab CI/CD](https://docs.gitlab.com/ee/ci/quick_start/README.html) / *documentation officielle*

> [GitLab CI/CD Pipeline Configuration Reference](https://docs.gitlab.com/ee/ci/yaml/) / *documentation officielle*

## Lancer les tests

Pour lancer les tests, on peut exécuter le fichier précédement configuré de plusieurs manières : localement, et après un commit.

Exécuter localement GitLab Runner :

`gitlab-runner exec shell test`

Pour lancer le service :

`gitlab-runner run`

Maintenant, après chaque commit une Pipeline sera créée au nom de l'utilisateur qui a push sur GitLab > CI/CD > Pipelines.

> [GitLab Runner commands](https://docs.gitlab.com/runner/commands/) / *documentation officielle*
