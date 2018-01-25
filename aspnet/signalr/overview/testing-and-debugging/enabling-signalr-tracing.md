---
uid: signalr/overview/testing-and-debugging/enabling-signalr-tracing
title: "L’activation du suivi de SignalR | Documents Microsoft"
author: tfitzmac
description: "Ce document décrit comment activer et configurer le suivi des clients et les serveurs de SignalR. Active le suivi vous permet d’afficher des informations sur les événements de diagnostic..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/08/2014
ms.topic: article
ms.assetid: 30060acb-be3e-4347-996f-3870f0c37829
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/testing-and-debugging/enabling-signalr-tracing
msc.type: authoredcontent
ms.openlocfilehash: ac979acf162084a195bb769f842e77ad2498c7f3
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="enabling-signalr-tracing"></a>L’activation du traçage SignalR
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce document décrit comment activer et configurer le suivi des clients et les serveurs de SignalR. Le suivi vous permet de vous permet d’afficher des informations sur les événements de diagnostic dans votre application SignalR.
> 
> Cette rubrique a été écrit à l’origine par Patrick Fletcher.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET Framework 4.5
> - SignalR version 2
>   
> 
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


Lorsque le traçage est activé, une application SignalR crée des entrées de journal des événements. Vous pouvez enregistrer les événements du client et le serveur. Sur la connexion du serveur de journaux, fournisseur de montée en puissance parallèle et événements de bus de messages de suivi. Les événements de connexion client des journaux de suivi. SignalR 2.1 et versions ultérieures, les journaux de suivi sur le client la totalité du contenu des messages d’appel de concentrateur.

## <a name="contents"></a>Sommaire

