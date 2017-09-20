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
ms.sourcegitcommit: f531d90646b9d261c5fbbffcecd6ded9185ae292
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2017
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Prise en charge d’IIS pendant le développement dans Visual Studio pour ASP.NET Core

Par : [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Cet article décrit la prise en charge de [Visual Studio](https://www.visualstudio.com/vs/) pour le débogage des applications ASP.NET Core s’exécutant derrière IIS sur Windows Server. Cette rubrique présente l’activation de cette fonctionnalité et la configuration de votre projet.

## <a name="prerequisites"></a>Prérequis

* Visual Studio (2017/version 15.3 ou ultérieure)
* Charge de travail de développement ASP.NET et web *OU* la charge de travail de développement multiplateforme .NET Core

## <a name="enable-iis"></a>Activer IIS

Activez IIS sur votre système. Accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran). Cochez la case **Services IIS (Internet Information Services)**.

![Fonctionnalités Windows montrant la case à cocher Services IIS (Internet Information Services) avec un carré noir (pas une coche) indiquant que certaines des fonctionnalités IIS sont activées](development-time-iis-support/_static/enable_iis.png)

Si votre installation d’IIS nécessite un redémarrage, redémarrez votre système.

## <a name="enable-development-time-iis-support"></a>Activer la prise en charge d’IIS lors du développement

Une fois que vous avez installé IIS, lancez le programme d’installation de Visual Studio pour modifier votre installation existante de Visual Studio. Dans le programme d’installation, sélectionnez le composant **Prise en charge d’IIS pendant le développement**. Le composant apparaît comme composant facultatif dans le panneau **Résumé** pour la charge de travail **Développement web et ASP.NET**. Ceci installe le [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), qui est un module IIS natif nécessaire pour exécuter des applications ASP.NET Core.

![Modification des fonctionnalités de Visual Studio : l’onglet Charges de travail est sélectionné. Dans la section Web et cloud, le panneau Développement web et ASP.NET est sélectionné. Sur la droite, dans la zone Facultatif du panneau Résumé, vous voyez une case à cocher Prise en charge d’IIS pendant le développement.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a>Configurer le projet

Créez un nouveau profil de lancement pour ajouter la prise en charge d’IIS lors du développement. Dans **l’Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**. Sélectionnez l’onglet **Débogage**. Sélectionnez **IIS** dans la liste déroulante **Lancer**. Vérifiez que la fonctionnalité **Lancer le navigateur** est activée avec l’URL correcte.

![Fenêtre de propriétés du projet avec l’onglet Débogage sélectionné. Les paramètres de profil et de lancement sont définis sur IIS. La fonctionnalité Lancer le navigateur est activée avec l’adresse http://localhost/WebApplication2. La même adresse est également fournie dans le champ URL de l’application de la zone Paramètres du serveur web avec Activer l’authentification anonyme activé.](development-time-iis-support/_static/project_properties.png)

Vous pouvez aussi ajouter manuellement un profil de lancement pour votre fichier [launchSettings.json](http://json.schemastore.org/launchsettings) dans l’application :

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

Vous pouvez être invité à redémarrer Visual Studio si vous ne l’exécutiez pas en tant qu’administrateur. Si vous y êtes invité, redémarrez Visual Studio.

Félicitations ! À ce stade, votre projet est configuré pour la prise en charge d’IIS pendant le développement. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Héberger ASP.NET Core sur Windows avec IIS](xref:publishing/iis)
* [Introduction au Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)
* [Informations de référence sur la configuration du Module ASP.NET Core](xref:hosting/aspnet-core-module)
