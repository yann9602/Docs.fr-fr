---
title: Visual Studio Tools pour Docker avec ASP.NET Core
author: spboyer
description: "Découvrez comment utiliser les outils Visual Studio 2017 et Docker pour Windows pour mettre une application ASP.NET Core dans un conteneur."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 12/12/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: caf0e423d8e6f61fd2470d1f4ea2dd93909c3696
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="visual-studio-tools-for-docker-with-aspnet-core"></a>Visual Studio Tools pour Docker avec ASP.NET Core

[Visual Studio 2017](https://www.visualstudio.com/) prend en charge la création, le débogage et l’exécution d’applications ASP.NET Core ciblant le .NET Framework ou .NET Core. Les conteneurs Windows et Linux sont pris en charge.

## <a name="prerequisites"></a>Prérequis

* [Visual Studio 2017](https://www.visualstudio.com/) avec la charge de travail **Développement multiplateforme .NET Core**
* [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a>Installation et configuration

Pour l’installation de Docker, passez en revue les informations contenues dans [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) et installez [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/).

Il est nécessaire de configurer les **[lecteurs partagés](https://docs.docker.com/docker-for-windows/#shared-drives)** dans Docker pour Windows pour prendre en charge le mappage de volume et le débogage. Cliquez sur l’icône de Docker, sélectionnez **paramètres...** , puis sélectionnez **des lecteurs partagés**. Sélectionnez le lecteur où Docker stocke les fichiers. Sélectionnez **appliquer**.

![Shared Drives](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Demander à Visual Studio 2017 15,6 et versions ultérieures lorsque **des lecteurs partagés** ne sont pas configurés.

## <a name="add-docker-support-to-an-app"></a>Ajouter la prise en charge de Docker à une application

La version cible de .NET Framework du projet ASP.NET Core détermine les types de conteneurs pris en charge. Les projets ciblant .NET Core prennent en charge les conteneurs Linux et Windows. Les projets ciblant le .NET Framework prennent seulement en charge les conteneurs Windows.

Lorsque vous ajoutez la prise en charge Docker à un projet, choisissez Windows ou un conteneur de Linux. L’hôte Docker doit exécuter le même type de conteneur. Pour changer le type de conteneur dans l’instance de Docker en cours d’exécution, cliquez avec le bouton droit sur l’icône Docker de la zone de notification, puis choisissez **Basculer vers les conteneurs Windows...** ou **Basculer vers les conteneurs Linux...**.

### <a name="new-app"></a>Nouvelle application

Quand vous créez une application avec les modèles de projet **Application web ASP.NET Core**, cochez la case **Activer la prise en charge de Docker** :

![Case Activer la prise en charge de Docker](visual-studio-tools-for-docker/_static/enable-docker-support-checkbox.png)

Si le framework cible est .NET Core, la **système d’exploitation** déroulante permet la sélection d’un type de conteneur.

### <a name="existing-app"></a>Application existante

Visual Studio Tools pour Docker ne prend pas en charge l’ajout de Docker à un projet ASP.NET Core existant ciblant le .NET Framework. Pour les projets ASP.NET Core ciblant .NET Core, il existe deux options pour ajouter la prise en charge de Docker via les outils. Ouvrez le projet dans Visual Studio et choisissez l’une des options suivantes :

* Sélectionnez **Prise en charge de Docker** dans le menu **Projet**.
* Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Ajouter** > **Prise en charge de Docker**.

## <a name="docker-assets-overview"></a>Vue d’ensemble des ressources de Docker

Visual Studio Tools pour Docker ajoute un projet *docker-compose* à la solution, qui contient les éléments suivants :

* *.dockerignore* : contient une liste de modèles de fichiers et de répertoires à exclure lors de la génération d’un contexte de build.
* *docker-compose.yml* : fichier [Docker Compose](https://docs.docker.com/compose/overview/) de base utilisé pour définir la collection d’images à générer et à exécuter avec `docker-compose build` et `docker-compose run`, respectivement.
* *docker-compose.override.yml* : fichier facultatif, lu par Docker Compose, contenant les remplacements de configuration pour les services. Visual Studio exécute `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` pour fusionner ces fichiers.

Un fichier *Dockerfile*, la recette de la création d’une image Docker finale, est ajouté à la racine du projet. Reportez-vous à [Informations de référence sur Dockerfile](https://docs.docker.com/engine/reference/builder/) pour comprendre les commandes qu’il contient. Ce fichier *Dockerfile* particulier utilise une [build en plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) contenant quatre étapes de build nommées distinctes :

[!code-text[](visual-studio-tools-for-docker/samples/HelloDockerTools/HelloDockerTools/Dockerfile?highlight=1,5,14,17)]

Le fichier *Dockerfile* est basé sur l’image [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore). Cette image de base inclut les packages NuGet ASP.NET Core, qui ont subi un traitement pré-JIT pour améliorer les performances au démarrage.

Le *docker-compose.yml* fichier contient le nom de l’image qui est créé lors de l’exécution du projet :

[!code-yaml[](visual-studio-tools-for-docker/samples/HelloDockerTools/docker-compose.yml?highlight=5)]

Dans l’exemple précédent, `image: hellodockertools` génère l’image `hellodockertools:dev` quand l’application est exécutée en mode **débogage**. L’image `hellodockertools:latest` est générée quand l’application est exécutée en mode **mise en production**.

Préfixe du nom de l’image avec le [Hub Docker](https://hub.docker.com/) nom d’utilisateur (par exemple, `dockerhubusername/hellodockertools`) si l’image est poussée vers le Registre. Vous pouvez également modifier le nom de l’image à inclure l’URL de registres privés (par exemple, `privateregistry.domain.com/hellodockertools`) selon la configuration.

## <a name="debug"></a>Débogage

Sélectionnez **Docker** dans la liste déroulante de débogage dans la barre d’outils et démarrez le débogage de l’application. La vue **Docker** de la fenêtre **Sortie** affiche les actions suivantes qui se déroulent :

* Le *microsoft/aspnetcore* acquisition d’image d’exécution (si pas déjà dans le cache).
* Le *aspnetcore/microsoft-build* compilation/publier l’image est acquis (si pas déjà dans le cache).
* La variable d’environnement *ASPNETCORE_ENVIRONMENT* a la valeur `Development` dans le conteneur.
* Le port 80 est exposé et mappé à un port affecté dynamiquement pour localhost. Le port est déterminé par l’hôte Docker et peut être interrogé avec la commande `docker ps`.
* L’application est copiée dans le conteneur.
* Le navigateur par défaut est lancé avec le débogueur attaché au conteneur via le port attribué de manière dynamique. 

L’image Docker qui en résulte est la *dev* l’image de l’application avec le *microsoft/aspnetcore* images en tant que l’image de base. Exécutez la commande `docker images` dans la fenêtre **Console du Gestionnaire de package**. Les images sur l’ordinateur s’affichent :

```console
REPOSITORY                   TAG                   IMAGE ID            CREATED             SIZE
hellodockertools             latest                f8f9d6c923e2        About an hour ago   391MB
hellodockertools             dev                   85c5ffee5258        About an hour ago   389MB
microsoft/aspnetcore-build   2.0-nanoserver-1709   d7cce94e3eb0        15 hours ago        1.86GB
microsoft/aspnetcore         2.0-nanoserver-1709   8872347d7e5d        40 hours ago        389MB
```

> [!NOTE]
> L’image de développement n’a pas le contenu de l’application, en tant que **déboguer** configurations utilisent le montage de volume pour fournir une expérience itérative. Pour placer une image, utilisez la configuration **release**.

Exécutez la commande `docker ps` dans la console du Gestionnaire de package. Notez que l’application s’exécute à l’aide du conteneur :

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Modifier & Continuer

Les modifications apportées à des fichiers statiques et à des vues Razor sont automatiquement mises à jour sans qu’une étape de compilation ne soit nécessaire. Apportez la modification, enregistrez et actualisez le navigateur pour afficher la mise à jour.  

Les modifications apportées aux fichiers de code nécessitent une compilation et un redémarrage de Kestrel au sein du conteneur. Après avoir effectué la modification, utilisez Ctrl+F5 pour exécuter le processus et démarrer l’application au sein du conteneur. Le conteneur Docker n’est pas reconstruit ou arrêté. Exécutez la commande `docker ps` dans la console du Gestionnaire de package. Notez que le conteneur d’origine est toujours en cours d’exécution comme 10 minutes auparavant :

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publier des images Docker

Une fois le cycle de développement et de débogage de l’application terminé, Visual Studio Tools pour Docker vous aider à créer l’image de production de l’application. Changez la liste déroulante de configuration en **Release** et générez l’application. Les outils de produit de l’image avec le *dernière* balise, qui peut être appliquée sur le Registre privé ou le Hub d’ancrage. 

Exécutez la commande `docker images` dans la console du Gestionnaire de package pour afficher la liste des images :

```console
REPOSITORY                   TAG                   IMAGE ID            CREATED             SIZE
hellodockertools             latest                4cb1fca533f0        19 seconds ago      391MB
hellodockertools             dev                   85c5ffee5258        About an hour ago   389MB
microsoft/aspnetcore-build   2.0-nanoserver-1709   d7cce94e3eb0        16 hours ago        1.86GB
microsoft/aspnetcore         2.0-nanoserver-1709   8872347d7e5d        40 hours ago        389MB
```

> [!NOTE]
> La commande `docker images` retourne des images intermédiaires avec des noms de dépôt et des balises identifiées comme *\<none>* (non répertoriées ci-dessus). Ces images sans nom sont produites par la [build en plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*. Elles améliorent l’efficacité de la création de l’image finale ; seules les couches nécessaires sont regénérées en cas de modifications. Lorsque les images intermédiaires ne sont plus nécessaires, supprimez-les à l’aide de la [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) commande.

Vous pourriez vous attendre à ce que l’image de production ou de publication ait une taille inférieure à l’image de *développement*. En raison du mappage de volume, le débogueur et l’application en cours d’exécution de l’ordinateur local et non dans le conteneur. L’image *latest* a empaqueté le code de l’application nécessaire pour l’exécuter sur un ordinateur hôte. Par conséquent, le fichier delta est la taille du code d’application.
