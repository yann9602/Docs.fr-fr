---
title: Application de SSL dans une application ASP.NET Core
author: rick-anderson
description: "Montre comment exiger le protocole SSL dans un cœur d’ASP.NET web app"
manager: wpickett
ms.author: riande
ms.date: 07/19/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/enforcing-ssl
ms.openlocfilehash: 2e0a2f4732e574c80ceef8fd21a530a11aef254c
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="enforcing-ssl-in-an-aspnet-core-app"></a>Application de SSL dans une application ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce document montre comment :

- Exiger le protocole SSL pour toutes les demandes (uniquement pour les requêtes HTTPS).
- Rediriger toutes les demandes HTTP vers HTTPS.

## <a name="require-ssl"></a>Exiger SSL

Le [RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) est utilisée pour exiger SSL. Vous pouvez la décorer contrôleurs ou méthodes avec cet attribut, ou vous pouvez l’appliquer globalement, comme indiqué ci-dessous :

Ajoutez le code suivant à `ConfigureServices` dans `Startup`:

[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]

Le code en surbrillance ci-dessus nécessite l’utilisation de toutes les demandes `HTTPS`, par conséquent, les requêtes HTTP sont ignorés. Le code en surbrillance suivant redirige toutes les demandes HTTP vers HTTPS :

[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]

Consultez [intergiciel (middleware) réécriture d’URL](xref:fundamentals/url-rewriting) pour plus d’informations.

Nécessitant HTTPS globalement (`options.Filters.Add(new RequireHttpsAttribute());`) est une meilleure pratique de sécurité. Appliquer le `[RequireHttps]` attribut à tous les contrôleur n’est pas aussi sécurisée comme nécessitant HTTPS globalement. Vous ne pouvez pas garantir la sécurité ajoutées à votre application de nouveaux contrôleurs penser à appliquer le `[RequireHttps]` attribut.
