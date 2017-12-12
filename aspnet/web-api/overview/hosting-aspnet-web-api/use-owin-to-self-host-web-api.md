---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: "Utiliser OWIN pour l’auto-hébergement ASP.NET Web API 2 | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel montre comment héberger des API Web ASP.NET dans une application console, à l’aide de OWIN pour l’auto-hébergement l’infrastructure API Web. Ouvrez l’Interface Web pour .NET (OWIN) d..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/09/2013
ms.topic: article
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: fda0db8155c3303907331a690af35f619b589154
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="use-owin-to-self-host-aspnet-web-api-2"></a>Utiliser OWIN pour l’auto-hébergement API Web ASP.NET 2
====================
par [Kanchan Mehrotra](https://twitter.com/kanchanmeh)

> Ce didacticiel montre comment héberger des API Web ASP.NET dans une application console, à l’aide de OWIN pour l’auto-hébergement l’infrastructure API Web.
> 
> [Ouvrir l’Interface Web pour .NET](http://owin.org) (OWIN) définit une abstraction entre les serveurs web .NET et des applications web. OWIN dissocie l’application web à partir du serveur, ce qui rend OWIN idéale pour l’auto-hébergement une application web dans votre propre processus, en dehors d’IIS.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads) (fonctionne également avec Visual Studio 2012)
> - API Web 2


> [!NOTE]
> Vous pouvez trouver le code source complet pour ce didacticiel à [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).


## <a name="create-a-console-application"></a>Créez une Application Console

Sur le **fichier** menu, cliquez sur **nouveau**, puis cliquez sur **projet**. À partir de **modèles installés**, sous Visual c#, cliquez sur **Windows** puis cliquez sur **Application Console**. Nommez le projet « OwinSelfhostSample » et cliquez sur **OK**.

[![](use-owin-to-self-host-web-api/_static/image2.png)](use-owin-to-self-host-web-api/_static/image1.png)

## <a name="add-the-web-api-and-owin-packages"></a>Ajouter les API Web et les Packages OWIN

À partir de la **outils** menu, cliquez sur **Gestionnaire de Package de bibliothèque**, puis cliquez sur **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

Cela installe le package selfhost WebAPI OWIN et tous les packages OWIN requis.

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a>Configurer les API Web pour auto-hébergement

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet et sélectionnez **ajouter** / **classe** pour ajouter une nouvelle classe. Nommez la classe `Startup`.

![](use-owin-to-self-host-web-api/_static/image5.png)

Remplacez tout le code réutilisable dans ce fichier avec les éléments suivants :

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a>Ajouter un contrôleur d’API Web

Ensuite, ajoutez une classe de contrôleur d’API Web. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet et sélectionnez **ajouter** / **classe** pour ajouter une nouvelle classe. Nommez la classe `ValuesController`.

Remplacez tout le code réutilisable dans ce fichier avec les éléments suivants :

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-using-httpclient"></a>Démarrer l’hôte OWIN et effectuer une demande à l’aide du client HTTP

Remplacez tout le code réutilisable dans le fichier Program.cs par le suivant :

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="running-the-application"></a>Exécution de l'application

Pour exécuter l’application, appuyez sur F5 dans Visual Studio. La sortie doit se présenter comme suit :

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a>Ressources supplémentaires

[Une vue d’ensemble du projet Katana](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[Héberger des API Web ASP.NET dans un rôle de travail Azure](host-aspnet-web-api-in-an-azure-worker-role.md)
