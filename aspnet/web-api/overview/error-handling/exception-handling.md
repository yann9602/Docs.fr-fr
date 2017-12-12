---
uid: web-api/overview/error-handling/exception-handling
title: "Gestion des exceptions dans l’API Web ASP.NET | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/12/2012
ms.topic: article
ms.assetid: cbebeb37-2594-41f2-b71a-f4f26520d512
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/error-handling/exception-handling
msc.type: authoredcontent
ms.openlocfilehash: c65ddcca012840d70ab5a33af92edb30041be971
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="exception-handling-in-aspnet-web-api"></a>Gestion des exceptions dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Cet article décrit l’erreur et gestion des exceptions dans l’API Web ASP.NET.

- [HttpResponseException](#httpresponserexception)
- [Filtres d’exception](#exception_filters)
- [L’inscription des filtres d’Exception](#registering_exception_filters)
- [Erreur HTTP](#httperror)

<a id="httpresponserexception"></a>
## <a name="httpresponseexception"></a>HttpResponseException

Que se passe-t-il si un contrôleur d’API Web lève une exception non interceptée ? Par défaut, la plupart des exceptions sont traduites en une réponse HTTP avec le code d’état 500 Erreur interne au serveur.

Le **HttpResponseException** type est un cas spécial. Cette exception retourne un code d’état HTTP que vous spécifiez dans le constructeur de l’exception. Par exemple, la méthode suivante retourne 404 introuvable, si la *id* paramètre n’est pas valide.

[!code-csharp[Main](exception-handling/samples/sample1.cs)]

Pour mieux contrôler la réponse, vous pouvez également construire le message de réponse entier et l’inclure avec la **HttpResponseException :** 

[!code-csharp[Main](exception-handling/samples/sample2.cs)]

<a id="exception_filters"></a>
## <a name="exception-filters"></a>Filtres d’exception

Vous pouvez personnaliser comment API Web gère les exceptions en écrivant un *filtre d’exception*. Un filtre d’exception est exécuté lorsqu’une méthode de contrôleur lève une exception non gérée qui est *pas* un **HttpResponseException** exception. Le **HttpResponseException** type est un cas spécial, parce qu’il est conçu spécifiquement pour renvoyer une réponse HTTP.

Implémentent des filtres d’exception le **System.Web.Http.Filters.IExceptionFilter** interface. La façon la plus simple pour écrire un filtre d’exception doit dériver de la **System.Web.Http.Filters.ExceptionFilterAttribute** classe et substituer les **OnException** (méthode).

> [!NOTE]
> Filtres d’exception dans l’API Web ASP.NET sont similaires à celles utilisées dans ASP.NET MVC. Toutefois, ils sont déclarés dans un espace de noms distinct et une fonction séparément. En particulier, la **HandleErrorAttribute** classe utilisée dans MVC ne gère pas les exceptions levées par les contrôleurs de l’API Web.


Voici un filtre qui convertit **NotImplementedException** 501, non implémenté de code des exceptions dans l’état HTTP :

[!code-csharp[Main](exception-handling/samples/sample3.cs)]

Le **réponse** propriété de la **HttpActionExecutedContext** objet contient le message de réponse HTTP qui sera envoyé au client.

<a id="registering_exception_filters"></a>
## <a name="registering-exception-filters"></a>L’inscription des filtres d’Exception

Il existe plusieurs façons d’enregistrer un filtre d’exception API Web :

- Par action
- Par le contrôleur
- Global

Pour appliquer le filtre à une action spécifique, ajoutez le filtre en tant qu’attribut de l’action :

[!code-csharp[Main](exception-handling/samples/sample4.cs)]

Pour appliquer le filtre à toutes les actions sur un contrôleur, ajoutez le filtre en tant qu’attribut à la classe de contrôleur :

[!code-csharp[Main](exception-handling/samples/sample5.cs)]

Pour appliquer le filtre globalement à tous les contrôleurs de l’API Web, ajoutez une instance du filtre à la **GlobalConfiguration.Configuration.Filters** collection. Filtres d’exception dans cette collection s’appliquent à toute action de contrôleur d’API Web.

[!code-csharp[Main](exception-handling/samples/sample6.cs)]

Si vous utilisez le modèle de projet « Application Web ASP.NET MVC 4 » pour créer votre projet, placez votre code de configuration de API Web à l’intérieur de la `WebApiConfig` (classe), qui se trouve dans l’application\_dossier de démarrage :

[!code-csharp[Main](exception-handling/samples/sample7.cs?highlight=5)]

<a id="httperror"></a>
## <a name="httperror"></a>Erreur HTTP

Le **HttpError** objet fournit un moyen cohérent de retourner des informations d’erreur dans le corps de réponse. L’exemple suivant montre comment retourner le code d’état HTTP 404 (introuvable) avec une **HttpError** dans le corps de réponse.

[!code-csharp[Main](exception-handling/samples/sample8.cs)]

**CreateErrorResponse** est une méthode d’extension définie dans le **System.Net.Http.HttpRequestMessageExtensions** classe. En interne, **CreateErrorResponse** crée un **HttpError** de l’instance, puis crée un **HttpResponseMessage** qui contient le **HttpError**.

Dans cet exemple, si la méthode réussit, elle retourne le produit dans la réponse HTTP. Mais si le produit demandé est introuvable, la réponse HTTP contient un **HttpError** dans le corps de la demande. La réponse peut se présenter comme suit :

[!code-console[Main](exception-handling/samples/sample9.cmd)]

Notez que la **HttpError** a été sérialisé en JSON dans cet exemple. L’un des avantages de l’utilisation de **HttpError** est qu’il passe par le même [négociation de contenu](../formats-and-model-binding/content-negotiation.md) et processus de sérialisation que tout autre modèle fortement typé.

### <a name="httperror-and-model-validation"></a>Erreur HTTP et la Validation du modèle

Pour la validation de modèle, vous pouvez passer à l’état du modèle **CreateErrorResponse**, pour inclure les erreurs de validation dans la réponse :

[!code-csharp[Main](exception-handling/samples/sample10.cs)]

Cet exemple peut retourner la réponse suivante :

[!code-console[Main](exception-handling/samples/sample11.cmd)]

Pour plus d’informations sur la validation des modèles, consultez [Validation de modèle dans ASP.NET Web API](../formats-and-model-binding/model-validation-in-aspnet-web-api.md).

### <a name="using-httperror-with-httpresponseexception"></a>À l’aide d’erreur HTTP avec HttpResponseException

Les exemples précédents retournent un **HttpResponseMessage** message à partir de l’action du contrôleur, mais vous pouvez également utiliser **HttpResponseException** pour retourner une **HttpError**. Cela vous permet de retourner un modèle fortement typé dans le cas de réussite normal, tout en continuant à renvoyer **HttpError** s’il existe une erreur :

[!code-csharp[Main](exception-handling/samples/sample12.cs)]