- [L’activation du traçage sur le serveur](#server)

    - [Journalisation des événements de serveur dans des fichiers texte](#server_text)
    - [Journalisation des événements de serveur dans le journal des événements](#server_eventlog)
- [L’activation du traçage dans le client .NET (applications de bureau Windows)](#net_client)

    - [Journalisation des événements à la console client de bureau](#desktop_console)
    - [Enregistrement des événements de client de bureau dans un fichier texte](#desktop_text)
- [L’activation du traçage dans les clients Windows Phone 8](#phone)

    - [Journalisation des événements du client Windows Phone à l’interface utilisateur](#phone_ui)
    - [Journalisation des événements du client Windows Phone à la console de débogage](#phone_debug)
- [L’activation du traçage dans le client JavaScript](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a>L’activation du traçage sur le serveur

Vous activez le traçage sur le serveur dans le fichier de configuration de l’application (App.config ou Web.config en fonction du type de projet.) Vous spécifiez les catégories d’événements que vous souhaitez enregistrer. Dans le fichier de configuration, vous spécifiez également s’il faut enregistrer les événements dans un fichier texte, le journal des événements Windows ou un journal personnalisé à l’aide d’une implémentation de [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).

Les catégories d’événements serveur incluent les types suivants de messages :

| Source | Messages |
| --- | --- |
| SignalR.SqlMessageBus | Configuration du fournisseur de Bus de messages SQL montée en puissance parallèle, opération de base de données, d’erreur et les événements de délai d’attente |
| SignalR.ServiceBusMessageBus | Création de rubrique service bus avec montée en puissance fournisseur et abonnement, erreurs et événements de messagerie |
| SignalR.RedisMessageBus | Événements de connexion, déconnexion et d’erreur du fournisseur avec montée en puissance de redis |
| SignalR.ScaleoutMessageBus | Événements de messagerie de montée en puissance parallèle |
| SignalR.Transports.WebSocketTransport | Événements de connexion, déconnexion, de messagerie et d’erreur de transport WebSocket |
| SignalR.Transports.ServerSentEventsTransport | Les événements de connexion, déconnexion, de messagerie et d’erreur de transport ServerSentEvents |
| SignalR.Transports.ForeverFrameTransport | Événements de connexion, déconnexion, de messagerie et d’erreur de transport ForeverFrame |
| SignalR.Transports.LongPollingTransport | Événements de connexion, déconnexion, de messagerie et d’erreur de transport LongPolling |
| SignalR.Transports.TransportHeartBeat | Les événements de connexion et déconnexion keepalive de transport |
| SignalR.ReflectedHubDescriptorProvider | Événements de détection de concentrateur |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a>Journalisation des événements de serveur dans des fichiers texte

Le code suivant montre comment activer le suivi pour chaque catégorie d’événement. Cet exemple configure l’application pour enregistrer des événements dans des fichiers texte.

**Code de serveur XML pour l’activation du suivi**

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

Dans le code ci-dessus, le `SignalRSwitch` entrée spécifie le [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) utilisés pour les événements envoyés dans le journal spécifié. Dans ce cas, il a la valeur `Verbose` ce qui signifie que tous les messages de traçage et de débogage sont enregistrés.

La sortie suivante montre les entrées lors de la `transports.log.txt` fichier pour une application à l’aide du fichier de configuration ci-dessus. Il montre une nouvelle connexion, une connexion supprimée et les événements de pulsation de transport.

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a>Journalisation des événements de serveur dans le journal des événements

Pour enregistrer les événements dans le journal des événements plutôt que dans un fichier texte, modifiez les valeurs pour les entrées dans le `sharedListeners` nœud. Le code suivant montre comment enregistrer les événements de serveur dans le journal des événements :

**Code de serveur XML pour la journalisation des événements dans le journal des événements**

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

Les événements sont consignés dans le journal des applications et sont disponibles via l’Observateur d’événements, comme indiqué ci-dessous :

![Afficher les journaux de SignalR l’Observateur d’événements](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> Le journal des événements, définissez la **TraceLevel** à **erreur** pour que le nombre de messages reste gérable.

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a>L’activation du traçage dans le client .NET (applications de bureau Windows)

Le client .NET peut enregistrer des événements dans la console, un fichier texte, ou dans un journal personnalisé à l’aide d’une implémentation de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).

Pour activer la journalisation dans le client .NET, valeur de la connexion `TraceLevel` propriété un [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) valeur et le `TraceWriter` valide à la propriété [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a>Journalisation des événements à la console client de bureau

Le code c# suivant montre comment enregistrer les événements dans le client .NET dans la console :

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a>Enregistrement des événements de client de bureau dans un fichier texte

Le code c# suivant montre comment enregistrer les événements dans le client .NET dans un fichier texte :

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

La sortie suivante montre les entrées lors de la `ClientLog.txt` fichier pour une application à l’aide du fichier de configuration ci-dessus. Elle indique le client qui se connecte au serveur et le concentrateur d’appeler une méthode client appelé `addMessage`:

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a>L’activation du traçage dans les clients Windows Phone 8

Applications SignalR pour les applications Windows Phone utilisent le même client .NET en tant qu’applications de bureau, mais [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) et l’écriture dans un fichier avec [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) ne sont pas disponibles. Au lieu de cela, vous devez créer une implémentation personnalisée de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) pour le suivi. 

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a>Journalisation des événements du client Windows Phone à l’interface utilisateur

Le [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) inclut un exemple de Windows Phone qui écrit la sortie de trace vers un [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) l’aide d’un [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) appelée implémentation `TextBlockWriter`. Cette classe peut être trouvée dans le **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** projet. Lors de la création d’une instance de `TextBlockWriter`, passez en cours [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx)et un [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) où il créera un [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) à utiliser pour la trace sortie :

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

La sortie de trace est alors écrites vers un nouveau [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) créé dans le [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) vous avez passé dans :

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a>Journalisation des événements du client Windows Phone à la console de débogage

Pour envoyer la sortie de la console de débogage plutôt que l’interface utilisateur, créer une implémentation de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) qui écrit dans la fenêtre de débogage et de les affecter à votre connexion [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) propriété :

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

Les informations de trace sont ensuite écrites dans la fenêtre de débogage dans Visual Studio :

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a>L’activation du traçage dans le client JavaScript

Pour activer la journalisation de côté client sur une connexion, définissez la `logging` propriété sur l’objet de connexion avant d’appeler le `start` méthode pour établir la connexion.

**Code JavaScript de client pour l’activation du suivi de la console du navigateur (avec le proxy généré)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

**Code JavaScript de client pour l’activation du suivi de la console du navigateur (sans le proxy généré)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

Lorsque le traçage est activé, le client JavaScript enregistre les événements dans la console du navigateur. Pour accéder à la console du navigateur, consultez [analyse les Transports](../getting-started/introduction-to-signalr.md#MonitoringTransports).

La capture d’écran suivante montre un client SignalR JavaScript avec le suivi activé. Il montre la connexion et les événements d’appel de concentrateur dans la console du navigateur :

![Événements de traçage SignalR dans la console du navigateur](enabling-signalr-tracing/_static/image4.png)
