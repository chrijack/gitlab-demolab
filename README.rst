##############
GitLab Demolab
##############

This repository contains all files to set up a local network, consisting of a
CI/CD GitLab server, multiple (docker-in-docker compatible) GitLab runners, as
well as SonarQube.

Prerequisites
=============

Have ``docker`` and ``docker-compose`` installed.

Usage
=====

All variables are stored in an ``.env`` file:

::

   DEMO_NAME=my-demo
   DIND_VERSION=24.0.6
   EXTERNAL_URL=http://gitlab:8080/
   GITLAB_CE_VERSION=16.5.0-ce.0
   SONARQUBE_VERSION=10.2.1-community
   SONARQUBE_PASSWORD=sonarqubeadminpassword
   SONARQUBE_PORT=9000
   HTTP_PORT=8080
   GITLAB_PASSWORD=minimmm-12-COMPLXX
   SSH_PORT=7722

The ``.env`` file itself not supplied in this repository, but an example is: To
quickly get started, copy the file ``env-example`` file to ``.env``, edit the
values (especially the SonarQube and GitLab passwords), and you're good to go.
Note that the GITLAB_PASSWORD needs to be complex, otherwise installation will
fail, see https://about.gitlab.com/handbook/security/password-standard.html

.. code-block:: console

   cp env-example .env

   # Edit the values in .env

   ./installer.sh

This will fire up an instance of GitLab, three GitLab runners, capable of
performing Docker-in-Docker commands, and SonarQube.

Then, you'll need to register the runners with a valid runner token, which needs
to be created manually: Go to the Admin Area, click on Shared Runners, and click
on New instance runner (``/admin/runners/new``).  Select "Run untagged jobs" and
click on Create runner. This will generate a new runner token. Copy the token.

Then, execute the ``register-runners.sh`` script with that token as
parameter. This script will register all runners that are up and running.


Stopping / pausing the demolab
==============================

.. code-block:: console

   docker-compose stop

By default all data, configuration and logs will be stored in persistent Docker
volumes. They will remain, even if you stop or remove the Docker containers. To
clean everything up, remove the containers including named volumes.

.. code-block:: console

   docker-compose down --remove-orphans --volumes

Please note that this is a lab setup - and not meant to be used in production in
any way. All services only bind / listen to ``127.0.0.1`` by default (this can
be changed in the file ``docker-compose``

Variables
=========

The ``DEMO_NAME`` allows you to run multiple instances of this demo on the same
machine, as it will generate its own "Docker namespace".

``GITLAB_CE_VERSION`` allows you to specify which GitLab image you would like to
use. Please note that the runners always use the latest version.

``SONARQUBE_VERSION`` allows you to specify which SonarQube image you would like
to use.

``SONARQUBE_PASSWORD`` is the initial password for the admin user of SonarQube.

``SONARQUBE_PORT`` specifies on which port SonarQube can be accessed.

``HTTP_PORT`` specifies on which port the GitLab webinterface can be accessed.

The ``SSH`` port is the SSH port for GitLab.

If you want to access the webinterface, make sure that the ``EXTERNAL_URL`` can
be resolved by your browser. If you choose any other value than ``localhost``,
make sure that the hostname can be resolved (e.g. by adding it to the ``hosts``
file).
