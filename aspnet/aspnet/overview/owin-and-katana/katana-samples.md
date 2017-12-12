---
uid: aspnet/overview/owin-and-katana/katana-samples
title: Exemples de Katana | Documents Microsoft
author: microsoft
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2014
ms.topic: article
ms.assetid: bec04f5d-2638-4417-b288-97c58c8d6379
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/owin-and-katana/katana-samples
msc.type: authoredcontent
ms.openlocfilehash: 5540164bda8db31c4e78b49ecb7f7c573acca013
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="katana-samples"></a>Exemples de Katana
====================
par [Microsoft](https://github.com/microsoft)

## <a name="katana-samples"></a>Exemples de Katana

**ASP.NET achemine exemple** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/AspNetRoutes/ReadMe.txt)  
Dans certaines applications, vous devez connecter les composants OWIN dans la table d’itinéraires Asp.Net côte à côte avec les composants non-OWIN. Cet exemple montre comment utiliser les méthodes d’extension RouteCollection MapOwinPath et MapOwinRoute fournie par Microsoft.Owin.Host.SystemWeb.

**Créer une branche de Pipelines exemple** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/BranchingPipelines/ReadMe.txt)  
Pipelines de traitement de la demande OWIN n’avez pas besoin d’être linéaire, ils peuvent être connecté pour traiter les demandes de différentes façons. Cet exemple montre comment construire un pipeline de branchement basé sur les chemins d’accès de la demande ou d’autres données de la demande tels que les en-têtes. Ces composants sont disponibles dans le package nuget Microsoft.Owin.Mapping.

**Exemple de serveur personnalisé** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/CustomServer/MyCustomServer/CustomServer.cs)   
Montre comment utiliser un serveur OWIN personnalisé lorsque l’auto-hébergement OWIN.

**Incorporé exemple** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/Embedded/ReadMe.txt)  
Certains serveurs OWIN peuvent être exécutés à l’intérieur de votre propre processus (&quot;auto-hébergé&quot;). Cet exemple montre comment démarrer une application OWIN à l’aide des outils fournis par le package nuget Microsoft.Owin.Hosting.

**Exemple HelloWorld** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/HelloWorld/ReadMe.txt)  
OWIN est un abstraction d’API qui permet la portabilité des applications sur différents serveurs du serveur HTTP. Cet exemple montre comment écrire une application Hello World utilisant certaines **wrappers simples** autour de l’abstraction de OWIN brutes et les exécuter sur un serveur web telles que ASP.NET.

**Hello World (exemple) brut OWIN** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/HelloWorldRawOwin/ReadMe.txt)  
Cet exemple montre comment écrire une application Hello World utilisant le **brutes** abstraction de OWIN et l’exécuter sur un serveur web comme Asp.Net.

**Exemple de SignalR** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/SignalR/Program.cs)  
Montre comment l’auto-hébergement SignalR à l’aide de OWIN / Katana. Pour plus d’informations sur SignalR d’auto-hébergement, consultez [didacticiel : SignalR auto-hébergement](../../../signalr/overview/deployment/tutorial-signalr-self-host.md).

**Exemple de fichiers statiques** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/StaticFilesSample/Startup.cs)   
Montre comment prendre en charge les requêtes HTTP pour les fichiers statiques à l’aide de OWIN / Katana.

**Web API** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/WebApi/ReadMe.txt)   
Cet exemple montre comment héberger OWIN dans IIS et ajouter des API Web au pipeline OWIN.

**Exemple de Socket de Web** | [Code Source](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/Katana/WebSocketSample/WebSocketServer/Startup.cs)   
Montre comment prendre en charge de WebSocket dans OWIN à l’aide de la [System.Net.WebSockets.WebSocket](https://msdn.microsoft.com/en-us/library/system.net.websockets.websocket(v=vs.110).aspx) classe.
