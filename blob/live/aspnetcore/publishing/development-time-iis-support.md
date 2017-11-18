---
title: "Prise en charge d’IIS pendant le développement dans Visual Studio pour ASP.NET Core"
author: shirhatti
description: "Découvrez la prise en charge du débogage des applications ASP.NET Core lors de l’exécution derrière IIS sur Windows Server."
keywords: "ASP.NET Core,internet information services,iis,windows server,module asp.net core,débogage"
ms.author: riande
manager: wpickett
ms.date: 09/13/2017
ms.topic: article
ms.assetid: 83d98477-9d10-4a78-a54a-f325ad67d13b
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/development-time-iis-support
ms.openlocfilehash: a35a6fd9896c4c110d1b6680b6aaf718d29a18ab
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="a065a-104">Prise en charge d’IIS pendant le développement dans Visual Studio pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-104">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="a065a-105">Par : [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="a065a-105">By: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="a065a-106">Cet article décrit la prise en charge de [Visual Studio](https://www.visualstudio.com/vs/) pour le débogage des applications ASP.NET Core s’exécutant derrière IIS sur Windows Server.</span><span class="sxs-lookup"><span data-stu-id="a065a-106">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core applications running behind IIS on Windows Server.</span></span> <span data-ttu-id="a065a-107">Cette rubrique présente l’activation de cette fonctionnalité et la configuration de votre projet.</span><span class="sxs-lookup"><span data-stu-id="a065a-107">This topic walks you through enabling this feature and setting up your project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a065a-108">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="a065a-108">Prerequisites</span></span>

* <span data-ttu-id="a065a-109">Visual Studio (2017/version 15.3 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="a065a-109">Visual Studio (2017/version 15.3 or later)</span></span>
* <span data-ttu-id="a065a-110">Charge de travail de développement ASP.NET et web *OU* la charge de travail de développement multiplateforme .NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-110">ASP.NET and web development workload *OR* the .NET Core cross-platform development workload</span></span>

## <a name="enable-iis"></a><span data-ttu-id="a065a-111">Activer IIS</span><span class="sxs-lookup"><span data-stu-id="a065a-111">Enable IIS</span></span>

<span data-ttu-id="a065a-112">Activez IIS sur votre système.</span><span class="sxs-lookup"><span data-stu-id="a065a-112">Enable IIS on your system.</span></span> <span data-ttu-id="a065a-113">Accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="a065a-113">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="a065a-114">Cochez la case **Services IIS (Internet Information Services)**.</span><span class="sxs-lookup"><span data-stu-id="a065a-114">Select the **Internet Information Services** checkbox.</span></span>

![Fonctionnalités Windows montrant la case à cocher Services IIS (Internet Information Services) avec un carré noir (pas une coche) indiquant que certaines des fonctionnalités IIS sont activées](development-time-iis-support/_static/enable_iis.png)

<span data-ttu-id="a065a-116">Si votre installation d’IIS nécessite un redémarrage, redémarrez votre système.</span><span class="sxs-lookup"><span data-stu-id="a065a-116">If your IIS installation requires a reboot, reboot your system.</span></span>

## <a name="enable-development-time-iis-support"></a><span data-ttu-id="a065a-117">Activer la prise en charge d’IIS lors du développement</span><span class="sxs-lookup"><span data-stu-id="a065a-117">Enable development-time IIS support</span></span>

<span data-ttu-id="a065a-118">Une fois que vous avez installé IIS, lancez le programme d’installation de Visual Studio pour modifier votre installation existante de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a065a-118">Once you've installed IIS, launch the Visual Studio installer to modify your existing Visual Studio installation.</span></span> <span data-ttu-id="a065a-119">Dans le programme d’installation, sélectionnez le composant **Prise en charge d’IIS pendant le développement**.</span><span class="sxs-lookup"><span data-stu-id="a065a-119">In the installer, select the **Development time IIS support** component.</span></span> <span data-ttu-id="a065a-120">Le composant apparaît comme composant facultatif dans le panneau **Résumé** pour la charge de travail **Développement web et ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="a065a-120">The component is listed as an optional component in the **Summary** panel for the **ASP.NET and web development** workload.</span></span> <span data-ttu-id="a065a-121">Ceci installe le [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), qui est un module IIS natif nécessaire pour exécuter des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a065a-121">This installs the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module), which is a native IIS module required to run ASP.NET Core applications.</span></span>

