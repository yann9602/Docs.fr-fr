---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: "À l’aide de SignalR avec les applications Web dans Azure App Service | Documents Microsoft"
author: pfletcher
description: "Ce document décrit comment configurer une application SignalR qui s’exécute sur Microsoft Azure. Versions du logiciel utilisaient dans le didacticiel Visual Studio 2013 ou vis..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/01/2015
ms.topic: article
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: 8386441690a3fb479ffb941ebd7c0b2f83870781
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a>À l’aide de SignalR avec les applications Web dans Azure App Service
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Ce document décrit comment configurer une application SignalR qui s’exécute sur Microsoft Azure.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads) ou Visual Studio 2012
> - .NET 4.5
> - SignalR version 2
> - Azure SDK 2.3 pour Visual Studio 2013 ou 2012
>   
> 
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), ou [forums de Microsoft Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).


## <a name="table-of-contents"></a>Sommaire

- [Introduction](#introduction)
- [Déploiement d’une application Web de SignalR dans Azure App Service](#deploying)
- [L’activation du protocole WebSocket sur Azure App Service](#websocket)
- [À l’aide de l’infrastructure d’intégration du Cache Redis Azure](#backplane)
- [Étapes suivantes](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a>Introduction

ASP.NET SignalR peut servir à mettre un nouveau niveau d’interactivité entre les serveurs et les clients .NET ou web. Lorsqu’il est hébergé dans Azure, des applications SignalR peuvent bénéficier de la haute disponibilité, évolutive, et fournit des performances de l’environnement qui s’exécutent dans le cloud.

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a>Déploiement d’une application Web de SignalR dans Azure App Service

SignalR n’ajoute pas les complications particuliers au déploiement d’une application dans Azure et de déploiement sur un serveur local. Une application qui utilise SignalR peut être hébergée dans Azure sans aucune modification de configuration ou d’autres paramètres (bien que pour la prise en charge de WebSocket, consultez [l’activation du protocole WebSocket sur Azure App Service](#websocket) ci-dessous.) Pour ce didacticiel, vous allez déployer l’application créée dans le [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md) vers Azure.

**Composants requis**

- Visual Studio 2013. Si vous n’avez pas Visual Studio, Visual Studio 2013 Express pour le Web est inclus dans l’installation du SDK Azure.
- [Azure SDK 2.3 pour Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou [Azure SDK 2.3 pour Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).
- Pour effectuer ce didacticiel, vous devez un abonnement Azure. Vous pouvez [activer vos avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), ou [souscrire un abonnement d’évaluation](https://azure.microsoft.com/pricing/free-trial/).

### <a name="deploying-a-signalr-web-app-to-azure"></a>Déploiement d’une application web de SignalR vers Azure

1. Terminer la [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md), ou télécharger le projet terminé à partir de [Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
2. Dans Visual Studio, sélectionnez **générer**, **publier la conversation SignalR**.
3. Dans la boîte de dialogue « Publier le site Web », sélectionnez « Sites Web Windows Azure ».

    ![Sélectionnez les Sites Web Azure](using-signalr-with-azure-web-sites/_static/image1.png)
4. Si vous n’êtes pas connecté à votre compte Microsoft, cliquez sur **connexion...**  dans la boîte de dialogue « Sélectionner un Site Web existant », puis connectez-vous.

    ![Sélectionnez le Site Web existant](using-signalr-with-azure-web-sites/_static/image2.png)    ![Connectez-vous à Azure](using-signalr-with-azure-web-sites/_static/image3.png)
5. Dans la boîte de dialogue « Sélectionner un Site Web existant », cliquez sur **nouveau**.

    ![Nouveau site Web](using-signalr-with-azure-web-sites/_static/image4.png)
6. Dans la boîte de dialogue « Créer le site sur Windows Azure », entrez un nom d’application unique. Dans la liste déroulante région, sélectionnez la région la plus proche pour vous. Cliquez sur **Créer**.

    ![Créer un site sur Azure](using-signalr-with-azure-web-sites/_static/image5.png)
7. Dans la boîte de dialogue « Publier le site Web », cliquez sur **publier**.

    ![publication du site](using-signalr-with-azure-web-sites/_static/image6.png)
8. Lorsque l’application a terminé la publication, l’application de conversation de SignalR hébergée dans Azure App Service Web Apps s’ouvre dans un navigateur.

    ![Site dans un navigateur](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a>L’activation de WebSocket sur Azure App Service Web Apps

WebSockets doit être activé explicitement dans votre application web à utiliser dans une application SignalR ; Sinon, les autres protocoles seront utilisés (consultez [du transport et secours](../getting-started/introduction-to-signalr.md#transports) pour plus d’informations).

Pour pouvoir utiliser le protocole WebSocket sur Azure App Service Web Apps, vous devez l’activer dans la section de configuration de l’application web. Pour ce faire, ouvrez votre application web dans le [portail de gestion Azure](https://manage.windowsazure.com/)et sélectionnez Configurer.

![Onglet Configurer](using-signalr-with-azure-web-sites/_static/image8.png)

En haut de la page de configuration, assurez-vous que .NET 4.5 est utilisée pour votre application web.

![Paramètre de .NET framework version 4.5](using-signalr-with-azure-web-sites/_static/image9.png)

Dans la page de configuration, dans le **WebSockets** paramètre, sélectionnez **sur**.

![Paramètre de protocole WebSocket : sur](using-signalr-with-azure-web-sites/_static/image10.png)

Au bas de la page de Configuration, sélectionnez **enregistrer** pour enregistrer vos modifications.

![Enregistrer les paramètres](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a>À l’aide de l’infrastructure d’intégration du Cache Redis Azure

Si vous utilisez plusieurs instances de votre application web, et que les utilisateurs de ces instances ont besoin d’interagir avec l’autre (de sorte que, par exemple, les messages de la conversation créées dans une instance peuvent atteindre les utilisateurs connectés à d’autres instances), le [du Cache Redis Azure fond de panier](../performance/scaleout-with-redis.md) doit être implémentée dans votre application.

<a id="nextsteps"></a>
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les applications Web dans Azure App Service, consultez [vue d’ensemble des applications Web](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).
