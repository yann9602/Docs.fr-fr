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
ms.openlocfilehash: bc436e2c02b05475b84cf9f8bdedf9463a673c4a
ms.sourcegitcommit: b861bab71ea6945f673c62223ae2cba3aa74cb6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="visual-studio-tools-for-docker"></a><span data-ttu-id="68cb5-104">Visual Studio Tools pour Docker</span><span class="sxs-lookup"><span data-stu-id="68cb5-104">Visual Studio Tools for Docker</span></span>

<span data-ttu-id="68cb5-105">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) avec [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/) prend en charge la création, le débogage et l’exécution d’applications web et console .NET Framework et .NET Core à l’aide de conteneurs Windows et Linux.</span><span class="sxs-lookup"><span data-stu-id="68cb5-105">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) with [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) supports building, debugging, and running .NET Framework and .NET Core web and console applications using Windows and Linux containers.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68cb5-106">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="68cb5-106">Prerequisites</span></span>

- <span data-ttu-id="68cb5-107">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) avec charge de travail .NET Core</span><span class="sxs-lookup"><span data-stu-id="68cb5-107">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) with .NET Core workload</span></span>
- [<span data-ttu-id="68cb5-108">Docker pour Windows</span><span class="sxs-lookup"><span data-stu-id="68cb5-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a><span data-ttu-id="68cb5-109">Installation et configuration</span><span class="sxs-lookup"><span data-stu-id="68cb5-109">Installation and setup</span></span>

<span data-ttu-id="68cb5-110">Installez [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) avec la charge de travail .NET Core.</span><span class="sxs-lookup"><span data-stu-id="68cb5-110">Install [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) with the .NET Core workload.</span></span>

<span data-ttu-id="68cb5-111">Pour l’installation de Docker, passez en revue les informations contenues dans [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) et installez [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="68cb5-111">For Docker installation, review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) and install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="68cb5-112">Il est nécessaire de configurer des **[lecteurs partagés](https://docs.docker.com/docker-for-windows/#shared-drives)** dans Docker pour Windows.</span><span class="sxs-lookup"><span data-stu-id="68cb5-112">A required configuration is to setup **[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows.</span></span> <span data-ttu-id="68cb5-113">Ce paramétrage est nécessaire pour le mappage de volume et la prise en charge du débogage.</span><span class="sxs-lookup"><span data-stu-id="68cb5-113">The setting is required for the volume mapping and debugging support.</span></span>

<span data-ttu-id="68cb5-114">Cliquez avec le bouton droit sur l’icône Docker dans la barre d’état, cliquez sur **Settings** et sélectionnez **Shared drives**.</span><span class="sxs-lookup"><span data-stu-id="68cb5-114">Right-click the Docker icon in the System Tray, click **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="68cb5-115">Sélectionnez le lecteur où Docker stockera vos fichiers et appliquez les modifications.</span><span class="sxs-lookup"><span data-stu-id="68cb5-115">Select the drive where Docker will store your files and apply changes.</span></span>

![Shared Drives](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

## <a name="create-an-aspnet-web-application-and-add-docker-support"></a><span data-ttu-id="68cb5-117">Créer une application web ASP.NET et ajouter la prise en charge de Docker</span><span class="sxs-lookup"><span data-stu-id="68cb5-117">Create an ASP.NET Web Application and add Docker Support</span></span>

<span data-ttu-id="68cb5-118">Avec Visual Studio, créez une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68cb5-118">Using Visual Studio, create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="68cb5-119">Quand l’application est chargée, sélectionnez **Ajouter la prise en charge de Docker** dans le **menu Projet**, ou cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Ajouter** > **Prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="68cb5-119">When the application is loaded, either select **Add Docker Support** from the **Project Menu** or right-click the project from the Solution Explorer and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="68cb5-120">*Menu projet*</span><span class="sxs-lookup"><span data-stu-id="68cb5-120">*Project Menu*</span></span>

![Projet – Ajouter la prise en charge de Docker](./visual-studio-tools-for-docker/_static/project-add-docker-support.png)

<span data-ttu-id="68cb5-122">*Projet – Menu contextuel*</span><span class="sxs-lookup"><span data-stu-id="68cb5-122">*Project Context Menu*</span></span>

![Cliquer avec le bouton droit – Ajouter la prise en charge de Docker](./visual-studio-tools-for-docker/_static/right-click-add-docker-support.png)

<span data-ttu-id="68cb5-124">Lorsque vous ajoutez la prise en charge Docker à votre projet, vous pouvez choisir des conteneurs Windows ou Linux.</span><span class="sxs-lookup"><span data-stu-id="68cb5-124">When you add Docker support to your project, you can choose either Windows or Linux containers.</span></span> <span data-ttu-id="68cb5-125">(L’hôte Docker doit exécuter le même type de conteneur.</span><span class="sxs-lookup"><span data-stu-id="68cb5-125">(The Docker host must be running the same container type.</span></span> <span data-ttu-id="68cb5-126">Si vous avez besoin de modifier le type de conteneur dans l’instance Docker en cours d’exécution, cliquez avec le bouton droit sur l’icône **Docker** dans la barre d’état, puis choisissez **Basculer vers les conteneurs Windows** ou **Basculer vers les conteneurs Linux**.)</span><span class="sxs-lookup"><span data-stu-id="68cb5-126">If you need change the container type in the running Docker instance, right-click the **Docker** icon in the System Tray, and choose **Switch to Windows containers** or **Switch to Linux containers**.)</span></span> 

<span data-ttu-id="68cb5-127">Les fichiers suivants sont ajoutés au projet :</span><span class="sxs-lookup"><span data-stu-id="68cb5-127">The following files are added to the project:</span></span>

- <span data-ttu-id="68cb5-128">**Dockerfile** : le fichier Docker pour les applications ASP.NET Core est basé sur l’image [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore).</span><span class="sxs-lookup"><span data-stu-id="68cb5-128">**Dockerfile**: the Docker file for ASP.NET Core applications is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) image.</span></span> <span data-ttu-id="68cb5-129">Cette image inclut les packages NuGet ASP.NET Core, qui ont été prétraités avec JiT pour améliorer les performances au démarrage.</span><span class="sxs-lookup"><span data-stu-id="68cb5-129">This image includes the ASP.NET Core NuGet packages, which have been pre-jitted improving startup performance.</span></span> <span data-ttu-id="68cb5-130">Lors de la génération d’applications de console .NET Core, le fichier Dockerfile FROM fait référence à l’image [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) la plus récente.</span><span class="sxs-lookup"><span data-stu-id="68cb5-130">When building .NET Core Console Applications, the Dockerfile FROM will reference the most recent [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) image.</span></span>   
- <span data-ttu-id="68cb5-131">**docker-compose.yml** : fichier Compose Docker de base utilisé pour définir la collection d’images à générer et à exécuter avec docker-compose build/run.</span><span class="sxs-lookup"><span data-stu-id="68cb5-131">**docker-compose.yml**: base Docker Compose file used to define the collection of images to be built and run with docker-compose build/run.</span></span>   
- <span data-ttu-id="68cb5-132">**docker-compose.dev.debug.yml** : fichier docker-compose supplémentaire avec des modifications itératives quand votre configuration est définie sur Debug.</span><span class="sxs-lookup"><span data-stu-id="68cb5-132">**docker-compose.dev.debug.yml**: additional docker-compose file with for iterative changes when your configuration is set to debug.</span></span> <span data-ttu-id="68cb5-133">Visual Studio appelle -f docker-compose.yml -f docker-compose.dev.debug.yml pour fusionner ces éléments.</span><span class="sxs-lookup"><span data-stu-id="68cb5-133">Visual Studio will call -f docker-compose.yml -f docker-compose.dev.debug.yml to merge these together.</span></span> <span data-ttu-id="68cb5-134">Ce fichier compose est utilisé par les outils de développement de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68cb5-134">This compose file is used by Visual Studio development tools.</span></span>   
- <span data-ttu-id="68cb5-135">**docker-compose.dev.release.yml** : fichier Compose Docker supplémentaire pour déboguer votre définition de version.</span><span class="sxs-lookup"><span data-stu-id="68cb5-135">**docker-compose.dev.release.yml**: additional Docker Compose file to debug your release definition.</span></span> <span data-ttu-id="68cb5-136">Il va monter le débogueur sur un volume afin qu’il ne change pas le contenu de l’image de production.</span><span class="sxs-lookup"><span data-stu-id="68cb5-136">It will volume mount the debugger so it doesn't change the contents of the production image.</span></span>  

<span data-ttu-id="68cb5-137">Le fichier *docker-compose.yml* contient le nom de l’image qui est créée lors de l’exécution du projet.</span><span class="sxs-lookup"><span data-stu-id="68cb5-137">The *docker-compose.yml* file contains the name of the image that is created when project is run.</span></span> 

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

<span data-ttu-id="68cb5-138">Dans cet exemple, `image: user/hellodockertools${TAG}` génère l’image `user/hellodockertools:dev` quand l’application est exécutée en mode **Debug** et `user/hellodockertools:latest` en mode **Release**, respectivement.</span><span class="sxs-lookup"><span data-stu-id="68cb5-138">In this example, `image: user/hellodockertools${TAG}` generates the image `user/hellodockertools:dev` when the application is run in **Debug** mode and `user/hellodockertools:latest` in **Release** mode respectively.</span></span> 

<span data-ttu-id="68cb5-139">Vous pouvez changer `user` pour votre nom d’utilisateur [Docker Hub](https://hub.docker.com/) si vous envisagez de placer l’image dans le Registre,</span><span class="sxs-lookup"><span data-stu-id="68cb5-139">You will want to change the `user` to your [Docker Hub](https://hub.docker.com/) username if you plan to push the image to the registry.</span></span> <span data-ttu-id="68cb5-140">par exemple `spboyer/hellodockertools`, ou vous pouvez changer pour votre URL de Registre privé `privateregistry.domain.com/`, en fonction de votre configuration.</span><span class="sxs-lookup"><span data-stu-id="68cb5-140">For example, `spboyer/hellodockertools`, or change to your private registry URL `privateregistry.domain.com/` depending on your configuration.</span></span>

### <a name="debugging"></a><span data-ttu-id="68cb5-141">Débogage</span><span class="sxs-lookup"><span data-stu-id="68cb5-141">Debugging</span></span>

<span data-ttu-id="68cb5-142">Sélectionnez **Docker** dans la liste déroulante de débogage dans la barre d’outils et utilisez la touche F5 pour démarrer le débogage de l’application.</span><span class="sxs-lookup"><span data-stu-id="68cb5-142">Select **Docker** from the debug drop-down in the toolbar and use F5 to start debugging the application.</span></span> 

- <span data-ttu-id="68cb5-143">L’image *microsoft/aspnetcore* est acquise (si elle n’est pas déjà dans le cache)</span><span class="sxs-lookup"><span data-stu-id="68cb5-143">The *microsoft/aspnetcore* image is acquired (if not already in your cache)</span></span>
- <span data-ttu-id="68cb5-144">*ASPNETCORE_ENVIRONMENT* est défini sur Développement au sein du conteneur</span><span class="sxs-lookup"><span data-stu-id="68cb5-144">*ASPNETCORE_ENVIRONMENT* is set to Development within the container</span></span>
- <span data-ttu-id="68cb5-145">Le PORT 80 est EXPOSÉ et mappé à un port affecté dynamiquement pour localhost.</span><span class="sxs-lookup"><span data-stu-id="68cb5-145">PORT 80 is EXPOSED and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="68cb5-146">Le port est déterminé par l’hôte docker et peut être interrogé avec docker ps.</span><span class="sxs-lookup"><span data-stu-id="68cb5-146">The port is determined by the docker host and can be queried with docker ps.</span></span> 
- <span data-ttu-id="68cb5-147">Votre application est copiée dans le conteneur</span><span class="sxs-lookup"><span data-stu-id="68cb5-147">Your application is copied to the container</span></span>
- <span data-ttu-id="68cb5-148">Le navigateur par défaut est lancé avec le débogueur attaché au conteneur, en utilisant le port affecté dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="68cb5-148">Default browser is launched with the debugger attached to the container, using the dynamically assigned port.</span></span> 

<span data-ttu-id="68cb5-149">L’image Docker générée est l’image de *développement* de votre application avec les images *microsoft/aspnetcore* comme image de base.</span><span class="sxs-lookup"><span data-stu-id="68cb5-149">The resulting Docker image built is the *dev* image of your application with the *microsoft/aspnetcore* images as the base image.</span></span>

<span data-ttu-id="68cb5-150">**Remarque :** L’image de développement n’inclut pas le contenu de votre application car les configurations Debug utilisent le montage de volume pour fournir l’expérience itérative.</span><span class="sxs-lookup"><span data-stu-id="68cb5-150">**Note:** The dev image is empty of your app contents as Debug configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="68cb5-151">Pour placer une image, utilisez la configuration Release.</span><span class="sxs-lookup"><span data-stu-id="68cb5-151">To push an image, use the Release configuration.</span></span>

```console
REPOSITORY                  TAG         IMAGE ID            CREATED         SIZE
spboyer/hellodockertools    dev         0b6e2e44b3df        4 minutes ago   268.9 MB
microsoft/aspnetcore        1.0.1       189ad4312ce7        5 days ago      268.9 MB
```

<span data-ttu-id="68cb5-152">L’application s’exécute en utilisant le conteneur que vous pouvez voir en exécutant la commande `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="68cb5-152">The application is running using the container, which you can see by running the `docker ps` command.</span></span>

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   4 minutes ago       Up 4 minutes        0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="edit-and-continue"></a><span data-ttu-id="68cb5-153">Modifier & Continuer</span><span class="sxs-lookup"><span data-stu-id="68cb5-153">Edit and Continue</span></span>

<span data-ttu-id="68cb5-154">Les modifications apportées à des fichiers statiques et/ou à des fichiers modèles Razor (*.cshtml*) sont automatiquement mises à jour qu’une étape de compilation soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="68cb5-154">Changes to static files and/or razor template files (*.cshtml*) are automatically updated without the need of a compilation step.</span></span> <span data-ttu-id="68cb5-155">Apportez les modifications, enregistrez et cliquez sur Actualiser dans le navigateur pour voir la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="68cb5-155">Make the change, save, and tap refresh in the browser to view the update.</span></span>  

<span data-ttu-id="68cb5-156">Les modifications apportées aux fichiers de code nécessitent une compilation et un redémarrage de Kestrel au sein du conteneur.</span><span class="sxs-lookup"><span data-stu-id="68cb5-156">Modifications to code files require compiling and a restart of Kestrel within the container.</span></span> <span data-ttu-id="68cb5-157">Après avoir effectué la modification, utilisez Ctrl+F5 pour exécuter le processus et démarrer l’application au sein du conteneur.</span><span class="sxs-lookup"><span data-stu-id="68cb5-157">After making the change, use CTRL + F5 to perform the process and start the application within the container.</span></span> <span data-ttu-id="68cb5-158">Le conteneur Docker n’est pas régénéré ni arrêté ; en utilisant `docker ps` sur la ligne de commande, vous pouvez voir que le conteneur d’origine est toujours en cours d’exécution comme 10 minutes auparavant.</span><span class="sxs-lookup"><span data-stu-id="68cb5-158">The Docker container is not rebuilt or stopped; using `docker ps` in the command line, you can see that the original container is still running as of 10 minutes ago.</span></span> 

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   10 minutes ago      Up 10 minutes       0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="publishing-docker-images"></a><span data-ttu-id="68cb5-159">Publication des images Docker</span><span class="sxs-lookup"><span data-stu-id="68cb5-159">Publishing Docker images</span></span>

<span data-ttu-id="68cb5-160">Une fois que vous avez terminé le cycle de développement et de débogage de votre application, Visual Studio Tools pour Docker vous aide à créer l’image de production de votre application.</span><span class="sxs-lookup"><span data-stu-id="68cb5-160">Once you have completed the develop and debug cycle of your application, the Visual Studio Tools for Docker will help you create the production image of your application.</span></span> <span data-ttu-id="68cb5-161">Changez la liste déroulante de Debug en **Release** et générez l’application.</span><span class="sxs-lookup"><span data-stu-id="68cb5-161">Change the debug dropdown to **Release** and build the application.</span></span> <span data-ttu-id="68cb5-162">Les outils produisent l’image avec l’étiquette `:latest`, que vous pouvez distribuer sur votre Registre privé ou sur Docker Hub.</span><span class="sxs-lookup"><span data-stu-id="68cb5-162">The tooling will produce the image with the `:latest` tag which you can push to your private registry or Docker Hub.</span></span> 

<span data-ttu-id="68cb5-163">Avec la commande `docker images`, vous pouvez voir la liste des images.</span><span class="sxs-lookup"><span data-stu-id="68cb5-163">Using the `docker images` command, you can see the list of images.</span></span>

```console
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
spboyer/hellodockertools   latest              8184ae38ba91        5 seconds ago       278.4 MB
spboyer/hellodockertools   dev                 0b6e2e44b3df        About an hour ago   268.9 MB
microsoft/aspnetcore       1.0.1               189ad4312ce7        5 days ago          268.9 MB
```

<span data-ttu-id="68cb5-164">Vous pourriez vous attendre à ce que l’image de production ou de version ait une taille inférieure à celle de l’image de **développement** en raison de l’utilisation du mappage de volume ; le débogueur et l’application ont été en réalité exécutés à partir de votre machine locale et non pas dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="68cb5-164">There may be an expectation for the production or release image to be smaller in size by comparison to the **dev** image; however, through the use of the volume mapping, the debugger and application were actually being run from your local machine and not within the container.</span></span> <span data-ttu-id="68cb5-165">L’image **latest** a packagé tout le code de l’application nécessaire pour l’exécuter sur un ordinateur hôte : le delta correspond donc à la taille du code de votre application.</span><span class="sxs-lookup"><span data-stu-id="68cb5-165">The **latest** image has packaged the entire application code needed to run the application on a host machine, therefore the delta is the size of your application code.</span></span>