![Modification des fonctionnalités de Visual Studio : l’onglet Charges de travail est sélectionné.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a><span data-ttu-id="a065a-125">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="a065a-125">Configure the project</span></span>

<span data-ttu-id="a065a-126">Créez un nouveau profil de lancement pour ajouter la prise en charge d’IIS lors du développement.</span><span class="sxs-lookup"><span data-stu-id="a065a-126">Create a new launch profile to add development-time IIS support.</span></span> <span data-ttu-id="a065a-127">Dans **l’Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="a065a-127">In Visual Studio's **Solution Explorer**, right-click the project and select **Properties**.</span></span> <span data-ttu-id="a065a-128">Sélectionnez l’onglet **Débogage**. Sélectionnez **IIS** dans la liste déroulante **Lancer**.</span><span class="sxs-lookup"><span data-stu-id="a065a-128">Select the **Debug** tab. Select **IIS** from the **Launch** dropdown.</span></span> <span data-ttu-id="a065a-129">Vérifiez que la fonctionnalité **Lancer le navigateur** est activée avec l’URL correcte.</span><span class="sxs-lookup"><span data-stu-id="a065a-129">Confirm that the **Launch browser** feature is enabled with the correct URL.</span></span>

![Fenêtre de propriétés du projet avec l’onglet Débogage sélectionné.](development-time-iis-support/_static/project_properties.png)

<span data-ttu-id="a065a-134">Vous pouvez aussi ajouter manuellement un profil de lancement pour votre fichier [launchSettings.json](http://json.schemastore.org/launchsettings) dans l’application :</span><span class="sxs-lookup"><span data-stu-id="a065a-134">Alternatively, you can manually add a launch profile to your [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:</span></span>

```json
{
    "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iis": {
            "applicationUrl": "http://localhost/WebApplication2",
            "sslPort": 0
        }
    },
    "profiles": {
        "IIS": {
            "commandName": "IIS",
            "launchBrowser": "true",
            "launchUrl": "http://localhost/WebApplication2",
            "environmentVariables": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    }
}
```

<span data-ttu-id="a065a-135">Vous pouvez être invité à redémarrer Visual Studio si vous ne l’exécutiez pas en tant qu’administrateur.</span><span class="sxs-lookup"><span data-stu-id="a065a-135">You may be prompted to restart Visual Studio if you weren't running as an administrator.</span></span> <span data-ttu-id="a065a-136">Si vous y êtes invité, redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a065a-136">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="a065a-137">Félicitations !</span><span class="sxs-lookup"><span data-stu-id="a065a-137">Congratulations!</span></span> <span data-ttu-id="a065a-138">À ce stade, votre projet est configuré pour la prise en charge d’IIS pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="a065a-138">At this point, your project is configured for development-time IIS support.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="a065a-139">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a065a-139">Additional resources</span></span>

* [<span data-ttu-id="a065a-140">Héberger ASP.NET Core sur Windows avec IIS</span><span class="sxs-lookup"><span data-stu-id="a065a-140">Host ASP.NET Core on Windows with IIS</span></span>](xref:publishing/iis)
* [<span data-ttu-id="a065a-141">Introduction au Module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-141">Introduction to ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)
* [<span data-ttu-id="a065a-142">Informations de référence sur la configuration du Module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-142">ASP.NET Core Module configuration reference</span></span>](xref:hosting/aspnet-core-module)
