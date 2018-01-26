---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: "À l’aide des API Web 2 avec Entity Framework 6 | Documents Microsoft"
author: MikeWasson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application web avec une API Web ASP.NET back-end. Ce didacticiel utilise Entity Framework 6 pour la disposition de données..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: cceefa128f90b4c3e23dd31119f44e6ffc55f46f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="using-web-api-2-with-entity-framework-6"></a>À l’aide des API Web 2 avec Entity Framework 6
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application web avec une API Web ASP.NET back-end. Le didacticiel utilise Entity Framework 6 pour la couche données et Knockout.js pour l’application JavaScript côté client. Le didacticiel montre également comment déployer l’application sur Azure App Service Web Apps.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Web API 2.1
> - [Visual Studio 2013 Update 2](https://www.visualstudio.com/downloads/download-visual-studio-vs)
> - Entity Framework 6
> - .NET 4.5
> - [Knockout.js](http://knockoutjs.com/) 3.1


Ce didacticiel utilise ASP.NET Web API 2 avec Entity Framework 6 pour créer une application web qui manipule une base de données principale. Voici une capture d’écran de l’application que vous allez créer.

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

L’application utilise une conception d’application de page unique (SPA). « Seule page application » est le terme général pour une application web qui charge une page HTML unique, puis met à jour la page dynamiquement, au lieu de charger les nouvelles pages. Après le chargement de page initial, l’application communique avec le serveur via les requêtes AJAX. Le AJAX demande renvoyer les données JSON, que l’application utilise pour mettre à jour l’interface utilisateur.

AJAX n’est pas nouveau, mais aujourd'hui des infrastructures JavaScript qui le rendent plus facile créer et gérer une grande application SPA sophistiquée. Ce didacticiel utilise [Knockout.js](http://knockoutjs.com/), mais vous pouvez utiliser n’importe quelle infrastructure de client JavaScript.

Voici les principaux blocs de construction pour cette application :

- ASP.NET MVC crée la page HTML.
- API Web ASP.NET gère les requêtes AJAX et retourne les données JSON.
- Knockout.js-lie les éléments HTML pour les données JSON.
- Entity Framework communique avec la base de données.

## <a name="see-this-app-running-on-azure"></a>Consultez cette application en cours d’exécution sur Azure

Vous voulez voir le site terminé en cours d’exécution comme une application web dynamique ? Vous pouvez déployer une version complète de l’application à votre compte Azure en cliquant simplement sur le bouton suivant.

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

Vous avez besoin d’un compte Azure pour déployer cette solution sur Azure. Si vous n’avez pas déjà un compte, vous disposez des options suivantes :

- [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) -vous obtenez des crédits que vous pouvez utiliser pour tester les services Azure payants et même après qu’ils soient utilisés jusqu'à, vous pouvez conserver le compte et libérer de l’utilisation des services Azure.
- [Activer les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -votre abonnement MSDN vous donne des crédits chaque mois que vous pouvez utiliser pour les services Azure payants.

## <a name="create-the-project"></a>Créer le projet

Ouvrez Visual Studio. À partir de la **fichier** menu, sélectionnez **nouveau**, puis sélectionnez **projet**. (Ou cliquez sur **nouveau projet** sur la page de démarrage.)

Dans le **nouveau projet** boîte de dialogue, cliquez sur **Web** dans le volet gauche et **Application Web ASP.NET** dans le volet central. Nommez le projet BookService et cliquez sur **OK**.

[![](part-1/_static/image4.png)](part-1/_static/image3.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **API Web** modèle.

[![](part-1/_static/image6.png)](part-1/_static/image5.png)

Si vous souhaitez héberger le projet dans un Service d’applications Azure, laissez le **hôte dans le cloud** case activée.

Cliquez sur **OK** pour créer le projet.

## <a name="configure-azure-settings-optional"></a>Configurer les paramètres Azure (facultatifs)

Si vous avez laissé le **hôte du Cloud** option activée, Visual Studio vous invite à vous connecter à Microsoft Azure

[![](part-1/_static/image8.png)](part-1/_static/image7.png)

Après que vous être connecté à Azure, Visual Studio vous invite à configurer l’application web. Entrez un nom pour le site de votre abonnement Azure et sélectionnez une région géographique. Sous **serveur de base de données**, sélectionnez **créer un nouveau serveur**. Entrez un nom d’utilisateur administrateur et le mot de passe.

[![](part-1/_static/image10.png)](part-1/_static/image9.png)

>[!div class="step-by-step"]
[Next](part-2.md)
