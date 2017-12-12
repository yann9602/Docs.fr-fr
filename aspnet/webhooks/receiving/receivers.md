---
uid: webhooks/receiving/receivers
title: "Récepteurs d’ASP.NET WebHooks | Documents Microsoft"
author: rick-anderson
description: "Récepteurs d’ASP.NET WebHooks"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 6cdea089-15b2-4732-8c68-921ca561a8f1
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 8c42db4056dd7a6ef77c7bcbc0eca3b5bf7c87e9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-webhooks-receivers"></a>Récepteurs d’ASP.NET WebHooks

Réception de WebHooks dépend de qui est l’expéditeur. Il existe parfois des étapes supplémentaires que l’inscription d’un WebHook vérifier que l’abonné est réellement à l’écoute. Certains WebHooks fournissent un modèle de pousser à tirer où la requête HTTP POST contient uniquement une référence pour les informations d’événement qui est ensuite être récupérés séparément. Fréquence à laquelle le modèle de sécurité varie légèrement.

L’objectif de Microsoft ASP.NET WebHooks est plus simple et plus cohérente pour rattacher votre API sans perdre beaucoup de temps à déterminer comment gérer une variante particulière de WebHooks.

Un récepteur de WebHook est responsable de l’acceptation et la vérification de WebHooks à partir d’un expéditeur particulier. Un récepteur de WebHook peut prendre en charge un nombre quelconque de WebHooks, chacune avec sa propre configuration. Par exemple, le récepteur GitHub WebHook peut accepter des WebHooks à partir de n’importe quel nombre de dépôts GitHub.

## <a name="webhook-receiver-uris"></a>URI du récepteur WebHook

En installant Microsoft ASP.NET WebHooks, vous obtenez un contrôleur de WebHook général qui accepte les requêtes de WebHook à partir d’un nombre très flexible de services. Lorsqu’une demande arrive, il choisit le récepteur approprié que vous avez installés pour gérer un expéditeur WebHook.

L’URI de ce contrôleur est l’URI du WebHook qui vous inscrivez auprès du service et du formulaire :

```
https://<host>/api/webhooks/incoming/<receiver>/{id}
```

Pour des raisons de sécurité, les différents destinataires WebHook nécessitent que l’URI est un *https* URI et dans certains cas, il doit également contenir un paramètre de requête supplémentaire qui est utilisé pour garantir que seul le destinataire prévu peut envoyer des WebHooks à l’URI ci-dessus .

Le  *<receiver>*  composant est le nom du destinataire, par exemple *github* ou *slack*.

Le *{id}* est un identificateur facultatif qui peut servir à identifier une configuration de récepteur WebHook particulière. Cela peut servir à inscrire le WebHooks N avec un récepteur spécifique. Par exemple, les URI suivants de trois utilisable pour vous inscrire à trois WebHooks indépendants :

```
https://<host>/api/webhooks/incoming/github
https://<host>/api/webhooks/incoming/github/12345
https://<host>/api/webhooks/incoming/github/54321
```

## <a name="installing-a-webhook-receiver"></a>L’installation d’un récepteur de WebHook

Pour recevoir le WebHooks à l’aide de Microsoft ASP.NET WebHooks, vous d’abord installez le package Nuget pour l’ou les fournisseurs de que vous souhaitez recevoir des WebHooks à partir WebHook. Les packages Nuget sont nommés [Microsoft.AspNet.WebHooks.Receivers.*](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Receivers) où la dernière partie indique le service de prise en charge. Exemple :

[Microsoft.AspNet.WebHooks.Receivers.GitHub](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Receivers.GitHub) fournit la prise en charge pour la réception de WebHooks à partir de GitHub et [Microsoft.AspNet.WebHooks.Receivers.Custom](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Receivers.Custom) fournit la prise en charge pour la réception des WebHooks généré par ASP. NET WebHooks.

Prêtes à l’emploi, vous pouvez trouver la prise en charge pour l’échange, GitHub, MailChimp, PayPal, Pusher, force de vente, la marge, Stripe, Trello et WordPress, mais il est possible de prendre en charge de n’importe quel nombre d’autres fournisseurs.

## <a name="configuring-a-webhook-receiver"></a>Configuration d’un récepteur de WebHook

Les récepteurs de WebHook sont configurés via la [IWebHookReceiverConfig](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/IWebHookReceiverConfig.cs) l’interface et les implémentations de cette interface particuliers peuvent être inscrit à l’aide de n’importe quel modèle d’injection de dépendance. L’implémentation par défaut utilise les paramètres d’Application qui peut être défini dans le fichier Web.config, ou, si vous utilisez des applications Web Azure, peut être défini via la [Azure Portal](https://portal.azure.com/).

![Paramètres de l’application Azure](_static/AzureAppSettings.png)

Le format pour les clés de paramètre d’Application est la suivante :

```
MS_WebHookReceiverSecret_<receiver>
```

La valeur est une liste séparée par des virgules des valeurs correspondant à la *{id}* valeurs pour lesquelles WebHooks ont été enregistrées, par exemple :

```
MS_WebHookReceiverSecret_GitHub = <secret1>, 12345=<secret2>, 54321=<secret3>
```

## <a name="initializing-a-webhook-receiver"></a>L’initialisation d’un récepteur de WebHook

Les récepteurs de WebHook sont initialisés en les enregistrant, généralement dans le *WebApiConfig* classe statique, par exemple :

```csharp
namespace WebHookReceivers
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            // Load receivers
            config.InitializeReceiveGitHubWebHooks();
        }
    }
}
```
