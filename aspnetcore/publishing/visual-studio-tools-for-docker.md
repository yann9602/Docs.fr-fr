---
title: Visual Studio Tools pour Docker avec ASP.NET Core
description: "Cet article explique l’utilisation des outils Visual Studio 2017 et de Docker pour Windows pour mettre une application ASP.NET Core dans un conteneur."
keywords: Docker,ASP.NET Core,Visual Studio,conteneur
author: spboyer
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.prod: asp.net-core
ms.technology: aspnet
ms.assetid: 1f3b9a68-4dea-4b60-8cb3-f46164eedbbf
uid: publishing/vs-tools-for-docker
ms.openlocfilehash: 2d8e337141ae4e0d0258f1d7546510b0ab077e39
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="visual-studio-tools-for-docker"></a>Visual Studio Tools pour Docker

[Microsoft Visual Studio 2017](https://www.visualstudio.com/) avec [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/) prend en charge la création, le débogage et l’exécution d’applications web et console .NET Framework et .NET Core à l’aide de conteneurs Windows et Linux.

## <a name="prerequisites"></a>Conditions préalables

- [Microsoft Visual Studio 2017](https://www.visualstudio.com/) avec charge de travail .NET Core
- [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a>Installation et configuration

Installez [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) avec la charge de travail .NET Core. Si vous avez déjà installé Visual Studio, vous pouvez [modifier votre installation de Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) pour ajouter la charge de travail .NET Core.

Pour l’installation de Docker, passez en revue les informations contenues dans [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) et installez [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/).

Il est nécessaire de configurer des **[lecteurs partagés](https://docs.docker.com/docker-for-windows/#shared-drives)** dans Docker pour Windows. Ce paramétrage est nécessaire pour le mappage de volume et la prise en charge du débogage.

Cliquez avec le bouton droit sur l’icône Docker dans la barre d’état, cliquez sur **Settings** et sélectionnez **Shared drives**. Sélectionnez le lecteur où Docker stockera vos fichiers et appliquez les modifications.

![Shared Drives](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

## <a name="create-an-aspnet-web-application-and-add-docker-support"></a>Créer une application web ASP.NET et ajouter la prise en charge de Docker

Avec Visual Studio, créez une application web ASP.NET Core. Quand l’application est chargée, sélectionnez **Ajouter la prise en charge de Docker** dans le **menu Projet**, ou cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Ajouter** > **Prise en charge de Docker**.

*Menu projet*

![Projet – Ajouter la prise en charge de Docker](./visual-studio-tools-for-docker/_static/project-add-docker-support.png)

*Projet – Menu contextuel*

![Cliquer avec le bouton droit – Ajouter la prise en charge de Docker](./visual-studio-tools-for-docker/_static/right-click-add-docker-support.png)

Lorsque vous ajoutez la prise en charge Docker à votre projet, vous pouvez choisir des conteneurs Windows ou Linux. (L’hôte Docker doit exécuter le même type de conteneur. Si vous avez besoin de modifier le type de conteneur dans l’instance Docker en cours d’exécution, cliquez avec le bouton droit sur l’icône **Docker** dans la barre d’état, puis choisissez **Basculer vers les conteneurs Windows** ou **Basculer vers les conteneurs Linux**.) 

Les fichiers suivants sont ajoutés au projet :

- **Dockerfile** : le fichier Docker pour les applications ASP.NET Core est basé sur l’image [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore). Cette image inclut les packages NuGet ASP.NET Core, qui ont été prétraités avec JiT pour améliorer les performances au démarrage. Lors de la génération d’applications de console .NET Core, le fichier Dockerfile FROM fait référence à l’image [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) la plus récente.   
- **docker-compose.yml** : fichier Compose Docker de base utilisé pour définir la collection d’images à générer et à exécuter avec docker-compose build/run.   
- **docker-compose.dev.debug.yml** : fichier docker-compose supplémentaire avec des modifications itératives quand votre configuration est définie sur Debug. Visual Studio appelle -f docker-compose.yml -f docker-compose.dev.debug.yml pour fusionner ces éléments. Ce fichier compose est utilisé par les outils de développement de Visual Studio.   
- **docker-compose.dev.release.yml** : fichier Compose Docker supplémentaire pour déboguer votre définition de version. Il va monter le débogueur sur un volume afin qu’il ne change pas le contenu de l’image de production.  

Le fichier *docker-compose.yml* contient le nom de l’image qui est créée lors de l’exécution du projet. 

```
version '2'

services:
  hellodockertools:
    image:  user/hellodockertools${TAG}
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "80"
``` 

Dans cet exemple, `image: user/hellodockertools${TAG}` génère l’image `user/hellodockertools:dev` quand l’application est exécutée en mode **Debug** et `user/hellodockertools:latest` en mode **Release**, respectivement. 

Vous pouvez changer `user` pour votre nom d’utilisateur [Docker Hub](https://hub.docker.com/) si vous envisagez de placer l’image dans le Registre, par exemple `spboyer/hellodockertools`, ou vous pouvez changer pour votre URL de Registre privé `privateregistry.domain.com/`, en fonction de votre configuration.

### <a name="debugging"></a>Débogage

Sélectionnez **Docker** dans la liste déroulante de débogage dans la barre d’outils et utilisez la touche F5 pour démarrer le débogage de l’application. 

- L’image *microsoft/aspnetcore* est acquise (si elle n’est pas déjà dans le cache)
- *ASPNETCORE_ENVIRONMENT* est défini sur Développement au sein du conteneur
- Le PORT 80 est EXPOSÉ et mappé à un port affecté dynamiquement pour localhost. Le port est déterminé par l’hôte docker et peut être interrogé avec docker ps. 
- Votre application est copiée dans le conteneur
- Le navigateur par défaut est lancé avec le débogueur attaché au conteneur, en utilisant le port affecté dynamiquement. 

L’image Docker générée est l’image de *développement* de votre application avec les images *microsoft/aspnetcore* comme image de base.

**Remarque :** L’image de développement n’inclut pas le contenu de votre application car les configurations Debug utilisent le montage de volume pour fournir l’expérience itérative. Pour placer une image, utilisez la configuration Release.

```console
REPOSITORY                  TAG         IMAGE ID            CREATED         SIZE
spboyer/hellodockertools    dev         0b6e2e44b3df        4 minutes ago   268.9 MB
microsoft/aspnetcore        1.0.1       189ad4312ce7        5 days ago      268.9 MB
```

L’application s’exécute en utilisant le conteneur que vous pouvez voir en exécutant la commande `docker ps`.

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   4 minutes ago       Up 4 minutes        0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="edit-and-continue"></a>Modifier & Continuer

Les modifications apportées à des fichiers statiques et/ou à des fichiers modèles Razor (*.cshtml*) sont automatiquement mises à jour qu’une étape de compilation soit nécessaire. Apportez les modifications, enregistrez et cliquez sur Actualiser dans le navigateur pour voir la mise à jour.  

Les modifications apportées aux fichiers de code nécessitent une compilation et un redémarrage de Kestrel au sein du conteneur. Après avoir effectué la modification, utilisez Ctrl+F5 pour exécuter le processus et démarrer l’application au sein du conteneur. Le conteneur Docker n’est pas régénéré ni arrêté ; en utilisant `docker ps` sur la ligne de commande, vous pouvez voir que le conteneur d’origine est toujours en cours d’exécution comme 10 minutes auparavant. 

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   10 minutes ago      Up 10 minutes       0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="publishing-docker-images"></a>Publication des images Docker

Une fois que vous avez terminé le cycle de développement et de débogage de votre application, Visual Studio Tools pour Docker vous aide à créer l’image de production de votre application. Changez la liste déroulante de Debug en **Release** et générez l’application. Les outils produisent l’image avec l’étiquette `:latest`, que vous pouvez distribuer sur votre Registre privé ou sur Docker Hub. 

Avec la commande `docker images`, vous pouvez voir la liste des images.

```console
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
spboyer/hellodockertools   latest              8184ae38ba91        5 seconds ago       278.4 MB
spboyer/hellodockertools   dev                 0b6e2e44b3df        About an hour ago   268.9 MB
microsoft/aspnetcore       1.0.1               189ad4312ce7        5 days ago          268.9 MB
```

Vous pourriez vous attendre à ce que l’image de production ou de version ait une taille inférieure à celle de l’image de **développement** en raison de l’utilisation du mappage de volume ; le débogueur et l’application ont été en réalité exécutés à partir de votre machine locale et non pas dans le conteneur. L’image **latest** a packagé tout le code de l’application nécessaire pour l’exécuter sur un ordinateur hôte : le delta correspond donc à la taille du code de votre application.
