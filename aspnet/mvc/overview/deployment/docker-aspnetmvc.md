---
uid: mvc/overview/deployment/docker
title: "Migration d’applications ASP.NET MVC vers des conteneurs Windows"
description: "Découvrez comment prendre une application ASP.NET MVC et l’exécuter dans un conteneur Docker Windows"
keywords: Windows Containers,Docker,ASP.NET MVC
author: BillWagner
ms.author: wiwagn
ms.date: 02/01/2017
ms.topic: article
ms.prod: .net-framework
ms.technology: dotnet-mvc
ms.devlang: dotnet
ms.assetid: c9f1d52c-b4bd-4b5d-b7f9-8f9ceaf778c4
ms.openlocfilehash: badc1c9b10ac27c3d876e3331c855a9d5904d27d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="migrating-aspnet-mvc-applications-to-windows-containers"></a>Migration d’applications ASP.NET MVC vers des conteneurs Windows

L’exécution d’une application .NET Framework existante dans un conteneur Windows ne nécessite aucune modification de votre application. Pour exécuter votre application dans un conteneur Windows, vous créez une image Docker contenant votre application et lancez le conteneur. Cette rubrique décrit comment prendre une [application ASP.NET MVC](http://www.asp.net/mvc) existante et la déployer dans un conteneur Windows.

Vous partez d’une application ASP.NET MVC existante, puis générez les ressources publiées à l’aide de Visual Studio. Vous utilisez Docker pour créer l’image qui contient et exécute votre application. Vous allez accéder au site en cours d’exécution dans un conteneur Windows et vérifier que l’application fonctionne.

Cet article suppose une connaissance élémentaire de Docker. Vous pouvez en savoir plus sur Docker en lisant l’article [Docker Overview](https://docs.docker.com/engine/understanding-docker/).

L’application que vous allez exécuter dans un conteneur est un site web simple qui répond à des questions de manière aléatoire. Cette application est une application MVC de base sans authentification ni stockage de base de données, qui vous permet de vous concentrer sur le déplacement de la couche web vers un conteneur. Des rubriques futures montreront comment déplacer et gérer le stockage persistant dans des applications en conteneur.

Le déplacement de votre application implique les étapes suivantes :

1. [Création d’une tâche de publication pour générer les ressources d’une image](#publish-script)
1. [Génération d’une image Docker destinée à exécuter votre application](#build-the-image)
1. [Démarrage d’un conteneur Docker qui exécute votre image](#start-a-container)
1. [Vérification de l’application à l’aide de votre navigateur](#verify-in-the-browser)

L’[application terminée](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator) se trouve sur GitHub.

## <a name="prerequisites"></a>Prérequis

L’ordinateur de développement doit être en cours d’exécution

- [Mise à jour anniversaire Windows 10](https://www.microsoft.com/software-download/windows10/) (ou version ultérieure) ou [Windows Server 2016](https://www.microsoft.com/cloud-platform/windows-server) (ou version ultérieure).
- [Docker pour Windows](https://docs.docker.com/docker-for-windows/), version stable 1.13.0 ou 1.12 bêta 26 (ou versions plus récentes)
- [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx).

> [!IMPORTANT]
> Si vous utilisez Windows Server 2016, suivez les instructions indiquées dans [Déploiement d’un hôte de conteneurs – Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).

Après avoir installé et démarré Docker, cliquez avec le bouton droit sur l’icône de barre d’état système et sélectionnez **Basculer vers les conteneurs Windows**. Cette opération est nécessaire pour exécuter des images Docker basées sur Windows. Cette commande prend quelques secondes :

![Conteneur Windows][windows-container]

## <a name="publish-script"></a>Script de publication

Collectez à un seul emplacement toutes les ressources à charger dans une image Docker. Vous pouvez utiliser la commande **Publier** de Visual Studio pour créer un profil de publication pour votre application. Ce profil placera toutes les ressources dans une arborescence de répertoires que vous copierez dans votre image cible plus loin dans ce didacticiel.

**Étapes de la publication**

1. Cliquez avec le bouton droit sur le projet web dans Visual Studio et sélectionnez **Publier**.
1. Cliquez sur le bouton **Profil personnalisé**, puis sélectionnez **Système de fichiers** comme méthode.
1. Choisissez le répertoire. Par convention, l’exemple téléchargé utilise `bin\Release\PublishOutput`.

![Connexion pour la publication][publish-connection]

Ouvrez la section **Options de publication des fichiers** sous l’onglet **Paramètres**. Sélectionnez **Précompiler durant la publication**. Cette optimisation signifie que, quand vous compilez les vues dans le conteneur Docker, vous copiez les vues précompilées.

![Paramètres de publication][publish-settings]

Cliquez sur **Publier** ; Visual Studio copie alors toutes les ressources nécessaires dans le dossier de destination.

## <a name="build-the-image"></a>Générer l’image

Définissez votre image Docker dans un fichier Dockerfile. Ce fichier contient des instructions pour l’image de base, des composants supplémentaires, l’application que vous souhaitez exécuter et d’autres images de configuration.  Le fichier Dockerfile est le paramètre d’entrée de la commande `docker build`, qui crée l’image.

Vous allez générer une image basée sur l’image `microsft/aspnet` qui se trouve sur le [Hub Docker](https://hub.docker.com/r/microsoft/aspnet/).
L’image de base, `microsoft/aspnet`, est une image Windows Server. Il contient Windows Server Core, IIS et ASP.NET 4.6.2. Quand vous exécutez cette image dans votre conteneur, elle démarre automatiquement IIS et tous les sites web installés.

Le fichier Dockerfile qui crée votre image ressemble à ceci :

```console
# The `FROM` instruction specifies the base image. You are
# extending the `microsoft/aspnet` image.

FROM microsoft/aspnet

# The final instruction copies the site you published earlier into the container.
COPY ./bin/Release/PublishOutput/ /inetpub/wwwroot
```

Ce fichier Dockerfile ne comprend pas de commande `ENTRYPOINT`. Vous n’en avez pas besoin. Lorsque vous exécutez Windows Server avec les services IIS, le processus IIS est le point d’entrée, qui est configuré pour démarrer dans l’image de base aspnet.

Exécutez la commande de génération Docker pour créer l’image qui exécute votre application ASP.NET. Pour ce faire, ouvrez une fenêtre PowerShell dans le répertoire de votre projet et tapez la commande suivante dans le répertoire de solution :

```console
docker build -t mvcrandomanswers .
```

Cette commande génère la nouvelle image en suivant les instructions dans votre fichier Dockerfile, d’affectation de noms (-t marquage) l’image en tant que mvcrandomanswers. Celles-ci peuvent inclure l’extraction de l’image de base du [Hub Docker](http://hub.docker.com), puis l’ajout de votre application à cette image.

Une fois cette commande terminée, vous pouvez exécuter la commande `docker images` pour afficher des informations sur la nouvelle image :

```console
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mvcrandomanswers              latest              86838648aab6        2 minutes ago       10.1 GB
```

L’ID de l’image sera différent sur votre ordinateur. À présent, exécutons l’application.

## <a name="start-a-container"></a>Démarrer un conteneur

Démarrez un conteneur en exécutant la commande `docker run` suivante :

```console
docker run -d --name randomanswers mvcrandomanswers
```

L’argument `-d` indique à Docker de démarrer l’image en mode détaché. Cela signifie que l’image Docker s’exécute déconnectée de l’interpréteur de commandes actuel.

Dans de nombreux exemples de docker, vous pouvez voir -p pour mapper les ports de conteneur et l’hôte. L’image d’aspnet par défaut a déjà configuré le conteneur pour écouter sur le port 80 et l’exposer. 

La portion `--name randomanswers` donne un nom au conteneur en cours d’exécution. Vous pouvez utiliser ce nom au lieu de l’ID de conteneur dans la plupart des commandes.

`mvcrandomanswers` est le nom de l’image à démarrer.

## <a name="verify-in-the-browser"></a>Vérifier dans le navigateur

> [!NOTE]
> Avec la version actuelle de conteneur Windows, vous ne pouvez pas rechercher `http://localhost`.
> Il s’agit d’un comportement connu dans WinNAT qui va être résolu. En attendant, vous devez utiliser l’adresse IP du conteneur.

Une fois le conteneur démarré, recherchez son adresse IP pour pouvoir vous connecter à votre conteneur en cours d’exécution à partir d’un navigateur :

```console
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" randomanswers
172.31.194.61
```

Se connecter au conteneur en cours d’exécution à l’aide de l’adresse IPv4, `http://172.31.194.61` dans l’exemple suivant. Tapez cette URL dans votre navigateur ; vous devriez voir le site en cours d’exécution.

> [!NOTE]
> Certains logiciels VPN ou proxy peuvent vous empêcher d’accéder à votre site.
> Vous pouvez les désactiver temporairement pour vérifier que votre conteneur fonctionne.

Le répertoire d’exemples sur GitHub contient un [script PowerShell](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator/run.ps1) qui exécute ces commandes pour vous. Ouvrez une fenêtre PowerShell, accédez au répertoire de votre solution et tapez la commande suivante :

```console
./run.ps1
```

La commande ci-dessus génère l’image, affiche la liste des images sur votre ordinateur, démarre un conteneur et affiche l’adresse IP de ce dernier.

Pour arrêter le conteneur, exécutez une commande `docker
stop` :

```console
docker stop randomanswers
```

Pour supprimer le conteneur, exécutez une commande `docker rm` :

```console
docker rm randomanswers
```

[windows-container]: media/aspnetmvc/SwitchContainer.png "Basculer vers le conteneur Windows"
[publish-connection]: media/aspnetmvc/PublishConnection.png "Publier dans le système de &fichiers"
[publish-settings]: media/aspnetmvc/PublishSettings.png "Paramètres de publication"
