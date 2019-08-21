# Setting up GitLab Runner

## Presentation

The purpose of this operation is to install and configure [gitlab-runner](https://docs.gitlab.com/runner/) to set up a continuous integration chain.

This allows you to launch tests (unitary, integration, validation) automatically after each commit. The results of these tests can be consulted in the GitLab project in the CI/CD part.

## Environment

Here we want to do CI for a Python backend using [Django REST framework](https://www.django-rest-framework.org/).
The server that hosts our Runner is running Debian.

Nevertheless, GitLab Runner is very flexible and can be implemented in a lot of environments.

For more details or installation in a different environment, consult the official documentation.

> [GitLab Runner](https://docs.gitlab.com/runner/) / *Official documentation*

## Installation

##### Install GitLab Runner

First, install [gitlab-runner](https://docs.gitlab.com/runner/) on the server.

  1. Add the official GitLab repository

  `curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash`

  2. Install the latest version of GitLab Runner

  `sudo apt-get install gitlab-runner`

> [Install GitLab Runner](https://docs.gitlab.com/runner/install/) / *Official documentation*

###### Create a Runner

Then we record a Runner. You can retrieve the token and consult your Runners in the GitLab project in the Settings > CI/CD > Runners section.

  `sudo gitlab-runner register`

1. Enter the GitLab instance url :
"Console
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com)
https://gitlab.com


2. Enter the token:
```console
Please enter the gitlab-ci token for this runner
xxx
```

3. Enter a description:
```console
Please enter the gitlab-ci description for this runner
gitlab ci runner
```

4. Enter the tags associated with the Runner:
```console
Please enter the gitlab-ci tags for this runner (comma separated):
my-tag,another-tag
```

5. Enter the Runner Executor:
```console
Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
shell
```

  The Runner is created and visible on GitLab.

  The Runner configuration is in `/etc/gitlab-runner/config.toml`.

> [Registering Runners](https://docs.gitlab.com/runner/register/index.html) / *Official documentation*

###### Configure the CI

Now you have to configure the integration chain.


To start, you must create a file **.gitlab-ci.yml** at the root of the GitLab project. This is where we will describe to GitLab how it will have to run our tests after a commit.

Here we simply launch the tests from an image and a docker volume.

```yml
#a docker image can be specified
image: visitadom_coordination

#stages are the steps performed after a commit
stages:
  - test
#  - build
#  - deploy

test:
  stage: test
  script: docker run -v ~/<path>/:/code/ <image> python manage.py test
  #we specify on which branch the tests must be executed
  only:
  - gitlab-CI
  ```

> [Getting started with GitLab CI/CD](https://docs.gitlab.com/ee/ci/quick_start/README.html) / *official documentation*

> [GitLab CI/CD Pipeline Configuration Reference](https://docs.gitlab.com/ee/ci/yaml/) / *Official documentation*

## Start the tests

To start the tests, you can run the file previously configured in several ways: locally, and after a commit.

Run GitLab Runner locally:

`gitlab-runner exec shell test`

To launch the service:

`gitlab-runner run`

Now, after each commit a Pipeline will be created with the name of the user who has pushed on GitLab > CI/CD > Pipelines.

> [GitLab Runner commands](https://docs.gitlab.com/runner/commands/) / *Official documentation*
