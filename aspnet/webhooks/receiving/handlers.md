---
uid: webhooks/receiving/handlers
title: "Gestionnaires d’ASP.NET WebHooks | Documents Microsoft"
author: rick-anderson
description: "Comment gérer les demandes de WebHooks de ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: a55b0d20-9c90-4bd3-a471-20da6f569f0c
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 4cf5770a731ef77842eb29b0a66ee0aac5d85d85
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="aspnet-webhooks-handlers"></a>Gestionnaires d’ASP.NET WebHooks

Une fois que les demandes de WebHooks a été validée par un récepteur de WebHook, il est prêt à être traité par le code utilisateur. C’est là *gestionnaires* sont disponibles dans. Gestionnaires dérivent la [IWebHookHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandler.cs) interface, mais généralement utilise le [WebHookHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandler.cs) classe au lieu de dériver directement à partir de l’interface.

Une demande de WebHook peut être traitée par un ou plusieurs gestionnaires. Gestionnaires sont appelés dans l’ordre en fonction de leurs détenteurs respectifs *commande* propriété allant de la plus basse à la plus élevée dont est un entier simple (suggéré pour être compris entre 1 et 100) :

![Schéma de propriété WebHook Gestionnaire ordre](_static/Handlers.png)

Un gestionnaire peut éventuellement définir le *réponse* propriété sur le WebHookHandlerContext, ce qui entraînera le traitement à l’arrêt et la réponse à envoyer comme la réponse HTTP pour le WebHook. Dans le cas ci-dessus, gestionnaire C ne sera pas appelée, car il a un ordre plus élevé que B et B définit la réponse.

Définition de la réponse est généralement uniquement pertinente pour WebHooks où la réponse peut contenir des informations sur l’API d’origine. Il s’agit par exemple le cas avec le WebHooks Slack où la réponse est publiée sur le canal d'où provenance le WebHook. Les gestionnaires peuvent définir la propriété de récepteur s’ils souhaitent uniquement recevoir WebHooks ce destinataire particulier. Si elles ne définissent pas le récepteur, ils sont appelés pour chacun d’eux.

Une autre utilisation courante d’une réponse consiste à utiliser un *Gone 410* réponse pour indiquer que le WebHook n’est plus actif et aucune demande supplémentaire ne doit être soumis.

Par défaut, un gestionnaire sera appelé par tous les destinataires de WebHook. Toutefois, si le *récepteur* est définie sur le nom d’un gestionnaire, puis ce gestionnaire reçoive uniquement les demandes de WebHook à partir de ce dernier.

## <a name="processing-a-webhook"></a>Le traitement d’un WebHook

Lorsqu’un gestionnaire est appelé, il obtient un [WebHookHandlerContext](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandlerContext.cs) contenant des informations sur la demande de WebHook. Les données, en général, le corps de demande HTTP, sont disponibles à partir de la *données* propriété.

Le type de données est généralement les données de formulaire JSON ou HTML, mais il est possible d’effectuer un cast en un type plus spécifique si vous le souhaitez. Par exemple, le WebHooks personnalisées générées par des WebHooks de ASP.NET peut être converti en type [CustomNotifications](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers.Custom/WebHooks/CustomNotifications.cs) comme suit :

```csharp
public class MyWebHookHandler : WebHookHandler
{
    public MyWebHookHandler()
    {
        this.Receiver = "custom";
    }

    public override Task ExecuteAsync(string generator, WebHookHandlerContext context)
    {
        CustomNotifications notifications = context.GetDataOrDefault<CustomNotifications>();
        foreach (var notification in notifications.Notifications)
        {
            ...
        }
        return Task.FromResult(true);
    }
}
```

  ## <a name="queued-processing"></a>Traitement en file d’attente

La plupart des expéditeurs de WebHook renverra un WebHook si une réponse n’est pas générée dans un certain nombre de secondes. Cela signifie que votre gestionnaire doit effectuer le traitement dans ce laps de temps dans l’ordre, pas pour pouvoir être appelée à nouveau.

Si le traitement prend plus de temps, ou il est mieux géré séparément le [WebHookQueueHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookQueueHandler.cs) peut être utilisé pour envoyer la demande de WebHook dans une file d’attente, par exemple [file d’attente de stockage Azure](https://msdn.microsoft.com/library/azure/dd179353.aspx).

Un plan d’un [WebHookQueueHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookQueueHandler.cs) implémentation est fournie ici :

```csharp
public class QueueHandler : WebHookQueueHandler
{
    public override Task EnqueueAsync(WebHookQueueContext context)
    {

        // Enqueue WebHookQueueContext to your queuing system of choice

        return Task.FromResult(true);
    }
}
```
